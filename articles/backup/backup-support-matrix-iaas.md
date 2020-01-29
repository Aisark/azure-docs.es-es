---
title: Matriz de compatibilidad para copias de seguridad de máquinas virtuales de Azure
description: Proporciona un resumen de opciones de compatibilidad y limitaciones para realizar copias de seguridad de máquinas virtuales de Azure con el servicio Azure Backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: c57f625e7f44dc5de6a801ec93bad5433e9a9a66
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294293"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de compatibilidad para copias de seguridad de máquinas virtuales de Azure

Puede usar el [servicio Azure Backup](backup-overview.md) para realizar copias de seguridad de cargas de trabajo y máquinas locales, y de las máquinas virtuales de Azure. En este artículo se resumen las configuraciones y limitaciones de compatibilidad para realizar copias de seguridad de máquinas virtuales de Azure con Azure Backup.

Otras matrices de compatibilidad:

- [Matriz de compatibilidad general](backup-support-matrix.md) para Azure Backup
- [Matriz de compatibilidad](backup-support-matrix-mabs-dpm.md) para copias de seguridad de Azure Backup Server o System Center Data Protection Manager (DPM)
- [Matriz de compatibilidad](backup-support-matrix-mars-agent.md) para la copia de seguridad con el agente de Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Escenarios admitidos

A continuación, se muestra cómo puede realizar copias de seguridad y restauraciones de máquinas virtuales de Azure con el servicio Azure Backup.

**Escenario** | **Backup** | **Agent** |**Restauración**
--- | --- | --- | ---
copia de seguridad directa de máquinas virtuales de Azure  | Copia de seguridad de toda la máquina virtual.  | No se necesita ningún agente en la máquina virtual de Azure. Azure Backup instala y usa una extensión en el [agente de máquina virtual de Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que se ejecuta en la máquina virtual. | Realice la restauración como sigue:<br/><br/> - **Creación de una máquina virtual básica**. Esto es útil si la máquina virtual no tiene ninguna configuración especial, como varias direcciones IP.<br/><br/> - **Restauración del disco de máquina virtual**. Restaure el disco. Conéctelo a una máquina virtual existente o cree una máquina virtual a partir del disco mediante PowerShell.<br/><br/> - **Sustitución del disco de máquina virtual**. Si existe una máquina virtual y esta usa discos administrados (sin cifrar), puede restaurar un disco y usarlo para reemplazar un disco existente en la máquina virtual.<br/><br/> - **Restauración de archivos y carpetas específicos**. Puede restaurar archivos o carpetas de una máquina virtual en vez de toda la máquina virtual.
Copia de seguridad directa de máquinas virtuales de Azure (solo Windows)  | Copia de seguridad de archivos, carpetas o volúmenes específicos. | Instale el [agente de Azure Recovery Services](backup-azure-file-folder-backup-faq.md).<br/><br/> Puede ejecutar al agente de MARS junto con la extensión de copia de seguridad del agente de máquina virtual de Azure para realizar copias de seguridad de la máquina virtual en el nivel de archivo o carpeta. | Restauración de archivos y carpetas específicos.
Copia de seguridad de una máquina virtual de Azure en un servidor de copia de seguridad  | Copia de seguridad de archivos, carpetas y volúmenes; archivos de estado del sistema y de reconstrucción completa; datos de aplicaciones para System Center DPM o Microsoft Azure Backup Server (MABS).<br/><br/> Después, DPM/MABS realiza una copia de seguridad en el almacén de Backup. | Instale al agente de protección de DPM/MABS en la máquina virtual. El agente de MARS se instala en DPM/MABS.| Restauración de archivos, carpetas y volúmenes; archivos de estado del sistema y de reconstrucción completa; datos de aplicaciones.

Más información sobre la copia de seguridad [mediante un servidor de copias de seguridad](backup-architecture.md#architecture-back-up-to-dpmmabs) y sobre los [requisitos de compatibilidad](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> Azure Backup admite ahora la copia de seguridad y restauración de discos selectivos mediante la solución de copia de seguridad de máquinas virtuales de Azure.
>
>En la actualidad, Azure Backup admite la copia de seguridad de todos los discos (sistema operativo y datos) en una máquina virtual junto con la solución de copia de seguridad de máquinas virtuales. Con la funcionalidad de exclusión de disco, tiene la opción de realizar una copia de seguridad de uno o de varios de los múltiples discos de datos de una máquina virtual. Esto proporciona una solución eficaz y rentable para sus necesidades de copia de seguridad y restauración. Cada punto de recuperación contiene datos de los discos incluidos en la operación de copia de seguridad, lo que permite además tener un subconjunto de discos restaurados desde el punto de recuperación determinado durante la operación de restauración. Esto se aplica a la restauración tanto desde la instantánea como desde el almacén.
>
> Esta solución es especialmente útil en los escenarios siguientes:
>  
>1. Tiene datos críticos de los que se va a realizar una copia de seguridad en un solo disco y no desea realizar copias de seguridad del resto de los discos conectados a una máquina virtual. Esto minimiza los costos de almacenamiento de copia de seguridad.  
>2. Tiene otras soluciones de copia de seguridad para parte de los datos de la máquina virtual. Por ejemplo, realiza una copia de seguridad de las bases de datos o los datos con una solución de copia de seguridad de carga de trabajo diferente y desea usar la copia de seguridad de nivel de máquina virtual de Azure para el resto de los discos y datos para crear un sistema eficaz y sólido con las mejores funcionalidades disponibles.
>
>Para suscribirse a la versión preliminar, escriba a AskAzureBackupTeam@microsoft.com.

## <a name="supported-backup-actions"></a>Acciones de copia de seguridad admitidas

**Acción** | **Soporte técnico**
--- | ---
Habilitar copia de seguridad al crear una máquina virtual de Azure con Windows | Compatible para: <br/><br/> - Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM and SP1 Standard)
Habilitar copia de seguridad al crear una máquina virtual Linux | Compatible para:<br/><br/> - Ubuntu Server: 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> - Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Copia de seguridad de una máquina virtual apagada o sin conexión | Compatible.<br/><br/> La instantánea es coherente solo con bloqueos, no con aplicaciones.
Copia de seguridad de discos después de migrar a discos administrados | Compatible.<br/><br/> La copia de seguridad seguirá funcionando. No se requiere ninguna acción.
Copia de seguridad de discos administrados después de habilitar el bloqueo del grupo de recursos | No compatible.<br/><br/> Azure Backup no puede eliminar los puntos de restauración anteriores y las copias de seguridad empezarán a generar errores cuando se alcance el límite máximo de puntos de restauración.
Modificar directiva de copia de seguridad de una máquina virtual | Compatible.<br/><br/> Se realizará la copia de seguridad de la máquina virtual con la programación y la configuración de retención de la nueva directiva. Si la configuración de retención está extendida, los puntos de recuperación existentes se marcan y se mantienen. Si se reducen, los puntos de recuperación existentes se eliminarán en el siguiente trabajo de limpieza y, después, se eliminarán por completo.
Cancelación de un trabajo de copia de seguridad| Compatible durante el proceso de instantáneas.<br/><br/> No se admite cuando se transfiere la instantánea al almacén.
Copia de seguridad de la máquina virtual en otra región o suscripción |No compatible.
Copias de seguridad por día (mediante la extensión de máquina virtual de Azure) | Una copia de seguridad programada por día.<br/><br/>El servicio de Azure Backup admite hasta nueve copias de seguridad a petición al día, pero Microsoft recomienda no más de cuatro copias de seguridad diarias a petición para garantizar el máximo rendimiento.
Copias de seguridad por día (mediante el agente de MARS) | Tres copias de seguridad programadas por día.
Copias de seguridad por día (mediante DPM/MABS) | Dos copias de seguridad programadas por día.
Copia de seguridad mensual o anual| No se admite cuando la copia de seguridad se realiza con la extensión de máquina virtual de Azure. Solo se admiten copias de seguridad diarias y semanales.<br/><br/> Puede configurar la directiva para conservar las copias de seguridad diarias y semanales durante el período de retención mensual o anual.
Ajuste automático del reloj | No compatible.<br/><br/> Azure Backup no se ajusta automáticamente a los cambios al horario de verano cuando realiza la copia de seguridad de una máquina virtual.<br/><br/>  Modifique la directiva de forma manual según sea necesario.
[Características de seguridad para copias de seguridad híbridas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |No se pueden deshabilitar las características de seguridad.
Copia de seguridad de la máquina virtual cuya hora se ha cambiado | No compatible.<br/><br/> Si se cambia la hora de la máquina a una fecha y hora futuras después de habilitar la copia de seguridad para esa máquina virtual. Incluso si se revierte el cambio horario, no se garantiza que la copia de seguridad sea correcta.  

## <a name="operating-system-support-windows"></a>Compatibilidad con sistema operativo (Windows)

En la tabla siguiente se resumen los sistemas operativos compatibles para realizar copias de seguridad de máquinas virtuales de Azure con Windows.

**Escenario** | **SO compatible**
--- | ---
Copia de seguridad con la extensión del agente de máquina virtual de Azure | - Cliente de Windows 10 (solo 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM and SP1 Standard)
Copia de seguridad con el agente de MARS | Sistemas operativos [compatibles](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Copia de seguridad con DPM/MABS | Sistemas operativos compatibles para copia de seguridad con [MABS](backup-mabs-protection-matrix.md) y [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup no admite sistemas operativos de 32 bits.

## <a name="support-for-linux-backup"></a>Compatibilidad con copia de seguridad de Linux

Esto es lo que se admite si quiere hacer copias de seguridad de máquinas Linux.

**Acción** | **Soporte técnico**
--- | ---
Copia de seguridad de máquinas virtuales de Azure con Linux mediante el agente de máquina virtual de Azure para Linux | Copia de seguridad coherente con archivos.<br/><br/> Copia de seguridad coherente con la aplicación mediante [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante la restauración, puede crear una máquina virtual, restaurar un disco y usarlo para crear una máquina virtual, o restaurar un disco y usarlo para reemplazar un disco en una máquina virtual existente. También puede restaurar archivos y carpetas individuales.
Copia de seguridad de máquinas virtuales de Azure con Linux mediante el agente de MARS | No compatible.<br/><br/> El agente MARS solo puede instalarse en máquinas Windows.
Copia de seguridad de máquinas virtuales de Azure con Linux mediante DPM/MABS | No compatible.

## <a name="operating-system-support-linux"></a>Compatibilidad con sistema operativo (Linux)

Para las copias de seguridad de máquinas virtuales de Azure con Linux, Azure Backup admite la [lista de distribuciones aprobadas por Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) para Linux. Tenga en cuenta lo siguiente:

- Azure Backup no admite Core OS Linux.
- Azure Backup no admite sistemas operativos de 32 bits.
- Otras distribuciones del tipo "traiga su propio Linux" podrían funcionar, siempre que el [agente de máquina virtual de Azure para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esté disponible en la máquina virtual y haya compatibilidad con Python.
- Azure Backup no admite máquinas virtuales Linux con proxy configurado si no tiene la versión Python 2.7 instalada.

## <a name="backup-frequency-and-retention"></a>Frecuencia y retención de copias de seguridad

**Configuración** | **Límites**
--- | ---
Puntos de recuperación máximos por instancia protegida (máquina/carga de trabajo) | 9999.
Tiempo de expiración máximo de un punto de recuperación | Ilimitado.
Frecuencia máxima de copia de seguridad en un almacén (extensión de máquina virtual de Azure) | una vez al día.
Frecuencia máxima de copia de seguridad en el almacén (agente de MARS) | Tres copias de seguridad por día.
Frecuencia máxima de copia de seguridad en DPM/MABS | Cada 15 minutos para SQL Server.<br/><br/> Una vez cada hora para otras cargas de trabajo.
Retención de punto de recuperación | Diariamente, semanalmente, mensualmente y anualmente.
Período de retención máximo | Depende de la frecuencia de la copia de seguridad.
Puntos de recuperación en disco DPM/MABS | 64 para servidores de archivos y 448 para servidores de aplicaciones.<br/><br/> Los puntos de recuperación de cinta son ilimitados para DPM en el entorno local.

## <a name="supported-restore-methods"></a>Métodos de restauración admitidos

**Método de restauración** | **Detalles**
--- | ---
Creación de una máquina virtual nueva | Puede crear una máquina virtual durante el proceso de restauración. <br/><br/> Esta opción pone en marcha una máquina virtual básica. Puede especificar el nombre de la máquina virtual, el grupo de recursos, la red virtual, la subred y el almacenamiento.  
Restauración de un disco | Puede restaurar un disco y usarlo para crear una máquina virtual.<br/><br/> Cuando se selecciona esta opción, Azure Backup copia datos desde el almacén a una cuenta de almacenamiento que seleccione. El trabajo de restauración genera una plantilla. Puede descargar esta plantilla y usarla para especificar la configuración personalizada de una máquina virtual y crear una máquina virtual.<br/><br/> Esta opción permite especificar más opciones de configuración que la opción anterior para crear una máquina virtual.<br/><br/>
Reemplazar un disco existente | Puede restaurar un disco y, después, usar el disco restaurado para reemplazar un disco que se encuentra actualmente en una máquina virtual.
Restauración de archivos | Puede recuperar archivos desde un punto de recuperación seleccionado. Descargue un script para montar el disco de máquina virtual a partir del punto de recuperación. A continuación, examine los volúmenes de disco para buscar los archivos o carpetas que desea recuperar y desmonte el disco cuando haya terminado.

## <a name="support-for-file-level-restore"></a>Compatibilidad con restauración de nivel de archivo

**Restauración** | **Compatible**
--- | ---
Restaurar archivos entre sistemas operativos | Puede restaurar archivos en cualquier máquina que tenga el mismo sistema operativo que la máquina virtual de copia de seguridad, o bien uno compatible. Consulte la [tabla de sistemas operativos compatibles](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar archivos en máquinas virtuales clásicas | No compatible.
Restaurar archivos desde máquinas virtuales cifradas | No compatible.
Restaurar archivos desde cuentas de almacenamiento con acceso restringido a la red | No compatible.
Restaurar archivos en máquinas virtuales con espacios de almacenamiento de Windows | La restauración no se admite en la misma máquina virtual.<br/><br/> En su lugar, restaure los archivos en una máquina virtual compatible.
Restaurar archivos en máquinas virtuales Linux con matrices RAID/LVM | La restauración no se admite en la misma máquina virtual.<br/><br/> Restaure en una máquina virtual compatible.
Restaurar archivos con una configuración de red especial | La restauración no se admite en la misma máquina virtual. <br/><br/> Restaure en una máquina virtual compatible.

## <a name="support-for-vm-management"></a>Compatibilidad con la administración de máquina virtual

En la tabla siguiente se resume la compatibilidad con copias de seguridad durante las tareas de administración de máquinas virtuales, como agregar o reemplazar discos de máquina virtual.

**Restauración** | **Compatible**
--- | ---
Restaurar por suscripción, región o zona. | No compatible.
Restaurar a una máquina virtual existente | Utilice la opción Reemplazar disco.
Restaurar disco con cuenta de almacenamiento habilitada para Azure Storage Service Encryption (SSE) | No compatible.<br/><br/> Restaure en una cuenta que no tenga SSE habilitado.
Restaurar en cuentas de almacenamiento mixtas |No compatible.<br/><br/> Según el tipo de cuenta de almacenamiento, todos los discos restaurados serán premium o estándar y no mixtos.
Restaurar la máquina virtual directamente en un conjunto de disponibilidad | En el caso de los discos administrados, puede restaurar el disco y usar la opción del conjunto de disponibilidad en la plantilla.<br/><br/> No se admite para discos no administrados. Para discos no administrados, restaure el disco y, después, cree una máquina virtual en el conjunto de disponibilidad.
Restaurar copia de seguridad de máquinas virtuales no administradas después de actualizar a máquinas virtuales administradas| Compatible.<br/><br/> Puede restaurar discos y después crear una máquina virtual administrada.
Restaurar máquina virtual a un punto de restauración antes de que la máquina virtual se migre a discos administrados | Compatible.<br/><br/> Restaure a discos no administrados (opción predeterminada), convierta los discos restaurados en discos administrados y cree una máquina virtual con los discos administrados.
Restaure una máquina virtual que se haya eliminado. | Compatible.<br/><br/> Puede restaurar la máquina virtual desde un punto de recuperación.
Restaurar una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio mediante el portal | Se admite si restaura el disco y crea una máquina virtual mediante PowerShell.
Restaurar una máquina virtual en una red virtual distinta |Compatible.<br/><br/> Las redes virtuales deben pertenecer a la misma suscripción y región.

## <a name="vm-compute-support"></a>Compatibilidad con proceso de máquina virtual

**Proceso** | **Soporte técnico**
--- | ---
Tamaño de VM |Cualquier tamaño de máquina virtual de Azure con al menos 2 núcleos de CPU y 1 GB de RAM<br/><br/> [Más información.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Copia de seguridad de máquinas virtuales en [conjuntos de disponibilidad](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Compatible.<br/><br/> No se puede restaurar una máquina virtual en un conjunto disponible con la opción para crear rápidamente una máquina virtual. En su lugar, cuando restaure la máquina virtual, restaure el disco y úselo para implementar una máquina virtual, o bien restaure un disco y úselo para reemplazar un disco existente.
Copia de seguridad de máquinas virtuales implementadas con la [ventaja de uso híbrido (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Compatible.
Copia de seguridad de máquinas virtuales implementadas en un [conjunto de escalado](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |No compatible.
Copia de seguridad de máquinas virtuales implementadas desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado por Microsoft y terceros) |Compatible.<br/><br/> La máquina virtual debe ejecutar un sistema operativo compatible.<br/><br/> Al recuperar archivos en la máquina virtual, puede restaurar solo en un sistema operativo compatible (no en un sistema operativo anterior ni posterior). No restauramos las máquinas virtuales de Azure Marketplace cuya copia de seguridad se ha realizado como de máquina virtual, ya que estas necesitan la información de compra, sino solo como discos.
Copia de seguridad de máquinas virtuales implementadas desde una imagen personalizada (terceros) |Compatible.<br/><br/> La máquina virtual debe ejecutar un sistema operativo compatible.<br/><br/> Al recuperar archivos en la máquina virtual, puede restaurar solo en un sistema operativo compatible (no en un sistema operativo anterior ni posterior).
Copia de seguridad de máquinas virtuales migradas a Azure| Compatible.<br/><br/> Para realizar copias de seguridad de la máquina virtual, el agente de máquina virtual debe estar instalado en la máquina migrada.
Copia de seguridad con coherencia con múltiples máquinas virtuales | Azure Backup no proporciona coherencia de datos y aplicaciones entre varias máquinas virtuales.
Copia de seguridad con [Configuración de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | No compatible. <br/><br/> Si la restauración de la máquina virtual de Azure con la configuración de diagnóstico se desencadena mediante la opción [Crear nueva](backup-azure-arm-restore-vms.md#create-a-vm), se produce un error en la restauración.
Restauración de máquinas virtuales ancladas por zona | Compatible (para máquinas virtuales cuya copia de seguridad se ha realizado después de enero de 2019 y en las que hay [zonas de disponibilidad](https://azure.microsoft.com/global-infrastructure/availability-zones/) disponibles).<br/><br/>Actualmente se admite la restauración en la misma zona que está anclada en las máquinas virtuales. Sin embargo, si la zona no está disponible, se producirá un error en la restauración.
Máquinas virtuales de Gen2 | Compatible <br> Azure Backup admite la copia de seguridad y la restauración de [máquinas virtuales de Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Cuando estas máquinas virtuales se restauran a partir del punto de recuperación, se restauran como [máquinas virtuales de Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Compatibilidad con almacenamiento de máquina virtual

**Componente** | **Soporte técnico**
--- | ---
Discos de datos de máquinas virtuales de Azure | Copia de seguridad de una máquina virtual con 16 discos de datos o menos.<BR> Para suscribirse a la versión preliminar privada de las máquinas virtuales con más de 16 discos (hasta 32 discos), escríbanos a AskAzureBackupTeam@microsoft.com
Tamaño del disco de datos | El tamaño de disco individual puede ser de hasta 32 TB y se admite un máximo de 256 TB si se combinan todos los discos de una máquina virtual.
Tipo de almacenamiento | HDD estándar, SSD estándar y SSD Premium.
Discos administrados | Compatible.
Discos cifrados | Compatible.<br/><br/> Se puede realizar una copia de seguridad (con o sin la aplicación Azure AD) de las máquinas virtuales de Azure que tengan habilitado Azure Disk Encryption.<br/><br/> Las máquinas virtuales cifradas no se pueden recuperar a nivel de archivo o carpeta. Tiene que recuperar la máquina virtual completa.<br/><br/> Puede habilitar el cifrado en máquinas virtuales que ya estén protegidas con Azure Backup.
Discos con el Acelerador de escritura habilitado | No compatible.<br/><br/> Azure Backup excluye automáticamente los discos con el acelerador de escritura habilitado durante la copia de seguridad. Como no tienen copia de seguridad, no podrá restaurar estos discos desde puntos de recuperación de la VM.
Copia de seguridad y restauración de discos y máquinas virtuales desduplicados | Azure Backup no admite la desduplicación. Para más información, consulte este [artículo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support). <br/> <br/>  - Azure Backup no se desduplica entre máquinas virtuales en el almacén de Recovery Services <br/> <br/>  - Si hay máquinas virtuales en estado de desduplicación durante la restauración, los archivos no se pueden restaurar porque el almacén no entiende el formato. Sin embargo, podrá realizar correctamente la restauración de una máquina virtual completa.
Agregar disco a una máquina virtual protegida | Compatible.
Cambiar tamaño de disco de una máquina virtual protegida | Compatible.
Almacenamiento compartido| No se admite la copia de seguridad de máquinas virtuales mediante el Volumen compartido de clúster (CSV) o el Servidor de archivos de escalabilidad horizontal. Es probable que los escritores de CSV experimenten errores durante la copia de seguridad. En la restauración, es posible que los discos que contienen volúmenes CSV no aparezcan.

## <a name="vm-network-support"></a>Compatibilidad con red de VM

**Componente** | **Soporte técnico**
--- | ---
Número de interfaces de red (NIC) | Hasta el número máximo de NIC admitidas por un tamaño específico de máquina virtual de Azure.<br/><br/> Las NIC se crean cuando se crea la máquina virtual durante el proceso de restauración.<br/><br/> El número de NIC de la máquina virtual restaurada refleja el número de NIC de la máquina virtual cuando se habilita la protección. La eliminación de NIC después de habilitar la protección no afecta al recuento.
Equilibrador de carga interno y externo |Compatible. <br/><br/> [Obtenga más información](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre cómo restaurar máquinas virtuales con una configuración de red especial.
Varias direcciones IP reservadas |Compatible. <br/><br/> [Obtenga más información](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre cómo restaurar máquinas virtuales con una configuración de red especial.
Máquinas virtuales con varios adaptadores de red| Compatible. <br/><br/> [Obtenga más información](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre cómo restaurar máquinas virtuales con una configuración de red especial.
Máquinas virtuales con direcciones IP públicas| Compatible.<br/><br/> Asocie una dirección IP pública existente a la NIC, o cree una dirección y asóciela a la NIC después de realizar la restauración.
Grupo de seguridad de red (NSG) en la NIC o la subred. |Compatible.
Dirección IP estática | No compatible.<br/><br/> A una nueva máquina virtual que se crea a partir de un punto de restauración se le asigna una dirección IP dinámica.<br/><br/> En el caso de las máquinas virtuales clásicas, no puede realizar una copia de seguridad de una máquina virtual con una dirección IP reservada y ningún punto de conexión definido.
Dirección IP dinámica |Compatible.<br/><br/> Si la NIC de la máquina virtual de origen usa una dirección IP dinámica, de forma predeterminada, la NIC de la máquina virtual restaurada también la usará.
Administrador de tráfico de Azure| Compatible.<br/><br/>Si la máquina virtual de la que se ha realizado la copia de seguridad está en Traffic Manager, agregue manualmente la máquina virtual restaurada a la misma instancia de Traffic Manager.
Azure DNS |Compatible.
DNS personalizado |Compatible.
Conectividad saliente mediante un proxy HTTP | Compatible.<br/><br/> No se admite un proxy basado autenticado.
Puntos de conexión de servicio de red virtual| Compatible.<br/><br/> La configuración del firewall y de la cuenta de almacenamiento de la red virtual deben permitir el acceso desde todas las redes.

## <a name="vm-security-and-encryption-support"></a>Compatibilidad con cifrado y seguridad de máquina virtual

Azure Backup admite el cifrado para datos en tránsito y en reposo:

Tráfico de red en Azure:

- El l tráfico de copia de seguridad de servidores al almacén de Recovery Services se cifra mediante el Estándar de cifrado avanzado 256.
- Los datos de copia de seguridad se envían a través de un vínculo HTTPS seguro.
- Los datos de copia de seguridad se almacenan en el almacén de Recovery Services en su forma cifrada.
- Solo el usuario tiene la frase de contraseña para desbloquear estos datos. Microsoft no puede descifrar los datos de copia de seguridad en ningún momento.

  > [!WARNING]
  > Después de configurar el almacén, solo el usuario tiene acceso a la clave de cifrado. Microsoft nunca mantiene una copia y no tiene acceso a la clave. Si la clave se pierde, Microsoft no puede recuperar los datos de copia de seguridad.

Seguridad de los datos:

- Cuando se realiza la copia de seguridad de máquinas virtuales de Azure, se requiere la configuración del cifrado *en* la máquina virtual.
- Azure Backup admite Azure Disk Encryption, que usa BitLocker en máquinas virtuales Windows y **dm-crypt** en máquinas virtuales Linux.
- En el back-end, Azure Backup usa el [cifrado del servicio Azure Storage](../storage/common/storage-service-encryption.md), que protege los datos en reposo.

**Máquina** | **En tránsito** | **En reposo**
--- | --- | ---
Máquinas Windows locales sin DPM/MABS | ![Sí][green] | ![Sí][green]
Máquinas virtuales de Azure | ![Sí][green] | ![Sí][green]
Máquinas virtuales de Azure o locales con DPM | ![Sí][green] | ![Sí][green]
Máquinas virtuales de Azure o locales con MABS | ![Sí][green] | ![Sí][green]

## <a name="vm-compression-support"></a>Compatibilidad de compresión de máquina virtual

Backup admite la compresión del tráfico de copia de seguridad, tal y como se resume en la siguiente tabla. Tenga en cuenta lo siguiente:

- Para las máquinas virtuales de Azure, la extensión de la máquina virtual lee directamente los datos de la cuenta de Azure Storage a través de la red de almacenamiento. No es necesario comprimir este tráfico.
- Si utiliza DPM o MABS, puede comprimir los datos antes de realizar la copia de seguridad en DPM/MABS para ahorrar ancho de banda.

**Máquina** | **Comprimir a MABS/DPM (TCP)** | **Comprimir al almacén (HTTPS)**
--- | --- | ---
Máquinas Windows locales sin DPM/MABS | N/D | ![Sí][green]
Máquinas virtuales de Azure | N/D | N/D
Máquinas virtuales de Azure o locales con DPM | ![Sí][green] | ![Sí][green]
Máquinas virtuales de Azure o locales con MABS | ![Sí][green] | ![Sí][green]

## <a name="next-steps"></a>Pasos siguientes

- [Copia de seguridad de máquinas virtuales de Azure](backup-azure-arm-vms-prepare.md).
- [Copia de seguridad directa de máquinas Windows](tutorial-backup-windows-server-to-azure.md), sin un servidor de copia de seguridad.
- [Configuración de MABS](backup-azure-microsoft-azure-backup.md) para la copia de seguridad en Azure y, luego, copia de seguridad de las cargas de trabajo en MABS.
- [Configuración de DPM](backup-azure-dpm-introduction.md) para la copia de seguridad en Azure y, luego, copia de seguridad de las cargas de trabajo en DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
