---
title: Configuración del entorno de desarrollo de Red Hat OpenShift en Azure | Microsoft Docs
description: Estos son los requisitos previos para trabajar con Red Hat OpenShift en Microsoft Azure.
services: openshift
keywords: configuración red hat openshift
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 21ccd7b2919714610dbd51c62701c4bb32d330d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808819"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configuración del entorno de desarrollo de Red Hat OpenShift en Azure

Para compilar y ejecutar aplicaciones de Red Hat OpenShift en Microsoft Azure, deberá:

* Comprar Azure Reserved Virtual Machine Instances.
* Instalar la versión 2.0.65 (o cualquier versión superior) de la CLI de Azure (o usar Azure Cloud Shell).
* Registrarse para la característica `AROGA` y los proveedores de recursos asociados.
* Crear un inquilino de Azure Active Directory (Azure AD).
* Crear un objeto de aplicación de Azure AD.
* Crear un usuario de Azure AD.

Las instrucciones siguientes le guiarán por todos estos requisitos previos.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Compra de instancias reservadas de nodos de aplicación de Red Hat OpenShift en Azure

Para poder usar Red Hat OpenShift en Azure, deberá adquirir un mínimo de cuatro nodos de aplicación reservados de Red Hat OpenShift en Azure, tras lo que podrá aprovisionar los clústeres.

Si es cliente de Azure, [compre instancias reservadas de Red Hat OpenShift en Azure](https://aka.ms/openshift/buy) a través de Azure Portal. Después de la compra, la suscripción se activará en un plazo máximo de 24 horas.

Si no es cliente de Azure, [póngase en contacto con el departamento comercial](https://aka.ms/openshift/contact-sales) y rellene el formulario de ventas de la parte inferior de la página para iniciar el proceso.

Para más información, consulte la [página de precios de Red Hat OpenShift en Azure](https://aka.ms/openshift/pricing).

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure

Red Hat OpenShift en Azure requiere la versión 2.0.65, o cualquier versión posterior, de la CLI de Azure. Si ya ha instalado la CLI de Azure, puede comprobar qué versión tiene. Para ello, debe ejecutar:

```bash
az --version
```

La primera línea de salida tendrá la versión de la CLI, por ejemplo `azure-cli (2.0.65)`.

Aquí encontrará instrucciones para [instalar la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) tanto si necesita una instalación nueva como una actualización.

Como alternativa, puede usar [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Cuando use Azure Cloud Shell, no olvide seleccionar el entorno **Bash** si planea seguir la serie de tutoriales [Tutorial: Creación de un clúster de Red Hat OpenShift en Azure](tutorial-create-cluster.md).

## <a name="register-providers-and-features"></a>Registro de proveedores y características

La característica `Microsoft.ContainerService AROGA` y los proveedores `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` y `Microsoft.Network` deben registrarse en su suscripción manualmente antes de implementar el primer clúster de Red Hat OpenShift en Azure.

Para registrar manualmente estos proveedores y características, utilice las instrucciones siguientes desde un shell de Bash si ha instalado la CLI, o bien desde la sesión de Azure Cloud Shell (Bash) en Azure Portal:

1. Si tiene varias suscripciones de Azure, especifique el identificador de la relevante:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre la característica Microsoft.ContainerService AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registre el proveedor Microsoft.Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registre el proveedor Microsoft.Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registre el proveedor Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registre el proveedor Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registre el proveedor Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Actualice el registro del proveedor de recursos Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creación de un inquilino de Azure Active Directory (Azure AD)

El servicio Red Hat OpenShift en Azure requiere un inquilino de Azure Active Directory (Azure AD) asociado que represente su organización y su relación con Microsoft. Su inquilino de Azure AD le permite registrar, compilar y administrar aplicaciones, así como usar otros servicios de Azure.

Si no tiene Azure AD para usarlo como el inquilino de su clúster de Red Hat OpenShift en Azure, o desea crear un inquilino de prueba, siga las instrucciones de [Creación de un inquilino de Azure AD para Red Hat OpenShift en Azure](howto-create-tenant.md) antes continuar con esta guía.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Creación de un usuario, un grupo de seguridad y un objeto de aplicación de Azure AD

Red Hat OpenShift en Azure requiere permisos para realizar tareas en su clúster, como configurar el almacenamiento. Dichos permisos se representan mediante una [entidad de servicio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). También puede crear un usuario de Active Directory para probar las aplicaciones que se ejecutan en el clúster de Red Hat OpenShift en Azure.

Siga las instrucciones de [Creación de un usuario y un objeto de aplicación de Azure AD](howto-aad-app-configuration.md) para crear una entidad de servicio, generar un secreto de cliente y una dirección URL de para la devolución de llamada de la autenticación, y crear un usuario y un grupo de seguridad de Azure AD y para acceder al clúster.

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para usar Red Hat OpenShift en Azure.

Pruebe el tutorial:
> [!div class="nextstepaction"]
> [Creación de un clúster de Red Hat OpenShift en Azure](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
