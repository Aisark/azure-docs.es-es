---
title: Configuración de la recuperación ante desastres para las máquinas virtuales de Azure en una región secundaria de Azure con Azure Site Recovery
description: Aprenda a configurar la recuperación ante desastres para las máquinas virtuales de Azure en una región de Azure diferente con el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5602298ffe60ad15d3daf52587c50357c310200c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480115"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configuración de la recuperación ante desastres de máquinas virtuales de Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

Este tutorial muestra cómo configurar la recuperación ante desastres en máquinas virtuales de Azure replicándolas de una región de Azure a otra. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un almacén de Recovery Services
> * Comprobación de la configuración de los recursos de destino
> * Configuración de la conectividad de red saliente para máquinas virtuales
> * Habilitación de la replicación para una máquina virtual

> [!NOTE]
> En este artículo se proporcionan instrucciones para implementar la recuperación ante desastres con la configuración más sencilla. Si desea obtener información acerca de la configuración personalizada, consulte los artículos de [esta sección](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Revise la [arquitectura del escenario y sus componentes](concepts-azure-to-azure-architecture.md).
- Revise los [requisitos de compatibilidad](site-recovery-support-matrix-azure-to-azure.md) antes de empezar.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Cree el almacén en cualquier región, excepto en la de origen.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Seleccione **Crear un recurso** > **Herramientas de administración** > **Backup and Site Recovery**.
3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione la apropiada.
4. Cree un grupo de recursos o seleccione uno existente. Especifique una región de Azure. Para comprobar las regiones admitidas, consulte la disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, después, en **Crear**.

   ![Almacén nuevo](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   El nuevo almacén se agrega al **Panel**, en **Todos los recursos**, y en la página principal **Almacenes de Recovery Services**.

## <a name="verify-target-resource-settings"></a>Comprobación de la configuración de los recursos de destino

1. Compruebe que su suscripción de Azure permite crear máquinas virtuales en la región de destino. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.
2. Asegúrese de que su suscripción tiene suficientes recursos para admitir tamaños de máquinas virtuales que se correspondan con las máquinas virtuales de origen. Site Recovery elige el mismo tamaño para la máquina virtual de destino o el más cercano posible.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configuración de la conectividad de red saliente para máquinas virtuales

Para que Site Recovery funcione de la forma esperada, debe modificar la conectividad de red de salida de las máquinas virtuales que desee replicar.

> [!NOTE]
> Site Recovery no admite el uso de un proxy de autenticación para controlar la conectividad de la red.


### <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en dirección URL para controlar la conectividad de salida, debe permitir acceder a estas direcciones URL.

| **URL** | **Detalles** |
| ------- | ----------- |
| * .blob.core.windows.net | Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché en la región de origen. |
| login.microsoftonline.com | Proporciona autorización y autenticación de las direcciones URL del servicio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que la máquina virtual se comunique con el servicio Site Recovery. |
| *.servicebus.windows.net | Permite que la máquina virtual escriba los datos de diagnóstico y supervisión de Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Si desea controlar la conectividad de salida mediante direcciones IP, en lugar de direcciones URL, permita dichas direcciones en los firewalls basados en IP, el proxy o las reglas del grupo de seguridad de red.

  - [Intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Intervalos de direcciones IP del centro de datos de Windows Azure en Alemania](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Intervalos de direcciones IP del centro de datos de Windows Azure en China](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Direcciones URL e intervalos de direcciones IP de Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Direcciones IP de puntos de conexión del servicio Site Recovery](https://aka.ms/site-recovery-public-ips)

Si usa NSG, puede crear reglas de NSG de etiquetas de servicio de almacenamiento para la región de origen. [Más información](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Comprobación de los certificados de la máquina virtual de Azure

Compruebe que las máquinas virtuales que desea replicar tienen los certificados raíz más recientes. Si nos los tienen, la máquina virtual no se puede registrar en Site Recovery debido a las restricciones de seguridad.

- Para las máquinas virtuales de Windows, instale las actualizaciones de Windows más recientes en la máquina virtual, de modo que todos los certificados raíz de confianza estén en ella. En un entorno desconectado, siga los procesos estándar de actualización de certificados y de Windows Update en su organización.
- En las máquinas virtuales Linux, para obtener los certificados raíz de confianza y la lista de revocación de certificados en la máquina virtual, siga las instrucciones proporcionadas por su distribuidor de Linux.

## <a name="set-permissions-on-the-account"></a>Obtención de permisos en la cuenta

Azure Site Recovery proporciona tres roles integrados para controlar las operaciones de administración de Site Recovery.

- **Colaborador de Site Recovery**: este rol tiene todos los permisos necesarios para administrar las operaciones de Azure Site Recovery en un almacén de Recovery Services. Sin embargo, un usuario con este rol no puede crear ni eliminar un almacén de Recovery Services, ni tampoco asignar derechos de acceso a otros usuarios. Este rol es ideal para administradores de recuperación ante desastres que pueden habilitar y administrar la recuperación ante desastres para aplicaciones u organizaciones completas.

- **Operador de Site Recovery**: este rol tiene permisos para ejecutar y administrar operaciones de conmutación por error y por recuperación. Un usuario con este rol no puede habilitar ni deshabilitar la replicación, crear ni eliminar almacenes, registrar una nueva infraestructura ni asignar derechos de acceso a otros usuarios. Este rol es ideal para un operador de recuperación ante desastres que puede conmutar por error desde máquinas virtuales o aplicaciones cuando se lo indican los propietarios de las aplicaciones y los administradores de TI. Tras la resolución del desastre, el operador de recuperación ante desastres puede volver a proteger las máquinas virtuales y realizar la conmutación por recuperación.

- **Lector de Site Recovery**: este rol tiene permisos para ver todas las operaciones de administración de Site Recovery. Este rol es ideal para un ejecutivo de supervisión de TI que puede supervisar el estado actual de protección y crear vales de soporte.

Obtenga más información sobre los [roles integrados de Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Habilitación de la replicación para una máquina virtual

### <a name="select-the-source"></a>Seleccione el origen

1. En los almacenes de Recovery Services, haga clic en el nombre del almacén y > **+Replicar**.
2. En **Origen**, seleccione **Azure**.
3. En **Ubicación de origen**, seleccione la región de Azure de origen donde se ejecutan actualmente sus máquinas virtuales.
4. Seleccione la **suscripción de origen** donde se ejecutan las máquinas virtuales. Puede tratarse de cualquier suscripción dentro del mismo inquilino de Azure Active Directory donde exista el almacén de Recovery Services.
5. Seleccione el **grupo de recursos de origen** y haga clic en **Aceptar** para guardar la configuración.

    ![Configurar origen](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Seleccione las máquinas virtuales

Site Recovery recupera una lista de las máquinas virtuales asociadas a la suscripción y el servicio en la nube/grupo de recursos.

1. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere replicar.
2. Haga clic en **OK**.

### <a name="configure-replication-settings"></a>Configuración de las opciones de replicación

Site Recovery crea la configuración predeterminada y la directiva de replicación para la región de destino. La configuración se puede cambiar cuando sea necesario.

1. Haga clic en **Configuración** para ver la configuración de destino y de replicación.
2. Para invalidar la configuración de destino predeterminada, haga clic en **Personalizar** junto a **Grupo de recursos, red, almacenamiento y disponibilidad**.

   ![Definición de la configuración](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personalice la configuración del destino como se resume en la tabla.

    **Configuración** | **Detalles**
    --- | ---
    **Suscripción de destino** | De forma predeterminada, la suscripción de destino será la misma que la de origen. Haga clic en "Personalizar" para seleccionar una suscripción de destino diferente dentro del mismo inquilino de Azure Active Directory.
    **Ubicación de destino** | la región de destino que se usa para la recuperación ante desastres.<br/><br/> Se recomienda que la ubicación de destino coincida con la ubicación del almacén de Site Recovery.
    **Grupo de recursos de destino** | el grupo de recursos en la región de destino que contiene las máquinas virtuales de Azure después de la conmutación por error.<br/><br/> De forma predeterminada, Site Recovery crea un nuevo grupo de recursos en la región de destino con un sufijo "asr". La ubicación del grupo de recursos de destino puede ser cualquier región, excepto la región en la que se hospedan las máquinas virtuales de origen.
    **Red virtual de destino** | la red en la región de destino en la que las máquinas virtuales se encuentran después de la conmutación por error.<br/><br/> De forma predeterminada, Site Recovery crea una nueva red (y subredes) virtual en la región de destino con un sufijo "asr".
    **Cuentas de almacenamiento en caché** | Site Recovery utiliza una cuenta de almacenamiento en la región de origen. Los cambios en las máquinas virtuales de origen se envían a esta cuenta, antes de la replicación en la ubicación de destino.<br/><br/> Si usa la cuenta de almacenamiento en caché con firewall habilitado, asegúrese de habilitar **Permitir servicios de Microsoft de confianza**. [Más información.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Cuentas de almacenamiento de destino (si la máquina virtual de origen no usa discos administrados)** | de forma predeterminada, Site Recovery crea una nueva cuenta de almacenamiento en la región de destino para reflejar la cuenta de almacenamiento de la máquina virtual de origen.<br/><br/> Si usa la cuenta de almacenamiento en caché con firewall habilitado, habilite **Permitir servicios de Microsoft de confianza**.
    **Discos administrados de réplica (si la máquina virtual de origen usa discos administrados)** | de forma predeterminada, Site Recovery crea discos administrados de réplica en la región de destino para reflejar los discos administrados de la máquina virtual de origen con el mismo tipo de almacenamiento (Standard o Premium) que el disco administrado de la máquina virtual de origen. Solo se puede personalizar el tipo de disco 
    **Conjuntos de disponibilidad de destino** | de forma predeterminada, Azure Site Recovery crea un conjunto de disponibilidad en la región de destino con un nombre con el sufijo "asr" para la parte de máquina virtual de un conjunto de disponibilidad de la región de origen. En caso de que el conjunto de disponibilidad que cree Azure Site Recovery ya exista, se vuelve a usar.
    **Zonas de disponibilidad de destino** | de forma predeterminada, Site Recovery asigna el mismo número de zona que la región de origen en la región de destino si esta admite zonas de disponibilidad.<br/><br/> Si la región de destino no admite zonas de disponibilidad, las máquinas virtuales de destino se configurarán como instancias únicas de forma predeterminada.<br/><br/> Haga clic en **Personalizar** para configurar las máquinas virtuales como parte de un conjunto de disponibilidad en la región de destino.<br/><br/> No puede cambiar el tipo de disponibilidad (instancia única, zona de disponibilidad o conjunto de disponibilidad) después de habilitar la replicación. Tendrá que deshabilitar y habilitar la replicación para cambiar el tipo de disponibilidad.

4. Para personalizar la configuración de la directiva de replicación, haga clic en **Personalizar** junto a **Directiva de replicación** y modifique los siguientes valores según sea necesario.

    **Configuración** | **Detalles**
    --- | ---
    **Nombre de la directiva de replicación** | nombre de la directiva.
    **Retención de punto de recuperación** | de forma predeterminada, Site Recovery conserva los puntos de recuperación durante 24 horas. Puede configurar un valor entre 1 y 72 horas.
    **Frecuencia de las instantáneas coherentes con la aplicación** | de forma predeterminada, Site Recovery toma una instantánea coherente con la aplicación cada 4 horas. Puede configurar cualquier valor entre 1 y 12 horas.<br/><br/> Una instantánea coherente con la aplicación es una instantánea en un momento dado de los datos de la aplicación dentro de la máquina virtual. El Servicio de instantáneas de volumen (VSS) garantiza que la aplicación en la máquina virtual se encuentre en un estado coherente cuando se toma la instantánea.
    **Grupo de replicación** | si la aplicación necesita coherencia de múltiples máquinas virtuales entre varias máquinas virtuales, puede crear un grupo de replicación para estas máquinas virtuales. De forma predeterminada, las máquinas virtuales seleccionadas no forman parte de ningún grupo de replicación.

5. En **Personalizar**, seleccione **Sí** para lograr coherencia entre varias máquinas virtuales si desea agregar máquinas virtuales a un grupo de replicación nuevo o existente. A continuación, haga clic en **Aceptar**. 

    >[!NOTE]
    >- Todas las máquinas de un grupo de replicación tendrán puntos de recuperación compartidos coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error.
    >- La habilitación de la coherencia entre varias VM puede afectar al rendimiento de la carga de trabajo (consume mucha CPU). Debe usarse únicamente si las máquinas ejecutan la misma carga de trabajo y necesita coherencia entre varias máquinas.
    >- Puede tener un máximo de 16 máquinas virtuales en un grupo de replicación.
    >- Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. Asegúrese de que no haya ningún firewall que bloquee la comunicación interna entre las máquinas virtuales en este puerto.
    >- Para las máquinas virtuales de Linux de un grupo de replicación, asegúrese de que el tráfico saliente en el puerto 20004 se abra manualmente según la guía de la versión de Linux.



### <a name="configure-encryption-settings"></a>Configuración de los valores del cifrado

Si la máquina virtual de origen tiene habilitado Azure Disk Encryption (ADE), revise la configuración.

1. Compruebe la configuración:
    - **Almacenes de claves de cifrado de disco**: De forma predeterminada, Site Recovery crea un almacén de claves en las claves de cifrado de disco de la máquina virtual de origen con un sufijo "asr". Si ya existe el almacén de claves, se vuelve a usar.
    - **Almacenes de claves de cifrado de claves**: De forma predeterminada, Site Recovery crea un nuevo almacén de claves en la región de destino. El nombre tiene un sufijo "asr" y se basa en las claves de cifrado de claves de la máquina virtual de origen. Si el almacén de claves que ha creado Site Recovery ya existe, se vuelve a usar.

2. Haga clic en **Personalizar** para seleccionar los almacenes de claves personalizados.

>[!NOTE]
>Actualmente, Azure Site Recovery solo admite máquinas virtuales Azure con sistemas operativos Windows y que estén [habilitadas para el cifrado con la aplicación de Azure AD](https://aka.ms/ade-aad-app).
>

### <a name="track-replication-status"></a>Seguimiento del estado de replicación

1. En **Configuración**, haga clic en **Actualizar** para obtener el estado más reciente.
2. Realice un seguimiento de estado y progreso como se indica a continuación:
    - Realice un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.
    - En **Configuración** > **Elementos replicados**, puede ver el estado de las máquinas virtuales y el progreso inicial de la replicación. Haga clic en la máquina virtual para ir a los detalles de su configuración.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se configuró la recuperación ante desastres para una máquina virtual de Azure. Ya puede iniciar la exploración de la recuperación ante desastres para comprobar que la conmutación por error funciona como cabría esperar.

> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](azure-to-azure-tutorial-dr-drill.md)
