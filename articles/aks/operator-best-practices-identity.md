---
title: 'Procedimientos recomendados del operador: identidad en Azure Kubernetes Service (AKS)'
description: Obtenga información sobre los procedimientos recomendados del operador de clústeres para saber cómo administrar la autenticación y autorización de los clústeres en Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 201fef6b3e773daa18ae252d1d5734d8d87419b5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287135"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Procedimientos recomendados para la autenticación y autorización en Azure Kubernetes Service (AKS)

A medida que implementa y mantiene clústeres en Azure Kubernetes Service (AKS), deberá implementar maneras de administrar el acceso a los recursos y servicios. Sin estos controles, las cuentas pueden tener acceso a recursos y servicios que no necesitan. También puede ser difícil realizar un seguimiento del conjunto de credenciales que se usaron para realizar cambios.

Este artículo de procedimientos recomendados se centra en cómo un operador de clústeres puede administrar el acceso y la identidad de los clústeres de AKS. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Autenticar a los usuarios de clústeres de AKS con Azure Active Directory.
> * Controlar el acceso a los recursos con el control de acceso basado en rol (RBAC).
> * Usar una identidad administrada para autenticarse con otros servicios.

## <a name="use-azure-active-directory"></a>Uso de Azure Active Directory

**Guía de procedimientos recomendados**:implementación de clústeres de AKS con integración de Azure AD. Mediante Azure AD se centraliza el componente de administración de identidades. Cualquier cambio en el estado del grupo o cuenta del usuario se actualiza automáticamente al acceder al clúster de AKS. Use roles o ClusterRoles y enlaces, como se expone en la siguiente sección, para definir el ámbito de usuarios o grupos con la mínima cantidad de permisos necesarios.

Los desarrolladores y propietarios de aplicaciones del clúster de Kubernetes necesitan obtener acceso a diferentes recursos. Kubernetes no proporciona una solución de administración de identidades para controlar qué usuarios pueden interactuar con qué recursos. En su lugar, el usuario suele integrar el clúster con una solución de identidades existente. Azure Active Directory (AD) proporciona una solución de administración de identidades para el ámbito empresarial que se puede integrar con los clústeres de AKS.

Con los clústeres integrados de Azure AD en AKS, el usuario crea *Roles* o *ClusterRoles* que definen los permisos de acceso a los recursos. A continuación, *enlaza* los roles a usuarios o grupos de Azure AD. Estos controles de acceso basado en rol (RBAC) de Kubernetes se describen en la sección siguiente. En el diagrama siguiente se pueden ver la integración de Azure AD y la forma en que controla el acceso a los recursos:

![Autenticación a nivel de clúster para la integración de Azure Active Directory con AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. El desarrollador se autentica con Azure AD.
1. El punto de conexión de emisión de tokens de Azure AD emite el token de acceso.
1. El desarrollador realiza una acción con el token de Azure AD, como `kubectl create pod`.
1. Kubernetes valida el token con Azure Active Directory y recupera las pertenencias a grupos del desarrollador.
1. Se aplican las directivas del clúster y el control de acceso basado en rol (RBAC) de Kubernetes.
1. La solicitud del desarrollador es correcta o no en función de la validación anterior de pertenencia a grupos de Azure AD y las directivas y RBAC de Kubernetes.

Para crear un clúster de AKS que usa Azure AD, consulte [Integración de Azure Active Directory con Azure Kubernetes Service][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Uso de los controles de acceso basado en rol (RBAC)

**Guía de procedimientos recomendados**: uso de RBAC de Kubernetes para definir los permisos que los usuarios o grupos tienen en relación con los recursos del clúster. Cree roles y enlaces que asignen la mínima cantidad de permisos necesarios. Realice la integración con Azure AD, para que cualquier cambio en el estado del usuario o la pertenencia al grupo se actualice automáticamente y que el acceso a los recursos del clúster sea como el actual.

En Kubernetes, puede proporcionar un control granular del acceso a los recursos del clúster. Los permisos pueden definirse en el nivel de clúster, así como en espacios de nombres específicos. Puede definir qué recursos se pueden administrar y con qué permisos. Estos roles se aplican a los usuarios o grupos con un enlace. Para obtener más información acerca de los *roles*, *ClusterRoles* y *enlaces*, consulte [Opciones de acceso e identidad en Azure Kubernetes Service (AKS)][aks-concepts-identity].

Por ejemplo, puede crear un rol que conceda acceso completo a los recursos en el espacio de nombres denominado *finance-app*, tal y como se muestra en el siguiente ejemplo de manifiesto de YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

A continuación, se crea un RoleBinding que enlaza el usuario de Azure AD *developer1\@contoso.com* a la RoleBinding, como se muestra en el siguiente manifiesto YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Cuando *developer1\@contoso.com* se autentica en el clúster de AKS, que tengan permisos completos a los recursos en el *aplicación finance* espacio de nombres. De esta forma, separa y controla el acceso a los recursos de forma lógica. RBAC de Kubernetes debe usarse junto con la integración de Azure AD, como se describe en la sección anterior.

## <a name="use-pod-identities"></a>Uso de identidades de pod

**Guía de procedimientos recomendados**: no use credenciales fijas dentro de pods o imágenes de contenedor, ya que corren riesgo de exposición o abuso. En su lugar, use identidades de pod para solicitar acceso automáticamente mediante una solución central de identidades de Azure AD.

Cuando los pods necesitan obtener acceso a otros servicios de Azure, como Cosmos DB, Key Vault o Blob Storage, el pod necesita credenciales de acceso. Estas credenciales de acceso pueden definirse con la imagen de contenedor o insertadas como un secreto de Kubernetes, pero es necesario crearlas y asignarlas manualmente. A menudo, las credenciales se reutilizan en los pods y no se rotan de forma periódica.

Las identidades administradas para los recursos de Azure (actualmente se implementa como un proyecto de código abierto AKS asociado) permiten automáticamente solicitar acceso a los servicios a través de Azure AD. No defina manualmente las credenciales de los pods; en su lugar, solicite un token de acceso en tiempo real y podrá usarlo para acceder solo a los servicios asignados. En AKS, se implementan dos componentes mediante el operador de clústeres para permitir que los pods usen identidades administradas:

* **El servidor de identidad de administración de nodos (NMI)** es un pod que se ejecuta como DaemonSet en cada nodo del clúster de AKS. El servidor de NMI escucha las solicitudes de pods para servicios de Azure.
* **El controlador de identidades administradas (MIC)** es un pod central con permisos para consultar el servidor de API de Kubernetes y comprueba si hay una asignación de identidades de Azure que corresponde a un pod.

Cuando los pods solicitan acceso a un servicio de Azure, las reglas de red redirigen el tráfico al servidor de identidad de administración de nodos (NMI). El servidor de NMI identifica los pods que solicitan acceso a servicios de Azure en función de su dirección remota y consulta el controlador de identidades administradas (MIC). MIC comprueba si hay asignaciones de identidades de Azure en el clúster de AKS, y el servidor de NMI, a continuación, solicita un token de acceso de Azure Active Directory (AD) en función de la asignación de identidades del pod. Azure AD proporciona acceso al servidor de NMI, que se devuelve al pod. El pod puede usar este token de acceso para, después, solicitar acceso a los servicios de Azure.

En el ejemplo siguiente, un desarrollador crea un pod que usa una identidad administrada para solicitar acceso a una instancia de Azure SQL Server:

![Las identidades de pod permiten que un pod solicite acceso a otros servicios automáticamente.](media/operator-best-practices-identity/pod-identities.png)

1. Primero, el operador de clústeres crea una cuenta de servicio que se puede usar para asignar identidades cuando los pods solicitan acceso a los servicios.
1. El servidor de NMI y MIC se implementan para retransmitir las solicitudes de pod de tokens de acceso a Azure AD.
1. Un desarrollador implementa un pod con una identidad administrada que solicita un token de acceso a través del servidor de NMI.
1. El token se devuelve al pod y se utiliza para obtener acceso a una instancia de Azure SQL Server.

Las identidades administradas pod es un proyecto de código abierto de AKS y no es compatible con el soporte técnico de Azure. Se proporciona para recopilar comentarios y los errores de nuestra comunidad. El proyecto no se recomienda para su uso en producción.

Para usar las identidades de pod, consulte [Identidades de Azure Active Directory para aplicaciones de Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Pasos siguientes

Este artículo de procedimientos recomendados se centra en la autenticación y autorización para el clúster y los recursos. Para implementar algunos de estos procedimientos recomendados, consulte los artículos siguientes:

* [Integración de Azure Active Directory con Azure Kubernetes Service][aks-aad]
* [Uso de identidades administradas para recursos de Azure con AKS][aad-pod-identity]

Para obtener más información acerca de las operaciones de clúster en AKS, consulte los siguientes procedimientos recomendados:

* [Multi-tenancy and cluster isolation][aks-best-practices-scheduler] (Aislamiento multiempresa y de clúster)
* [Basic Kubernetes scheduler features][aks-best-practices-scheduler] (Características básicas de programadores de Kubernetes)
* [Advanced Kubernetes scheduler features][aks-best-practices-advanced-scheduler] (Características avanzadas de programadores de Kubernetes)

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: aad-integration.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
