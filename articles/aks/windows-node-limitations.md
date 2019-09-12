---
title: Limitaciones de los grupos de nodos de Windows Server en Azure Kubernetes Service (AKS)
description: Obtenga información sobre las limitaciones conocidas al ejecutar cargas de trabajo de aplicaciones y grupos de nodos de Windows Server en Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: ca5d857e4d473c7f76b7fac62e8a8bab39769b25
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233132"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitaciones actuales para las cargas de trabajo de aplicaciones y los grupos de nodos de Windows Server en Azure Kubernetes Service (AKS)

En Azure Kubernetes Service (AKS) puede crear un grupo de nodos que ejecuta Windows Server como sistema operativo invitado en los nodos. Estos nodos pueden ejecutar aplicaciones de contenedor nativas de Windows, como las integradas en .NET Framework. Como existen diferencias importantes en la forma en que los sistemas operativos Windows y Linux proporcionan compatibilidad con contenedores, algunas características de Kubernetes comunes y relacionadas con el pod no están actualmente disponibles para grupos de nodos de Windows.

En este artículo se describen algunas de las limitaciones y los conceptos de sistema operativo para los nodos de Windows Server en AKS. Los grupos de nodos para Windows Server actualmente están en versión preliminar.

> [!IMPORTANT]
> Las características en vista previa de AKS son de autoservicio y se tienen que habilitar. Las versiones preliminares se proporcionan "tal cual" y "como están disponibles", y están excluidas de los contratos de nivel de servicio y la garantía limitada. Las versiones preliminares de AKS reciben cobertura parcial del soporte al cliente en la medida de lo posible. Por lo tanto, estas características no están diseñadas para usarse en producción. Para más información, consulte los siguientes artículos de soporte:
>
> * [Directivas de soporte técnico para AKS][aks-support-policies]
> * [Preguntas más frecuentes de soporte técnico de Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>¿Qué sistemas operativos de Windows se admiten?

AKS usa Windows Server 2019 como la versión del sistema operativo del host y solo admite el aislamiento de procesos. No se admiten las imágenes de contenedor compiladas con otras versiones de Windows Server. [Compatibilidad con versiones de contenedores de Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>¿Es Kubernetes diferente en Windows y Linux?

La compatibilidad del grupo de nodos de Windows Server incluye algunas limitaciones que forman parte de Windows Server ascendente en el proyecto de Kubernetes. Estas limitaciones no son específicas de AKS. Para obtener más información sobre esta compatibilidad ascendente con Windows Server en Kubernetes, consulte la sección de [funcionalidades y limitaciones admitidas][upstream-limitations] del documento [Información sobre la compatibilidad de Windows en Kubernetes][intro-windows], desde el proyecto Kubernetes.

Kubernetes se ha centrado siempre en Linux. Muchos ejemplos usados en el sitio web [Kubernetes.io][kubernetes] ascendente están diseñados para su uso en nodos de Linux. Al crear implementaciones que usan contenedores de Windows Server, se aplican las consideraciones siguientes en el nivel de sistema operativo:

- **Identidad**: Linux utiliza los valores userID (UID) y groupID (GID), representados como tipos de enteros. Los nombres de usuario y de grupo no son canónicos: son simplemente un alias en */etc/groups* o */etc/passwd* en UID+GID.
    - Windows Server usa un identificador de seguridad binario de mayor tamaño (SID) que se almacena en la base de datos de Windows Security Access Manager (SAM). Esta base de datos no se comparte entre el host y los contenedores ni entre contenedores.
- **Permisos de archivos**: Windows Server utiliza una lista de control de acceso basada en SID, en lugar de una máscara de bits de permisos y UID+GID.
- **Rutas de archivo**: la convención en Windows Server consiste en usar \ en lugar de /.
    - En las especificaciones de pod que montan los volúmenes, especifique la ruta correctamente para los contenedores de Windows Server. Por ejemplo, en lugar de un punto de montaje de */mnt/volume* en un contenedor de Linux, especifique la letra de unidad y la ubicación como */K/Volume* para montarlo como la unidad *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>¿Qué tipo de discos se admiten en Windows?

Azure Disk y Azure Files son los tipos de volúmenes admitidos, a los que se accede como volúmenes NTFS en el contenedor de Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>¿Puedo ejecutar clústeres que sean solo de Windows en AKS?

AKS hospeda los nodos maestros (esto es, el plano de control) de un clúster de AKS en el servicio, y usted no verá el sistema operativo de los nodos que hospedan los componentes maestros. Todos los clústeres de AKS se crean con un primer grupo de nodos predeterminado que se basa en Linux. Este grupo de nodos contiene los servicios del sistema necesarios para que el clúster funcione. Se recomienda ejecutar al menos dos nodos en el primer grupo de nodos para garantizar la confiabilidad del clúster y que tenga la capacidad de realizar operaciones de clúster. No se puede eliminar este primer grupo de nodos basado en Linux, a menos que se elimine el propio clúster de AKS.

## <a name="what-network-plug-ins-are-supported"></a>¿Qué complementos de red se admiten?

Los clústeres de AKS con grupos de nodos de Windows deben usar el modelo de redes (avanzado) de Azure CNI. No se admiten las redes Kubenet (básico). Para obtener más información sobre las diferencias en los modelos de redes, consulte [Conceptos de redes para las aplicaciones en AKS][azure-network-models]. - El modelo de redes de Azure CNI requiere un plan y consideraciones adicionales para la administración de direcciones IP. Para obtener más información sobre cómo planear e implementar Azure CNI, consulte [Configuración de redes de Azure CNI en AKS][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>¿Puedo cambiar el número mínimo de pods por nodo?

Actualmente, es necesario establecer un mínimo de 30 pods para garantizar la confiabilidad de los clústeres.

## <a name="how-do-patch-my-windows-nodes"></a>¿Cómo se aplica la revisión en los nodos de Windows?

Los nodos de Windows Server en AKS deben estar *actualizados* para obtener las correcciones y actualizaciones más recientes. Las actualizaciones de Windows no están habilitadas en los nodos de AKS. AKS publica nuevas imágenes de grupos de nodos en cuanto hay revisiones disponibles; es responsabilidad de los clientes actualizar los grupos de nodos para mantenerse al día en las revisiones y reparaciones. Esto también se aplica a la versión de Kubernetes que se esté usando. Las notas de la versión de AKS indicarán cuándo estarán disponibles las nuevas versiones. Para más información sobre cómo actualizar un grupo de nodos de Windows Server, consulte el artículo de [actualización de un grupo de nodos en AKS][nodepool-upgrade].

> [!NOTE]
> La imagen de Windows Server actualizada solo se usará si se ha realizado una actualización de clúster (actualización del plano de control) antes de actualizar el grupo de nodos.
>

## <a name="how-many-node-pools-can-i-create"></a>¿Cuántos grupos de nodos puedo crear?

El clúster de AKS puede tener un máximo de ocho (8) grupos de nodos. Puede tener un máximo de 400 nodos distribuidos entre esos grupos de nodos. [Limitaciones del grupo de nodos][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>¿Qué nombre puedo usar para mis grupos de nodos de Windows?

El nombre debe tener un máximo de 6 (seis) caracteres. Esta es una limitación de AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>¿Son todas las características compatibles con los nodos de Windows?

Las directivas de red y de kubenet no se admiten actualmente en los nodos de Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>¿Puedo ejecutar controladores de entrada en los nodos de Windows?

Sí, un controlador de entrada que admita contenedores de Windows Server se puede ejecutar en nodos de Windows en AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>¿Puedo usar Azure Dev Spaces con los nodos de Windows?

Azure Dev Spaces actualmente solo está disponible para grupos de nodos basados en Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>¿Pueden usar gMSA los contenedores de Windows Server?

La compatibilidad con las cuentas de servicio administradas de grupo (gMSA) no está disponible actualmente en AKS.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>¿Qué ocurre si necesito una característica que no se admite?

Estamos trabajando día y noche para poner a su disposición todas las características que necesite para Windows en AKS, pero si encuentra errores, el proyecto ascendente de código abierto [aks-engine][aks-engine], proporciona una manera fácil y totalmente personalizable de ejecutar Kubernetes en Azure e incluye el soporte técnico de Windows. Asegúrese de consultar nuestro plan de desarrollo de las características que se incluyen en el [plan de desarrollo de AKS][aks-roadmap].

## <a name="next-steps"></a>Pasos siguientes

Para comenzar con los contenedores de Windows Server en AKS, [cree un grupo de nodos que ejecute Windows Server en AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility