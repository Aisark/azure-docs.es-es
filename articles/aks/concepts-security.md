---
title: 'Conceptos: seguridad en Azure Kubernetes Service (AKS)'
description: Obtenga información acerca de la seguridad en Azure Kubernetes Service (AKS), incluidos los secretos de Kubernetes, las directivas de red, y la comunicación con el maestro y los nodos.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 69ec3869f7bfd74b150db537a01e604cae87570f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441996"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceptos de seguridad de las aplicaciones y los clústeres en Azure Kubernetes Service (AKS)

Para proteger los datos de los clientes a medida que ejecuta cargas de trabajo de aplicaciones en Azure Kubernetes Service (AKS), es importante tener en cuenta la seguridad del clúster. Kubernetes incluye componentes de seguridad tales como *directivas de red* y *secretos*. Posteriormente, Azure agrega componentes, como grupos de seguridad de red y actualizaciones de clúster organizadas. Estos componentes de seguridad se combinan para que el clúster de AKS siga ejecutando las actualizaciones de seguridad del sistema operativo y las versiones de Kubernetes más recientes, con tráfico de pods seguro y acceso a credenciales confidenciales.

En este artículo se presentan los conceptos básicos para proteger sus aplicaciones en AKS:

- [Seguridad de componentes maestros](#master-security)
- [Seguridad de nodos](#node-security)
- [Actualización de un clúster de Service Fabric](#cluster-upgrades)
- [Seguridad de las redes](#network-security)
- [Secretos de Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Seguridad de componentes maestros

En AKS, los componentes maestros de Kubernetes forman parte del servicio administrado que proporciona Microsoft. Cada clúster de AKS tiene su propio maestro de Kubernetes dedicado de inquilino único para proporcionar el servidor de API, Scheduler, etc. Microsoft administra y mantiene este maestro.

De forma predeterminada, el servidor de API de Kubernetes utiliza una dirección IP pública con un nombre de dominio completo (FQDN). Puede controlar el acceso al servidor de API mediante controles de acceso basado en rol y Azure Active Directory. Para más información, consulte el artículo de [integración de Azure AD con AKS][aks-aad].

## <a name="node-security"></a>Seguridad de nodos

Los nodos de AKS son máquinas virtuales de Azure de los que usted realiza la administración y el mantenimiento. Los nodos de Linux ejecutan una distribución Ubuntu optimizada con el tiempo de ejecución del contenedor de Moby. Los nodos de Windows Server (actualmente en versión preliminar en AKS) ejecutan una versión de Windows Server 2019 y también, el tiempo de ejecución del contenedor de Moby. Cuando se crea o se escala verticalmente un clúster de AKS, los nodos se implementan automáticamente con las actualizaciones de seguridad del sistema operativo y las configuraciones más recientes.

La plataforma Azure aplica automáticamente las revisiones de seguridad del sistema operativo a los nodos de Linux del clúster durante la noche. Si una actualización de seguridad del sistema operativo de Linux requiere un reinicio del host, este no se realiza automáticamente. Puede reiniciar manualmente los nodos de Linux o usar un enfoque habitual que consiste en usar [Kured][kured], an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet][aks-daemonsets] y supervisar si en los nodos hay un archivo que indique que el reinicio es necesario. Los reinicios se administran en el clúster con el mismo [proceso de acordonar y purgar](#cordon-and-drain) que una actualización de clúster.

Para los nodos de Windows Server (actualmente en versión preliminar en AKS), Windows Update no ejecuta ni aplica las actualizaciones más recientes de manera automática. Según la programación normal en el ciclo de versiones de Windows Update y su proceso de validación propio, debe realizar una actualización de los grupos de nodos de Windows Server en el clúster de AKS. Esta actualización crea nodos que ejecutan la imagen y las revisiones más recientes de Windows Server y elimina los nodos anteriores. Para más información sobre este proceso, consulte la sección sobre la [Actualización de un grupo de nodos en AKS][nodepool-upgrade].

Los nodos se implementan en una subred de una red privada virtual, sin ninguna dirección IP pública asignada. Para fines de administración y solución de problemas, SSH está habilitado de forma predeterminada. El acceso de SSH solo está disponible mediante la dirección IP interna.

Para proporcionar almacenamiento, los nodos usan Azure Managed Disks. Para la mayoría de los tamaños de nodo de máquina virtual, estos son los discos Premium respaldados por SSD de alto rendimiento. Los datos almacenados en discos administrados se cifran automáticamente en reposo dentro de la plataforma Azure. Para mejorar la redundancia, estos discos también se replican de forma segura en el centro de datos de Azure.

Los entornos de Kubernetes en AKS o en cualquier otro lugar no están completamente seguros en este momento ante la utilización de multiinquilinos hostiles. Utilizar otras características de seguridad adicionales, como las *directivas de seguridad de pod* o los controles de acceso basados en roles (RBAC) más específicos, puede dificultar las vulnerabilidades de seguridad. Sin embargo, para que la seguridad resulte efectiva cuando se ejecutan cargas de trabajo multiinquilino hostiles, el hipervisor es el único nivel de seguridad en el que debe confiar. El dominio de seguridad de Kubernetes se convierte en todo el clúster, no en un nodo específico. En el caso de estos tipos de cargas de trabajo multiinquilino hostiles, debe usar clústeres que estén físicamente aislados. Para más información sobre las formas de aislar las cargas de trabajo, consulte [Procedimientos recomendados para el aislamiento de clústeres en Azure Kubernetes Service (AKS)][cluster-isolation].

## <a name="cluster-upgrades"></a>Actualizaciones de clústeres

Por motivos de seguridad y cumplimiento, o para usar las características más recientes, Azure proporciona herramientas para orquestar la actualización de un clúster y de componentes de AKS. La orquestación de esta actualización incluye los componentes tanto de maestro como de agente de Kubernetes. Puede ver una [lista de las versiones de Kubernetes disponibles](supported-kubernetes-versions.md) para su clúster de AKS. Para iniciar el proceso de actualización, especifique una de estas versiones disponibles. A continuación, Azure acordona y purga con seguridad cada uno de los nodos de AKS y lleva a cabo la actualización.

### <a name="cordon-and-drain"></a>Acordonar y purgar

Durante el proceso de actualización, los nodos de AKS se acordonan individualmente desde el clúster de modo que no se programen nuevos pods en ellos. A continuación, los nodos se purgan y actualizan de la siguiente manera:

- Se implementa un nuevo nodo en el grupo de nodos. Este nodo ejecuta la imagen de sistema operativo y las revisiones más recientes.
- Se identifica uno de los nodos existentes para la actualización. Los pods de este nodo finalizan correctamente y se programan en los otros nodos del grupo.
- Este nodo existente se elimina del clúster de AKS.
- El siguiente nodo del clúster se acordona y purga mediante el mismo proceso hasta que todos los nodos se reemplazan correctamente como parte de la actualización.

Para más información, consulte [Actualización de un clúster de Azure Kubernetes Service (AKS)][aks-upgrade-cluster].

## <a name="network-security"></a>Seguridad de las redes

Por motivos de conectividad y seguridad con las redes locales, puede implementar el clúster de AKS en subredes de red virtual de Azure existentes. Estas redes virtuales pueden presentar una conexión de VPN de sitio a sitio o ExpressRoute de Azure de vuelta a su red local. Los controladores de entrada de Kubernetes pueden definirse con direcciones IP internas privadas, de modo que los servicios solo sean accesibles a través de esta conexión de red interna.

### <a name="azure-network-security-groups"></a>Grupos de seguridad de red de Azure

Para filtrar el flujo del tráfico en las redes virtuales, Azure usa reglas de grupo de seguridad de red. Estas reglas definen los intervalos, los puertos y los protocolos de IP de origen y destino, a los que se permite o deniega el acceso a los recursos. Se crean reglas predeterminadas para permitir el tráfico TLS al servidor de API de Kubernetes. A medida que crea servicios con equilibradores de carga, asignaciones de puertos o rutas de entrada, AKS modifica automáticamente el grupo de seguridad de red para que el tráfico fluya adecuadamente.

## <a name="kubernetes-secrets"></a>Secretos de Kubernetes

Un *secreto* de Kubernetes se usa para insertar datos confidenciales en pods, como credenciales de acceso o claves. En primer lugar, cree un secreto mediante la API de Kubernetes. Al definir el pod o la implementación, puede solicitar un secreto específico. Los secretos solo se proporcionan a los nodos que tienen un pod programado que lo requiere. El secreto se almacena en el sistema *tmpfs*, no se escribe en el disco. Cuando se elimina el último pod en un nodo que requiere un secreto, el secreto se elimina del sistema tmpfs del nodo. Los secretos se almacenan en un espacio de nombres determinado y solo son accesibles para los pods del mismo espacio de nombres.

El uso de secretos reduce la información confidencial que se define en el pod o el manifiesto YAML del servicio. En su lugar, solicite el secreto almacenado en el servidor de API de Kubernetes como parte de su manifiesto YAML. Este enfoque proporciona acceso al secreto solo al pod específico. Nota: Los archivos de manifiesto de secreto sin formato contienen los datos secretos en formato base64 (consulte la [documentación oficial][secret-risks] para más detalles). Por lo tanto, este archivo debe considerarse como información confidencial y no confirmarse en el control de código fuente.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a proteger los clústeres de AKS, consulte [Actualización de un clúster de Azure Kubernetes Service (AKS)][aks-upgrade-cluster].

Para los procedimientos recomendados asociados, consulte [Procedimientos recomendados para administrar la seguridad y las actualizaciones de los clústeres en Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security].

Para obtener más información sobre los conceptos básicos de Kubernetes y AKS, consulte los artículos siguientes:

- [Clústeres y cargas de trabajo de Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identidad de Kubernetes/AKS][aks-concepts-identity]
- [Redes virtuales de Kubernetes/AKS][aks-concepts-network]
- [Almacenamiento de Kubernetes/AKS][aks-concepts-storage]
- [Escala de Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
