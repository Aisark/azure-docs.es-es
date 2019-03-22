---
title: Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales locales de Hyper-V en Azure | Microsoft Docs
description: Se resumen los requisitos y componentes compatibles para la recuperación ante desastres de máquinas virtuales de Hyper-V en Azure con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: ea9f6a65ae804d4d2e5004ff4e2c61a2a85b976d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317228"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales locales de Hyper-V en Azure


En este artículo se resumen los ajustes y los componentes compatibles para la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Escenarios admitidos

**Escenario** | **Detalles**
--- | ---
Hyper-V con Virtual Machine Manager | Ahora puede realizar la recuperación ante desastres en Azure de las máquinas virtuales que se ejecutan en los hosts de Hyper-V administrados en el tejido de System Center Virtual Machine Manager.<br/><br/> Puede implementar este escenario en Azure Portal o mediante PowerShell.<br/><br/> Cuando Virtual Machine Manager administra los hosts de Hyper-V, también puede realizar la recuperación ante desastres en un sitio local secundario. Lea [este tutorial](hyper-v-vmm-disaster-recovery.md) para más información sobre este escenario.
Hyper-V sin Virtual Machine Manager | Ahora puede realizar la recuperación ante desastres en Azure de las máquinas virtuales que se ejecutan en los hosts de Hyper-V no administrados por Virtual Machine Manager.<br/><br/> Puede implementar este escenario en Azure Portal o mediante PowerShell.


## <a name="on-premises-servers"></a>Servidores locales

**Servidor** | **Requisitos** | **Detalles**
--- | --- | ---
Hyper-V en ejecución sin Virtual Machine Manager | Windows Server 2016 (incluida la instalación de Server Core), Windows Server 2012 R2 con las actualizaciones más recientes | Si ya ha configurado Windows Server 2012 R2 o SCVMM 2012 R2 con Azure Site Recovery, y va a actualizar el sistema operativo, siga las instrucciones de la [documentación.](upgrade-2012R2-to-2016.md) 
Hyper-V en ejecución con Virtual Machine Manager | Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Si se usa Virtual Machine Manager, los hosts de Windows Server 2016 deben administrarse en Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Máquinas virtuales replicadas


En la tabla siguiente se resume la compatibilidad de las máquinas virtuales. Site Recovery admite todas las cargas de trabajo que se ejecutan en un sistema operativo compatible.

 **Componente** | **Detalles**
--- | ---
Configuración de VM | Las máquinas virtuales que se replican en Azure deben cumplir con los [requisitos de Azure](#azure-vm-requirements).
Sistema operativo invitado | Cualquier sistema operativo invitado [compatible con Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases).<br/><br/> No se admite Windows Server 2016 Nano Server.


## <a name="vmdisk-management"></a>Administración de máquinas virtuales o discos

**Acción** | **Detalles**
--- | ---
Cambiar el tamaño de disco en una máquina virtual de Hyper-V replicada | No se admite. Deshabilite la replicación, realice el cambio y, luego, vuelva a habilitar la replicación para la máquina virtual.
Agregar disco en máquina virtual de Hyper-V replicada | No se admite. Deshabilite la replicación, realice el cambio y, luego, vuelva a habilitar la replicación para la máquina virtual.

## <a name="hyper-v-network-configuration"></a>Configuración de la red de Hyper-V

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
Red de host: Formación de equipos NIC | Sí | Sí
Red de host: VLAN | Sí | Sí
Red de host: IPv4 | Sí | Sí
Red de host: IPv6 | No | No
Red de máquina virtual invitada: Formación de equipos NIC | No | No
Red de máquina virtual invitada: IPv4 | Sí | Sí
Red de máquina virtual invitada: IPv6 | No | Sí
Red de máquina virtual invitada: Dirección IP estática (Windows) | Sí | Sí
Red de máquina virtual invitada: Dirección IP estática (Linux) | No | No
Red de máquina virtual invitada: Varias NIC | Sí | Sí



## <a name="azure-vm-network-configuration-after-failover"></a>Configuración de la red de máquina virtual de Azure (después de la conmutación por error)

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Sí | Sí
ILB | Sí | Sí
ELB | Sí | Sí
Azure Traffic Manager | Sí | Sí
Varias NIC | Sí | Sí
IP reservada | Sí | Sí
IPv4 | Sí | Sí
Conservar la dirección IP de origen | Sí | Sí
Puntos de conexión del servicio Azure Virtual Network<br/> (sin firewalls de Azure Storage) | Sí | Sí
Redes aceleradas | No | No


## <a name="hyper-v-host-storage"></a>Almacenamiento de host de Hyper-V

**Storage** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | --- 
NFS | N/A | N/A
SMB 3.0 | Sí | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO). Probado con:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM para CLARiiON | Sí | Sí

## <a name="hyper-v-vm-guest-storage"></a>Almacenamiento de invitado de máquina virtual de Hyper-V

**Storage** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
VMDK | N/A | N/A
VHD/VHDX | Sí | Sí
VM de generación 2 | Sí | Sí
EFI/UEFI| Sí | Sí
Disco en clúster compartido | No | No
Disco cifrado | No | No
NFS | N/A | N/A
SMB 3.0 | No | No
RDM | N/A | N/A
Disco > 1 TB | Sí, hasta 4095 GB | Sí, hasta 4095 GB
Disco: sector físico y lógico de 4 K | No compatible: Gen 1, Gen 2 | No compatible: Gen 1, Gen 2
Disco: sector lógico de 4 K y sector físico de 512 bytes | Sí |  Sí
Administración de volúmenes lógicos (LVM). Se admite LVM solo en discos de datos. Azure proporciona un único disco de sistema operativo. | Sí | Sí
Volumen con disco seccionado > 1 TB | Sí | Sí
Espacios de almacenamiento | Sí | Sí
Agregar/quitar disco en caliente | No | No
Excluir el disco | Sí | Sí
Varias rutas (MPIO) | Sí | Sí

## <a name="azure-storage"></a>Azure Storage

**Componente** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
Almacenamiento con redundancia local | Sí | Sí
Almacenamiento con redundancia geográfica | Sí | Sí
Almacenamiento con redundancia geográfica con acceso de lectura | Sí | Sí
Almacenamiento de acceso esporádico | No | No
Almacenamiento de acceso frecuente| No | No
Blobs en bloques | No | No
Cifrado en reposo (SSE)| Sí | Sí
Premium Storage | Sí | Sí
Servicio Import/Export | No | No
Los firewalls de Azure Storage para redes virtuales se configuran en la cuenta de almacenamiento o la cuenta de almacenamiento en caché de destino (se usa para almacenar datos de replicación) | No | No


## <a name="azure-compute-features"></a>Características de proceso de Azure

**Característica** | **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
Conjuntos de disponibilidad | Sí | Sí
CONCENTRADOR | Sí | Sí  
Discos administrados | Sí, para la conmutación por error.<br/><br/> No se admite la conmutación por recuperación de los discos administrados. | Sí, para la conmutación por error.<br/><br/> No se admite la conmutación por recuperación de los discos administrados.

## <a name="azure-vm-requirements"></a>Requisitos de VM de Azure

Las máquinas virtuales locales que se replican en Azure deben cumplir con los requisitos de VM de Azure que se resumen en esta tabla.

**Componente** | **Requisitos** | **Detalles**
--- | --- | ---
Sistema operativo invitado | Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Arquitectura del sistema operativo invitado | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño del disco del sistema operativo | Hasta 2048 GB para máquinas virtuales de generación 1.<br/><br/> Hasta 300 GB para máquinas virtuales de generación 2.  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos del sistema operativo | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos de datos | 16 o menos  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño de VHD del disco de datos | Hasta 4095 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Adaptador de red | Se admiten varios adaptadores |
VHD compartido | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Disco FC | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Formato de disco duro | VHD <br/><br/>  VHDX | Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en el entorno local, las máquinas virtuales siguen usando el formato VHDX.
BitLocker | No compatible | Debe deshabilitar BitLocker antes de habilitar la replicación para una máquina virtual.
Nombre de VM | Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre de la VM debe empezar y terminar con una letra o un número. | Actualice el valor de las propiedades de la máquina virtual en Site Recovery.
Tipo de máquina virtual | Generación 1<br/><br/> Generación 2: Windows | Las VM de generación 2 con un tipo de disco de SO básico, que incluye uno o dos volúmenes de datos con el formato VHDX y menos de 300 GB de espacio en disco, son compatibles.<br></br>No se admiten las VM Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Acciones del almacén de Recovery Services

**Acción** |  **Hyper-V con Virtual Machine Manager** | **Hyper-V sin Virtual Machine Manager**
--- | --- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | No | No
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | No | No

> [!NOTE]
> Al replicar máquinas virtuales de Hyper-V (administradas con o sin SCVMM) desde un entorno local en Azure, solo puede replicar a un inquilino de AD de un sitio Hyper-V específico del entorno o SCVMM según corresponda.


## <a name="provider-and-agent"></a>Proveedor y agente

Para garantizar que la implementación sea compatible con la configuración que se muestra en este artículo, asegúrese de ejecutar las versiones más recientes de proveedor y agente.

**Name** | **Descripción** | **Detalles**
--- | --- | --- 
Proveedor de Azure Site Recovery | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Hyper-V con Virtual Machine Manager: Se instala en servidores de Virtual Machine Manager<br/><br/> Hyper-V sin Virtual Machine Manager: Se instala en hosts de Hyper-V| Versión más reciente: 5.1.2700.1 (disponible en Azure Portal)<br/><br/> [Características y correcciones más recientes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente de Microsoft Azure Recovery Services | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/> Se instala en servidores de Hyper-V locales (con o sin Virtual Machine Manager) | Agente más reciente disponible en el portal






## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo [preparar Azure](tutorial-prepare-azure.md) para la recuperación ante desastres de las máquinas virtuales de Hyper-V locales.
