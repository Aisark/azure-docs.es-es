---
title: Ejecución de una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región secundaria de Azure con el servicio Azure Site Recovery
description: Obtenga información sobre cómo ejecutar una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región de Azure secundaria mediante el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 16c791287cc50b5ac8992a86f6de1e3eeb5e329e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260279"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Ejecución de una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región secundaria de Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, lo que incluye la replicación, la conmutación por error y la recuperación.

En este tutorial se muestra cómo ejecutar una exploración de recuperación ante desastres en una máquina virtual de Azure, desde una región de Azure a otra, con una conmutación por error de prueba. Mediante una exploración se valida su estrategia de replicación sin ocasionar ninguna pérdida de datos ni tiempo de inactividad, y sin afectar al entorno de producción. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar los requisitos previos
> * Ejecutar una conmutación por error de prueba en una sola máquina virtual

> [!NOTE]
> Este tutorial está diseñado para guiar al usuario en los pasos para habilitar una exploración DR con los pasos mínimos; en caso de que desee obtener más información acerca de los diversos aspectos asociados con la realización de una exploración DR, incluidas las consideraciones de red, la automatización o la solución de problemas, consulte los documentos de procedimientos para las máquinas virtuales de Azure.

## <a name="prerequisites"></a>Requisitos previos

- Antes de ejecutar una conmutación por error de prueba, se recomienda que compruebe las propiedades de la máquina virtual para asegurarse de que todo se ajusta a lo esperado.  Acceda a las propiedades de la máquina virtual en **Elementos replicados**. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.
- **Se recomienda usar una red de máquina virtual de Azure independiente en la conmutación por error de prueba** en lugar de la red predeterminada que se configuró cuando habilitó la replicación.


## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. En **Configuración** > **Elementos replicados**, haga clic en la VM > icono **+Probar conmutación por error**.

2. En **Conmutación por error**, seleccione el punto de recuperación que usar para la conmutación por error:

   - **Procesado más recientemente**: error de la VM en el último punto de recuperación procesado por el servicio Site Recovery. Se muestra la marca de tiempo. Con esta opción, no se emplea tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
   - **Más reciente coherente con la aplicación**: esta opción conmuta por error todas las VM en el punto de recuperación más reciente coherente con la aplicación. Se muestra la marca de tiempo.
   - **Personalizado**: seleccione un punto de recuperación.

3. Seleccione la red virtual de Azure de destino a la se conectarán las VM de Azure en la región secundaria después de que se produzca la conmutación por error.

4. Para iniciar la conmutación por error, haga clic en **Aceptar**. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Conmutación por error de prueba** en el nombre del almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.
5. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Asegúrese de que la máquina virtual está en funcionamiento, tiene el tamaño adecuado y está conectada a la red apropiada.
6. Para eliminar las máquinas virtuales que se crearon durante la conmutación por error de prueba, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el artículo replicado o el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ejecutar una conmutación por error de producción](azure-to-azure-tutorial-failover-failback.md)
