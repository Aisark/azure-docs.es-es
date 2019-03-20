---
title: Arquitectura de replicación de Azure en Azure en Azure Site Recovery | Microsoft Docs
description: En este artículo se proporciona información general sobre los componentes y la arquitectura usados al configurar la recuperación ante desastres de las máquinas virtuales de Azure entre regiones de Azure, mediante el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: ef75ec40df50931f5a49c06184c61d2f78608dcf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014997"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitectura de recuperación ante desastres de Azure a Azure


En este artículo se describen la arquitectura, loc componentes y los procesos que se usan al implementar la recuperación ante desastres de máquinas virtuales de Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md). Con la configuración de la recuperación de desastres, las máquinas virtuales de Azure se replican continuamente en una región de destino diferente. Si se produce una interrupción, puede conmutar por error las máquinas virtuales en la región secundaria y acceder a ellas desde allí. Cuando todo se ejecute con normalidad de nuevo, puede realizar la conmutación por recuperación y seguir trabajando en la ubicación principal.



## <a name="architectural-components"></a>Componentes de la arquitectura

En la tabla siguiente se resumen los componentes implicados en la recuperación ante desastres para las máquinas virtuales de Azure.

**Componente** | **Requisitos**
--- | ---
**Máquinas virtuales de la región de origen** | Una de más máquinas virtuales de Azure en una [región de origen compatible](azure-to-azure-support-matrix.md#region-support).<br/><br/> Las máquinas virtuales pueden ejecutar cualquier [sistema operativo compatible](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Almacenamiento de la máquina virtual de origen** | Las máquinas virtuales de Azure se pueden administrar o pueden tener discos no administrados en cuentas de almacenamiento.<br/><br/>[Aprenda sobre](azure-to-azure-support-matrix.md#replicated-machines---storage) el almacenamiento de Azure compatible.
**Redes de máquinas virtuales de origen** | Las máquinas virtuales pueden encontrarse en una o varias subredes de una red virtual (VNet) en la región de origen. [Más información](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre los requisitos de las redes.
**Cuenta de almacenamiento en caché** | Necesita una cuenta de almacenamiento de caché en la red de origen. Durante la replicación, los cambios de la máquina virtual se almacenan en la memoria caché antes de enviarse al almacenamiento de destino.<br/><br/> Esto garantiza que las aplicaciones de producción que se ejecutan en la máquina virtual resulten mínimamente afectadas.<br/><br/> [Más información](azure-to-azure-support-matrix.md#cache-storage) acerca de los requisitos de almacenamiento en caché. 
**Recursos de destino** | Los recursos de destino se utilizan durante la replicación y al producirse una conmutación por error. Site Recovery puede configurar el recurso de destino de forma predeterminada o, si lo prefiere, puede crearlo usted o personalizarlo.<br/><br/> En la región de destino, compruebe que se pueden crear máquinas virtuales y que la suscripción tiene suficientes recursos para admitir los tamaños de máquina virtual necesarios en la región de destino. 

![Origen y destino de la replicación](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Recursos de destino

Al habilitar la replicación para una máquina virtual, Site Recovery ofrece la opción de crear automáticamente los recursos de destino. 

**Recurso de destino** | **Configuración predeterminada**
--- | ---
**Suscripción de destino** | Igual que en la suscripción de origen.
**Grupo de recursos de destino** | El grupo de recursos a los que pertenecen las máquinas virtuales después de la conmutación por error.<br/><br/> Puede encontrarse en cualquier región de Azure, excepto en la de origen.<br/><br/> Site Recovery crea un nuevo grupo de recursos en la región de destino con el sufijo "asr".<br/><br/>
**Red virtual de destino** | Red virtual en la que se encuentran las máquinas virtuales replicadas después de la conmutación por error. Se crea una asignación de red entre las redes virtuales de origen y de destino y viceversa.<br/><br/> Site Recovery crea una red virtual y una subred con el sufijo "asr".
**Cuenta de almacenamiento de destino** |  Si la máquina virtual no usa un disco administrado, esta es la cuenta de almacenamiento donde se replican los datos.<br/><br/> Site Recovery crea una cuenta de almacenamiento en la región de destino para reflejar la cuenta de almacenamiento de origen.
**Discos administrados de réplica** | Si la máquina virtual usa un disco administrado, se trata de los discos administrados donde se replican los datos.<br/><br/> Site Recovery crea discos administrados de réplica en la región de almacenamiento que reflejan la de origen.
**Conjuntos de disponibilidad de destino** |  Donde se encuentran las máquinas virtuales replicadas tras la conmutación por error.<br/><br/> Site Recovery crea un conjunto de disponibilidad en la región de destino con el sufijo "asr" para las máquinas virtuales que se encuentran en un conjunto de disponibilidad en la ubicación de origen. Si existe un conjunto de disponibilidad, se usa y no se crea otro.
**Zonas de disponibilidad de destino** | Si la región de destino admite zonas de disponibilidad, Site Recovery asigna el mismo número de zona que se use en la región de origen.

### <a name="managing-target-resources"></a>Administración de los recursos de destino

Puede administrar los recursos de destino como sigue:

- Puede modificar la configuración de destino al habilitar la replicación.
- Puede modificar la configuración de destino con la replicación en curso. La excepción es el tipo de disponibilidad (instancia, conjunto o zona únicos). Para cambiar esta configuración debe deshabilitar la replicación, modificar la configuración y volver a habilitarla.



## <a name="replication-policy"></a>Directiva de replicación 

Al habilitar la replicación de máquinas virtuales de Azure, de forma predeterminada Site Recovery crea una directiva de replicación con la configuración predeterminada que se resume en la tabla.

**Configuración de la directiva** | **Detalles** | **Valor predeterminado**
--- | --- | ---
**Retención de punto de recuperación** | Especifica el tiempo que Site Recovery conserva los puntos de recuperación | 24 horas
**Frecuencia de las instantáneas coherentes con la aplicación** | La frecuencia con la que Site Recovery toma una instantánea coherente con la aplicación. | Cada 60 minutos.

### <a name="managing-replication-policies"></a>Administración de las directivas de replicación

Puede administrar y modificar la configuración de las directivas de replicación predeterminada como sigue:
- Puede modificar la configuración al habilitar la replicación.
- Puede crear una directiva de replicación en cualquier momento y aplicarla al habilitar la replicación.

### <a name="multi-vm-consistency"></a>Coherencia con múltiples máquinas virtuales

Si desea que varias máquinas se repliquen al mismo tiempo y estas comparten puntos de recuperación coherentes frente a bloqueos y coherentes con la aplicación al conmutar por error, puede reunirlas en un grupo de replicación. La coherencia entre varias máquinas virtuales afecta al rendimiento de la carga de trabajo y solo debe usarse para las que ejecuten cargas de trabajo que necesiten coherencia en todas las máquinas. 



## <a name="snapshots-and-recovery-points"></a>Instantáneas y puntos de recuperación

Los puntos de recuperación se crean a partir de instantáneas de los discos de máquina virtual tomadas en un momento determinado. Al conmutar por error una máquina virtual se usa un punto de recuperación para restaurarla en la ubicación de destino.

Al conmutar por error, normalmente queremos garantizar que la máquina virtual empieza sin daños ni pérdida de datos y que los datos de la máquina virtual son coherentes para el sistema operativo y para las aplicaciones que se ejecutan en ella. Esto depende del tipo de instantánea realizada.

Site Recovery toma instantáneas como sigue:

1. Site Recovery toma instantáneas coherentes frente a bloqueos de datos de forma predeterminada e instantáneas coherentes con la aplicación si especifica una frecuencia para ello.
2. Los puntos de recuperación se crean a partir de las instantáneas y se almacenan según la configuración de retención establecida en la directiva de replicación.

### <a name="consistency"></a>Coherencia

La siguiente tabla explica los diferentes tipos de coherencia.

### <a name="crash-consistent"></a>Coherente frente a bloqueos

**Descripción** | **Detalles** | **Recomendación**
--- | --- | ---
Una instantánea coherente frente a bloqueos captura los datos que estaban en el disco cuando se tomó la instantánea. No incluye nada de la memoria.<br/><br/> Contiene el equivalente de los datos de disco que estuvieran presentes si se bloqueó la máquina virtual o se extrajo el cable de alimentación del servidor en el momento en que se tomara la instantánea.<br/><br/> Un punto de recuperación coherente frente a bloqueos no garantiza la coherencia de datos para el sistema operativo o las aplicaciones de la máquina virtual. | Site Recovery crea puntos de recuperación coherentes frente a bloqueos cada cinco minutos de forma predeterminada. No se puede modificar esta configuración.<br/><br/>  | Hoy en día, la mayoría de las aplicaciones se pueden recuperar bien a partir de instantáneas coherentes frente a bloqueos.<br/><br/> Los puntos de replicación coherentes frente a bloqueos suelen bastar para la replicación de sistemas operativos y aplicaciones de tipo servidor DHCP y de impresión.

### <a name="app-consistent"></a>Coherente con la aplicación

**Descripción** | **Detalles** | **Recomendación**
--- | --- | ---
Se crean puntos de recuperación coherentes con la aplicación a partir de instantáneas coherentes con la aplicación.<br/><br/> Una instantánea coherente con la aplicación contiene toda la información en una instantánea coherente frente a bloqueos, además de todos los datos en memoria y las transacciones en curso. | Las instantáneas coherentes con la aplicación usan el servicio Volume Shadow Copy (VSS):<br/><br/>   (1) Cuando se inicia una instantánea, VSS realiza una operación de copia en escritura (COW) en el volumen.<br/><br/>   (2) Antes de realizar esta operación, VSS informa a todas las aplicaciones de la máquina que deben vaciar los datos en memoria en el disco.<br/><br/>   (3) A continuación, VSS permite que la aplicación de recuperación ante desastres/copia de seguridad (en este caso, Site Recovery) lea los datos de la instantánea y continúe. | Se toman instantáneas coherentes con la aplicación de acuerdo con la frecuencia que especifique. Esta frecuencia debe ser siempre menor que la establecida para retención de los puntos de recuperación. Por ejemplo, si retiene puntos de recuperación con la configuración predeterminada de 24 horas, debe establecer la frecuencia en menos de 24 horas.<br/><br/>Son más complejas y tardan más tiempo en completarse que las instantáneas coherentes frente a bloqueos.<br/><br/> Afectan el rendimiento de las aplicaciones que se ejecutan en la máquina virtual habilitada para la replicación. 

## <a name="replication-process"></a>Proceso de replicación

Al habilitar la replicación para una máquina virtual de Azure, ocurre lo siguiente:

1. La extensión Mobility Service de Site Recovery se instala automáticamente en la máquina virtual.
2. La extensión registra la máquina virtual con Site Recovery.
3. Comienza la replicación continua para la máquina virtual.  Las escrituras en disco se transfieren inmediatamente a la cuenta de almacenamiento en caché de la ubicación de origen.
4. Site Recovery procesa los datos en la caché y los envía a la cuenta de almacenamiento de destino o a los discos administrados de réplica.
5. Una vez procesados los datos, se generan puntos de recuperación coherentes frente a bloqueos cada cinco minutos. Los puntos de recuperación coherentes con la aplicación se generan según la configuración especificada en la directiva de replicación.

![Habilitación del proceso de replicación, paso 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proceso de replicación**

## <a name="connectivity-requirements"></a>Requisitos de conectividad

 Las máquinas virtuales de Azure que replique necesitan conectividad de salida. Site Recovery no necesita nunca conectividad de entrada a la máquina virtual. 

### <a name="outbound-connectivity-urls"></a>Conectividad de salida (direcciones URL)

Si se controla el acceso de salida para las máquinas virtuales con direcciones URL, debe permitir las siguientes.

| **URL** | **Detalles** |
| ------- | ----------- |
| * .blob.core.windows.net | Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché en la región de origen. |
| login.microsoftonline.com | Proporciona autorización y autenticación de las direcciones URL del servicio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que la máquina virtual se comunique con el servicio Site Recovery. |
| *.servicebus.windows.net | Permite que la máquina virtual escriba los datos de diagnóstico y supervisión de Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Para controlar la conectividad de salida para máquinas virtuales con direcciones IP, permita estas direcciones.

#### <a name="source-region-rules"></a>Reglas de la región de origen

**Regla** |  **Detalles** | **Etiqueta de servicio**
--- | --- | --- 
Permitir HTTPS de salida: puerto 443 | Permitir rangos que correspondan a las cuentas de almacenamiento en la región de origen. | Storage.<nombre-de-la-región>.
Permitir HTTPS de salida: puerto 443 | Permitir rangos que correspondan a Azure Active Directory (Azure AD).<br/><br/> Si se agregan direcciones de Azure AD en el futuro, debe crear reglas de grupo de seguridad de red.  | AzureActiveDirectory
Permitir HTTPS de salida: puerto 443 | Permitir el acceso a los [puntos de conexión de Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondan a la ubicación de destino. 

#### <a name="target-region-rules"></a>Reglas de la región de destino

**Regla** |  **Detalles** | **Etiqueta de servicio**
--- | --- | --- 
Permitir HTTPS de salida: puerto 443 | Permitir rangos que correspondan a las cuentas de almacenamiento en la región de destino. | Storage.<nombre-de-la-región>.
Permitir HTTPS de salida: puerto 443 | Permitir rangos que correspondan a Azure AD.<br/><br/> Si se agregan direcciones de Azure AD en el futuro, debe crear reglas de grupo de seguridad de red.  | AzureActiveDirectory
Permitir HTTPS de salida: puerto 443 | Permitir el acceso a los [puntos de conexión de Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondan a la ubicación de origen. 


#### <a name="control-access-with-nsg-rules"></a>Control de acceso con reglas de grupo de seguridad de red

Si se controla la conectividad de la máquina virtual mediante el filtrado de tráfico de red hacia y desde redes o subredes de Azure con [reglas de grupo de seguridad de red](https://docs.microsoft.com/azure/virtual-network/security-overview), tenga en cuenta los siguientes requisitos:

- Las reglas de grupo de seguridad de red para la región de Azure de origen deben permitir el acceso de salida para el tráfico de replicación.
- Se recomienda crear reglas en un entorno de prueba antes de pasar a producción.
- Use [etiquetas de servicio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) en lugar de permitir direcciones IP individuales.
    - Las etiquetas de servicio representan un grupo de prefijos de direcciones IP que ayudan a reducir la complejidad de la creación de reglas de seguridad.
    - Microsoft actualiza periódica y automáticamente las etiquetas de servicio. 
 
Más información sobre la [conectividad de salida](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) para Site Recovery y el [control de la conectividad con grupos de seguridad de red](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividad para la coherencia entre varias máquinas virtuales

Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004.
- Asegúrese de que no haya ninguna aplicación de firewall que bloquee la comunicación interna entre las máquinas virtuales en el puerto 20004.
- Si desea que las máquinas virtuales de Linux formen parte de un grupo de replicación, asegúrese de que el tráfico saliente en el puerto 20004 se abra manualmente según las instrucciones de la versión específica de Linux.




## <a name="failover-process"></a>Proceso de conmutación por error

Cuando se inicia una conmutación por error, las máquinas virtuales se crean en el grupo de recursos de destino, la red virtual de destino, la subred de destino y el conjunto de disponibilidad de destino. Durante una conmutación por error, puede usar cualquier punto de recuperación.

![Proceso de conmutación por error](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Pasos siguientes

[Replicación rápida](azure-to-azure-quickstart.md) de una máquina virtual de Azure a una región secundaria.
