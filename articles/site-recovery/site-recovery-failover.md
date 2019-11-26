---
title: Conmutación por error durante la recuperación ante desastres con Azure Site Recovery
description: Aprenda sobre la conmutación por error de máquinas virtuales y servidores físicos durante la recuperación ante desastres con el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2019
ms.author: raynew
ms.openlocfilehash: 1585c5dbdecf11bbc6ef3dad63bf4f982c70f73e
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053769"
---
# <a name="fail-over-vms-and-physical-servers"></a>Conmutación por error de máquinas virtuales y servidores físicos 

En este artículo se describe cómo se realiza la conmutación por error en máquinas virtuales y en servidores físicos protegidos con Site Recovery.

## <a name="prerequisites"></a>Requisitos previos
1. Antes de realizar una conmutación por error, realice una [conmutación por error de prueba](site-recovery-test-failover-to-azure.md) para asegurarse de que todo funciona según lo previsto.
1. [Prepare la red](site-recovery-network-design.md) en la ubicación de destino antes de realizar una conmutación por error.  

Consulte en la siguiente tabla las opciones de conmutación por error proporcionadas por Azure Site Recovery. Estas opciones también se muestran para distintos escenarios de conmutación por error.

| Escenario | Requisito de recuperación de la aplicación | Flujo de trabajo para Hyper-V | Flujo de trabajo para VMware
|---|--|--|--|
|Conmutación por error planeada debido a que el centro de datos estará fuera de servicio próximamente| Cuando se realiza una actividad planeada, no se pierden datos de aplicación| En Hyper-V, ASR replica los datos con una frecuencia de copia especificada por el usuario. La conmutación por error planeada se usa para anular la frecuencia y replicar los cambios finales antes de que se inicie una conmutación por error. <br/> <br/> 1. Planifique un período de mantenimiento de acuerdo con el proceso de administración de cambios de su empresa. <br/><br/> 2. Notifique a los usuarios el tiempo de inactividad programado. <br/><br/> 3. Desconecte la aplicación orientada a los usuarios.<br/><br/>4. Inicie la conmutación por error planeada mediante el portal de ASR. La máquina virtual local se apagará automáticamente.<br/><br/>Pérdida efectiva de datos de aplicación = 0 <br/><br/>También se proporciona un diario de puntos de recuperación en un período de retención para los usuarios que deseen utilizar un punto de recuperación anterior. (Retención de 24 horas para Hyper-V). Si se ha detenido la replicación más allá del período de tiempo de la ventana de retención, los clientes aún pueden realizar la conmutación por error mediante los puntos de recuperación disponibles más recientes. | Para VMware, ASR replica los datos continuamente mediante CDP. La conmutación por error proporciona al usuario la opción de conmutar por error a los datos más recientes (incluso a los posteriores a la desconexión de la aplicación).<br/><br/> 1. Planifique un período de mantenimiento de acuerdo con el proceso de administración de cambios. <br/><br/>2. Notifique a los usuarios el tiempo de inactividad programado. <br/><br/>3. Desconecte la aplicación orientada a los usuarios.<br/><br/>4. Inicie una conmutación por error planeada mediante el portal de ASR para el punto más reciente posterior a la desconexión de la aplicación. Utilice la opción "Conmutación por error planeada" en el portal y seleccione el punto más reciente al que conmutar por error. La máquina virtual local se apagará automáticamente.<br/><br/>Pérdida efectiva de datos de aplicación = 0 <br/><br/>Se proporciona un diario de puntos de recuperación en un período de retención para los clientes que deseen utilizar un punto de recuperación anterior. (72 horas de retención para VMware). Si se ha detenido la replicación más allá del período de tiempo de la ventana de retención, los clientes aún pueden realizar la conmutación por error mediante los puntos de recuperación disponibles más recientes.
|Conmutación por error debida a un tiempo de inactividad del centro de datos no planeado (desastre natural o informático) | Pérdida mínima de datos de la aplicación | 1. Inicie el plan BCP de la organización. <br/><br/>2. Inicie la conmutación por error no planeada mediante el portal de ASR al punto más reciente o a otro punto del período de retención (diario).| 1. Inicie el plan BCP de la organización. <br/><br/>2. Inicie la conmutación por error no planeada mediante el portal de ASR al punto más reciente o a otro punto del período de retención (diario).


## <a name="run-a-failover"></a>Ejecución de la conmutación por error
En este procedimiento se describe cómo ejecutar una conmutación por error para un [plan de recuperación](site-recovery-create-recovery-plans.md). También puede ejecutar la conmutación por error para una única máquina virtual o un único servidor físico desde la página **Elementos replicados**, tal como se describe [aquí](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Conmutación por error](./media/site-recovery-failover/Failover.png)

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error**.
2. En la pantalla **Conmutación por error**, seleccione el **Punto de recuperación** al que se va a realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
   1. **Más reciente**: para iniciar el trabajo, esta opción procesa primero todos los datos que se han enviado al servicio de Site Recovery. El procesamiento de los datos crea un punto de recuperación para cada máquina virtual. La máquina virtual usa este punto de recuperación durante la conmutación por error. Esta opción ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual creada después de la conmutación por error tiene todos los datos que se han replicado en el servicio Site Recovery cuando se desencadenó la conmutación por error.
   1. **Procesado más recientemente**: esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al punto de recuperación más reciente que el servicio Site Recovery haya procesado. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información. Como no se emplea ningún tiempo en procesar los datos sin procesar, esta es una opción de conmutación por error con un objetivo de tiempo de recuperación bajo.
   1. **Más reciente coherente con la aplicación**: esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al último punto de recuperación coherente con la aplicación que ya haya procesado el servicio Site Recovery. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación coherente con la aplicación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información.
   1. **Último procesamiento de máquinas virtuales múltiples**: esta opción solo está disponible para planes de recuperación que tienen al menos una máquina virtual con la coherencia para varias máquinas virtuales activada. Las máquinas virtuales que forman parte de un grupo de replicación conmutan por error al último punto de recuperación común coherente con varias máquinas virtuales. Otras máquinas virtuales conmutan por error a su punto de recuperación procesado más reciente.  
   1. **Último coherente con aplicación de múltiples VM**: esta opción solo está disponible para planes de recuperación que tienen al menos una máquina virtual con la coherencia para varias máquinas virtuales activada. Las máquinas virtuales que forman parte de un grupo de replicación conmutan por error al último punto de recuperación común coherente con la aplicación de varias máquinas virtuales. Otras máquinas virtuales conmutan por error a su punto de recuperación coherente con la aplicación más reciente.
   1. **Personalizado**: si va a realizar una conmutación por error de prueba de una máquina virtual, puede usar esta opción para conmutar por error a un punto de recuperación determinado.

      > [!NOTE]
      > La opción de elegir un punto de recuperación solo está disponible cuando se conmuta por error a Azure.
      >
      >


1. Si algunas de las máquinas virtuales del plan de recuperación se han conmutado por error en una ejecución anterior y ahora están activas en la ubicación de origen y en la de destino, puede usar la opción **Cambiar dirección** para decidir la dirección en la que debe producirse la conmutación por error.
1. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos (aplicable únicamente con máquinas virtuales de Hyper-V desde un servidor de VMM), en **Clave de cifrado**, seleccione el certificado que se emitió al habilitarlo durante la instalación del proveedor en el servidor de VMM.
1. Seleccione **Apague la máquina antes de comenzar con la conmutación por error** si quiere que Site Recovery intente apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque el apagado no se produzca.  

    > [!NOTE]
    > Si las máquinas virtuales de Hyper-V están protegidas, la opción de apagado también intenta sincronizar los datos locales que aún no se hayan enviado al servicio antes de desencadenar la conmutación por error.
    >
    >

1. Puede seguir el progreso de la conmutación por error en la página **Trabajos**. Aunque se produzcan errores durante una conmutación por error no planeada, el plan de recuperación se ejecuta hasta que se completa.
1. Después de la conmutación por error, valide la máquina virtual iniciando sesión en ella. Si quiere volver a otro punto de recuperación de la máquina virtual, puede usar la opción **Cambiar punto de recuperación**.
1. Realice la acción **Confirmar** una vez que quede satisfecho con la conmutación por error de la máquina virtual. **Así elimina todos los puntos de recuperación disponibles con el servicio**  y la opción **Cambiar punto de recuperación** dejará de estar disponible.

## <a name="planned-failover"></a>Conmutación por error planeada
Las máquinas virtuales y los servidores físicos protegidos con Site Recovery también admiten la **conmutación por error planeada**. La conmutación por error planeada es una opción de conmutación por error sin pérdida de datos. Cuando se desencadena una conmutación por error planeada, primero se apagan las máquinas virtuales de origen, después se sincronizan los últimos datos y, por último, se desencadena la conmutación por error.

> [!NOTE]
> Durante la conmutación por error de máquinas virtuales de Hyper-V de un sitio local a otro sitio local, para regresar al sitio local principal local, debe realizar primero una **replicación inversa** de la máquina virtual en el sitio principal y luego desencadenar la conmutación por error. Si la máquina virtual principal no está disponible, antes de iniciar la **replicación inversa** tiene que restaurar la máquina virtual desde una copia de seguridad.   
 
 
## <a name="failover-job"></a>Trabajo de conmutación por error

![Conmutación por error](./media/site-recovery-failover/FailoverJob.png)

Cuando se desencadena una conmutación por error, se realizan estos pasos:

1. Comprobación de requisitos previos: en este paso se garantiza que se cumplen todas las condiciones necesarias para la conmutación por error.
1. Conmutación por error: en este paso se procesan los datos y se preparan para que se pueda crear una máquina virtual de Azure a partir de ellos. Si ha elegido el punto de recuperación **más reciente**, en este paso se crea un punto de recuperación de los datos enviados al servicio.
1. Inicio: en este paso se crea una máquina virtual de Azure con los datos procesados en el paso anterior.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de inicia la conmutación por error, se detiene la replicación de la máquina virtual. Si se **cancela** un trabajo en curso, la conmutación por error se detiene, pero la máquina virtual no comenzará la replicación. La replicación no se puede reiniciar.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tiempo necesario para la conmutación por error en Azure

En algunos casos, la conmutación por error de máquinas virtuales requiere un paso intermedio adicional que normalmente tarda aproximadamente de 8 a 10 minutos en completarse. En los casos siguientes, el tiempo necesario para la conmutación por error será mayor de lo habitual:

* Máquinas virtuales de VMware con el servicio de movilidad de una versión anterior a 9.8
* Servidores físicos
* Máquinas virtuales de VMware Linux
* Máquinas virtuales Hyper-V protegidas como servidores físicos
* Máquinas virtuales de VMware donde los siguientes controladores no están presentes como controladores de arranque
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Máquinas virtuales de VMware que no tienen el servicio DHCP habilitado independientemente de si usan direcciones IP estáticas o DHCP

En los demás casos, no es necesario este paso intermedio y el tiempo necesario para la conmutación por error es menor.

## <a name="using-scripts-in-failover"></a>Uso de scripts en la conmutación por error
Es posible que desee automatizar determinadas acciones de la conmutación por error. Para ello, puede utilizar scripts o [runbooks de Azure Automation](site-recovery-runbook-automation.md) en [planes de recuperación](site-recovery-create-recovery-plans.md).

## <a name="post-failover-considerations"></a>Consideraciones posteriores a la conmutación por error
Conviene tener en cuenta las siguientes recomendaciones tras la conmutación por error:
### <a name="retaining-drive-letter-after-failover"></a>Conservación de la letra de unidad después de la conmutación por error
Azure Site Recovery controla la retención de letras de unidad. [Más información](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) sobre el procedimiento para excluir algunos discos.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Si desea conectarse a máquinas virtuales de Azure mediante RDP/SSH después de la conmutación por error, siga los requisitos resumidos en [esta](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabla.

Siga los pasos descritos [aquí](site-recovery-failover-to-azure-troubleshoot.md) para solucionar problemas de conectividad tras la conmutación por error.


## <a name="next-steps"></a>Pasos siguientes

> [!WARNING]
> Una vez que finalizada la conmutación por error de las máquinas virtuales y cuando el centro de datos local esté disponible, debe [**volver a proteger**](vmware-azure-reprotect.md) las máquinas virtuales de VMware en el centro de datos local.

Use la opción de[**conmutación por error planeada**](hyper-v-azure-failback.md) para la **conmutación por recuperación** de máquinas virtuales de Hyper-V hacia el entorno local desde Azure.

Si se ha producido una conmutación por error de una máquina virtual de Hyper-V a otro centro de datos local administrado por un servidor de VMM y el centro de datos principal está disponible, use la **replicación inversa** para iniciar la replicación hacia el centro de datos principal.
