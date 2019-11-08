---
title: Configuración de la recuperación ante desastres de una máquina virtual de Azure IaaS en una región secundaria de Azure
description: Esta guía de inicio rápido proporciona los pasos necesarios para la recuperación ante desastres de una máquina virtual de Azure IaaS entre regiones de Azure mediante el servicio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b06cae5bcb07831ba79b805802a7851c2b6ad2b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467972"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configuración de la recuperación ante desastres en una región secundaria de Azure de una máquina virtual de Azure        

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, lo que incluye la replicación, la conmutación por error y la recuperación.

En este inicio rápido se describe cómo configurar la recuperación ante desastres en una máquina virtual de Azure replicándola a otra región de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!NOTE]
> Este artículo es un tutorial rápido para nuevos usuarios. Usa la ruta más sencilla, con las opciones predeterminadas y la mínima personalización.  Para ver un tutorial más completo, consulte [el nuestro](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Habilitación de la replicación para la máquina virtual de Azure

1. En el menú de Azure Portal, seleccione **Máquinas virtuales** o busque y seleccione *Máquinas virtuales* en cualquier página. Seleccione la máquina virtual que quiere replicar.
2. En **Operaciones**, seleccione **Recuperación ante desastres**.
3. En **Configurar recuperación ante desastres** > **Región de destino**, seleccione la región de destino en la que quiere realizar la replicación.
4. En esta guía de inicio rápido, acepte los restantes valores predeterminados.
5. Seleccione **Revisar e iniciar replicación**. Después, seleccione **Iniciar replicación** para iniciar un trabajo y habilitar la replicación de la máquina virtual.

    ![habilitar replicación](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Comprobación de la configuración

Cuando haya finalizado el trabajo de replicación, puede comprobar el estado de replicación, modificar la configuración de replicación y probar la implementación.

1. En el menú de Azure Portal, seleccione **Máquinas virtuales** o busque y seleccione *Máquinas virtuales* en cualquier página. Seleccione la máquina virtual que quiera comprobar.
2. En **Operaciones**, seleccione **Recuperación ante desastres**.

   Puede comprobar el estado de replicación, los puntos de recuperación que se han creado y las regiones de origen y destino en el mapa.

   ![Estado de replicación](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

La máquina virtual de la región primaria deja de replicar al deshabilitar la replicación:

- La configuración de replicación de origen se limpia automáticamente. La extensión de Site Recovery que se instala en la máquina virtual como parte de la replicación no se ha eliminado y tiene que quitarse manualmente. 
- La facturación de Site Recovery para la máquina virtual se detiene.

Detenga la replicación como se indica a continuación

1. En el menú de Azure Portal, seleccione **Máquinas virtuales** o busque y seleccione *Máquinas virtuales* en cualquier página. Seleccione la máquina virtual que quiera modificar.
2. En **Recuperación ante desastres**, haga clic en **Deshabilitar replicación**.

   ![Deshabilitar replicación](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, se replica una única máquina virtual en una región secundaria. Ahora, pruebe a replicar varias máquinas virtuales de Azure con un plan de recuperación.

> [!div class="nextstepaction"]
> [Configuración de la recuperación ante desastres para las máquinas virtuales de Azure](azure-to-azure-tutorial-enable-replication.md)
