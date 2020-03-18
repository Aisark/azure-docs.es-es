---
title: Preguntas frecuentes sobre Azure Migrate Server Migration
description: Obtenga respuestas a preguntas habituales sobre el uso de Azure Migrate Server Migration para migrar máquinas.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 4d3638e930b4e12a29df4ab189ffb24ab248582b
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939209"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server Migration: Preguntas frecuentes

En este artículo se responde a preguntas habituales sobre Azure Migrate: Herramienta de migración del servidor. Si tiene otras preguntas, repase estos recursos:

- [Preguntas generales](resources-faq.md) sobre Azure Migrate
- Preguntas sobre el dispositivo de [Azure Migrate](common-questions-appliance.md)
- Preguntas sobre la [detección, valoración y visualización de dependencias](common-questions-discovery-assessment.md)
- Obtenga respuestas a sus preguntas en el [foro de Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>¿Cómo funciona la replicación sin agente de VMware?

El método de replicación sin agente de VMware emplea instantáneas de VMware y seguimiento de bloques modificados (CBT) en VMware.

Este es el proceso:

1. Cuando inicia la replicación, se programa un ciclo de replicación inicial. En el ciclo inicial, se toma una instantánea de la VM. La instantánea se usa para replicar los VMDK (discos) de la máquina virtual. 
2. Una vez finalizado el ciclo de replicación inicial, se programan ciclos de replicación diferencial periódicamente.
    - Durante la replicación diferencial, se toma una instantánea y se replican los bloques de datos que han cambiado desde el ciclo de replicación anterior.
    - El seguimiento de bloques modificados en VMware se usa para determinar los bloques que han cambiado desde el último ciclo.
    - Azure Migrate administra automáticamente la frecuencia de los ciclos de replicación periódicos en función de cuántas otras máquinas virtuales o discos se estén replicando a la vez desde el mismo almacén de datos. En condiciones ideales, la replicación finalmente converge en un ciclo por hora para cada VM.

Al migrar, se programa un ciclo de replicación a petición para que la máquina capture el resto de los datos. Puede optar por apagar la máquina durante la migración para garantizar la coherencia de la aplicación y que no haya pérdida de datos.

## <a name="why-isnt-resynchronization-exposed"></a>¿Por qué no se expone la resincronización?

Durante la migración sin agente, en cada ciclo diferencial se escribe la diferencia entre la instantánea actual y la instantánea tomada previamente. Puesto que siempre es la diferencia entre las instantáneas, los datos se incorporan. Si un sector específico se escribe *N* veces entre instantáneas, solo se debe transferir la última escritura, ya que solo nos interesa la última sincronización. El proceso es diferente de la replicación basada en agente, donde se realiza un seguimiento de todas las escrituras y se aplican estas. En este proceso, cada ciclo diferencial es una resincronización. Por lo tanto, no se expone ninguna opción de resincronización. Si alguna vez los discos no están sincronizados debido a un error, se corrige en el siguiente ciclo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>¿Cómo afecta la tasa de modificación a la replicación sin agente?

Dado que la replicación sin agente incorpora datos, el *patrón de modificación* es más importante que la *tasa de modificación*. Cuando un archivo se escribe una y otra vez, la tasa no tiene un gran efecto. Sin embargo, un patrón en el que se escriben todos los demás sectores produce una gran renovación en el ciclo siguiente. Dado que se reduce la cantidad de datos que se transfieren, se permite que los datos se plieguen lo más posible antes de programar el siguiente ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>¿Con qué frecuencia se programa un ciclo de replicación?

La fórmula para programar el siguiente ciclo de replicación es igual al tiempo de ciclo anterior/2 o una hora, lo que sea mayor.

Por ejemplo, si una VM tarda cuatro horas en un ciclo diferencial, su siguiente ciclo se programa dos horas después y no en la próxima hora. El proceso es distinto inmediatamente después de la replicación inicial, en la que el primer ciclo diferencial se programa inmediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>¿Cómo afecta la replicación sin agente a los servidores de VMware?

La replicación sin agente produce un impacto en el rendimiento de los hosts de VMware vCenter Server y VMware ESXi. Dado que la replicación sin agente utiliza instantáneas, consume IOPS en el almacenamiento, por lo que se necesita algo de ancho de banda de almacenamiento de IOPS. No se recomienda usar la replicación sin agente si hay restricciones en el almacenamiento o en el número de IOPS en el entorno.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>¿Puedo realizar la migración sin agente de VM UEFI a Azure Gen 2?

No. Use Azure Site Recovery para migrar estas VM a VM de Azure Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>¿Se pueden anclar VM a Azure Availability Zones al migrarlas?

No. No se admite Azure Availability Zones para la migración de Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>¿Qué protocolo de transporte usa Azure Migrate durante la replicación?

Azure Migrate usa el protocolo de dispositivo de bloque de red (NBD) con cifrado SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>¿Cuál es la versión mínima de vCenter Server necesaria para la migración?

Debe tener al menos vCenter Server 5.5 y la versión 5.5 del host de ESXi de vSphere.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>¿Los clientes pueden migrar sus máquinas virtuales a discos no administrados?

No. Azure Migrate solo admite la migración a discos administrados (HDD estándar, SSD premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>¿Cuántas máquinas virtuales se pueden replicar a la vez con la migración sin agente?

Actualmente, se pueden migrar 100 máquinas virtuales por instancia de vCenter Server a la vez. Realice la migración en lotes de 10 VM.

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>¿Cuándo debo migrar máquinas como servidores físicos a Azure?

La migración de máquinas tratándolas como servidores físicos es útil en varios escenarios:

- Al migrar servidores físicos locales.
- Cuando vaya a migrar máquinas virtuales virtualizadas por plataformas como Xen, KVM.
- Para migrar máquinas virtuales de Hyper-V o VMware si, por algún motivo, no puede usar el proceso de migración estándar para la migración de [Hyper-V](tutorial-migrate-hyper-v.md) o [VMware](server-migrate-overview.md). Por ejemplo, si no va a ejecutar VMware vCenter y solo usa hosts de ESXi.
- Para migrar máquinas virtuales que se ejecutan actualmente en nubes privadas a Azure
- Si desea migrar máquinas virtuales que se ejecutan en nubes públicas, como Amazon Web Services (AWS) o Google Cloud Platform (GCP), a Azure.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>¿Necesito VMware vCenter para migrar máquinas virtuales de VMware?
Para [migrar máquinas virtuales de VMware](server-migrate-overview.md) mediante la migración sin agente o basada en agentes de VMware. vCenter Server debe administrar los hosts de ESXi en los que se encuentran las máquinas virtuales. Si no tiene vCenter Server, puede migrar máquinas virtuales de VMware mediante su migración como servidores físicos. [Más información](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Pasos siguientes

Consulte la sección [Información general de Azure Migrate](migrate-services-overview.md).
