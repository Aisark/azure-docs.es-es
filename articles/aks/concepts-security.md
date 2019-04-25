---
title: 'Conceptos: seguridad en Azure Kubernetes Service (AKS)'
description: Obtenga información acerca de la seguridad en Azure Kubernetes Service (AKS), incluidos los secretos de Kubernetes, las directivas de red, y la comunicación con el maestro y los nodos.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 8fd5b726c01b056d38e7e187cec8270ee4e127a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466746"
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

En AKS, los componentes maestros de Kubernetes forman parte del servicio administrado que proporciona Microsoft. Cada clúster de AKS tiene su propio maestro de Kubernetes dedicado de inquilino único para proporcionar el servidor de API, Scheduler, etc. Este patrón administrado y mantenido por Microsoft.

De forma predeterminada, el servidor de API de Kubernetes utiliza una dirección IP pública con un nombre de dominio completo (FQDN). Puede controlar el acceso al servidor de API mediante controles de acceso basado en rol y Azure Active Directory. Para obtener más información, consulte [Integración de Azure Active Directory con Azure Kubernetes Service][aks-aad].

## <a name="node-security"></a>Seguridad de nodos

Los nodos de AKS son máquinas virtuales de Azure de los que usted realiza la administración y el mantenimiento. Los nodos que ejecuten una distribución Ubuntu Linux optimizada con el tiempo de ejecución del contenedor de Moby. Cuando se crea o se escala verticalmente un clúster de AKS, los nodos se implementan automáticamente con las actualizaciones de seguridad del sistema operativo y las configuraciones más recientes.

La plataforma Azure aplica automáticamente las revisiones de seguridad del sistema operativo a los nodos del clúster durante la noche. Si una actualización de seguridad del sistema operativo requiere un reinicio del host, este no se realiza automáticamente. Puede reiniciar manualmente los nodos, o bien optar por un enfoque común que consiste en usar [Kured][kured], un demonio de reinicio de código abierto para Kubernetes. Kured se ejecuta como un elemento [DaemonSet][aks-daemonsets] y supervisa en todos los nodos la presencia de un archivo que indique que hace falta un reinicio. Los reinicios se administran en el clúster con el mismo [proceso de acordonar y purgar](#cordon-and-drain) que una actualización de clúster.

Los nodos se implementan en una subred de una red privada virtual, sin ninguna dirección IP pública asignada. Para fines de administración y solución de problemas, SSH está habilitado de forma predeterminada. El acceso de SSH solo está disponible mediante la dirección IP interna.

Para proporcionar almacenamiento, los nodos usan Azure Managed Disks. Para la mayoría de los tamaños de nodo de máquina virtual, estos son los discos Premium respaldados por SSD de alto rendimiento. Los datos almacenados en discos administrados se cifran automáticamente en reposo dentro de la plataforma Azure. Para mejorar la redundancia, estos discos también se replican de forma segura en el centro de datos de Azure.

Los entornos de Kubernetes en AKS o en cualquier otro lugar no están completamente seguros en este momento ante la utilización de multiinquilinos hostiles. Utilizar otras características de seguridad adicionales, como las *directivas de seguridad de pod* o los controles de acceso basados en roles (RBAC) más específicos, puede dificultar las vulnerabilidades de seguridad. Sin embargo, para que la seguridad resulte efectiva cuando se ejecutan cargas de trabajo multiinquilino hostiles, el hipervisor es el único nivel de seguridad en el que debe confiar. El dominio de seguridad de Kubernetes se convierte en todo el clúster, no en un nodo específico. En el caso de estos tipos de cargas de trabajo multiinquilino hostiles, debe usar clústeres que estén físicamente aislados. Para más información sobre las formas de aislar las cargas de trabajo, consulte [Procedimientos recomendados para el aislamiento de clústeres en Azure Kubernetes Service (AKS)][cluster-isolation].

## <a name="cluster-upgrades"></a>Actualizaciones de clústeres

Por motivos de seguridad y cumplimiento, o para usar las características más recientes, Azure proporciona herramientas para orquestar la actualización de un clúster y de componentes de AKS. La orquestación de esta actualización incluye los componentes tanto de maestro como de agente de Kubernetes. Puede ver una [lista de las versiones de Kubernetes disponibles](supported-kubernetes-versions.md) para su clúster de AKS. Para iniciar el proceso de actualización, especifique una de estas versiones disponibles. A continuación, Azure acordona y purga con seguridad cada uno de los nodos de AKS y lleva a cabo la actualización.

### <a name="cordon-and-drain"></a>Acordonar y purgar

Durante el proceso de actualización, los nodos de AKS se acordonan individualmente desde el clúster, de modo que no se programen nuevos pods en estos. A continuación, los nodos se purgan y actualizan de la siguiente manera:

- Los pods existentes se finalizan correctamente y se programan en los nodos restantes.
- El nodo se reinicia, el proceso de actualización se completa y, a continuación, se vuelve a unir al clúster de AKS.
- Los pods se programan para ejecutarse de nuevo en estos.
- El siguiente nodo del clúster se acordona y purga mediante el mismo proceso hasta que todos los nodos están actualizados correctamente.

Para más información, consulte [Actualización de un clúster de AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Seguridad de las redes

Por motivos de conectividad y seguridad con las redes locales, puede implementar el clúster de AKS en subredes de red virtual de Azure existentes. Estas redes virtuales pueden presentar una conexión de VPN de sitio a sitio o ExpressRoute de Azure de vuelta a su red local. Los controladores de entrada de Kubernetes pueden definirse con direcciones IP internas privadas, de modo que los servicios solo sean accesibles a través de esta conexión de red interna.

### <a name="azure-network-security-groups"></a>Grupos de seguridad de red de Azure

Para filtrar el flujo del tráfico en las redes virtuales, Azure usa reglas de grupo de seguridad de red. Estas reglas definen los intervalos, los puertos y los protocolos de IP de origen y destino, a los que se permite o deniega el acceso a los recursos. Las reglas predeterminadas se crean para permitir el tráfico TLS para el servidor de API de Kubernetes. A medida que crea servicios con equilibradores de carga, asignaciones de puertos o rutas de entrada, AKS modifica automáticamente el grupo de seguridad de red para que el tráfico fluya adecuadamente.

## <a name="kubernetes-secrets"></a>Secretos de Kubernetes

Un *secreto* de Kubernetes se usa para insertar datos confidenciales en pods, como credenciales de acceso o claves. En primer lugar, cree un secreto mediante la API de Kubernetes. Al definir el pod o la implementación, puede solicitar un secreto específico. Los secretos solo se proporcionan a los nodos que tienen un pod programado que lo requiere. El secreto se almacena en el sistema *tmpfs*, no se escribe en el disco. Cuando se elimina el último pod en un nodo que requiere un secreto, el secreto se elimina del sistema tmpfs del nodo. Los secretos se almacenan en un espacio de nombres determinado y solo son accesibles para los pods del mismo espacio de nombres.

El uso de secretos reduce la información confidencial que se define en el pod o el manifiesto YAML del servicio. En su lugar, solicite el secreto almacenado en el servidor de API de Kubernetes como parte de su manifiesto YAML. Este enfoque proporciona acceso al secreto solo al pod específico.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a proteger los clústeres de AKS, consulte [Upgrade an AKS cluster][aks-upgrade-cluster] (Actualización de un clúster de AKS).

Para las prácticas recomendadas asociadas, consulte [procedimientos recomendados de seguridad del clúster y las actualizaciones en AKS][operator-best-practices-cluster-security].

Para obtener más información sobre los conceptos básicos de Kubernetes y AKS, consulte los artículos siguientes:

- [Kubernetes / AKS clusters and workloads][aks-concepts-clusters-workloads] (Clústeres y cargas de trabajo de Kubernetes/AKS)
- [Kubernetes / AKS identity][aks-concepts-identity] (Identidad de Kubernetes/AKS)
- [Kubernetes / AKS virtual networks][aks-concepts-network] (Redes virtuales de Kubernetes/AKS)
- [Kubernetes / AKS storage][aks-concepts-storage] (Almacenamiento de Kubernetes/AKS)
- [Kubernetes / AKS scale][aks-concepts-scale] (Escala de Kubernetes/AKS)

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

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
