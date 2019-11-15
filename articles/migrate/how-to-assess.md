---
title: Incorporación de herramientas de valoración en Azure Migrate | Microsoft Docs
description: En este artículo se describe cómo agregar herramientas de valoración en el centro de Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 8d13222ec05fc97fa591ac67d636a1f8c5c88845
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715857"
---
# <a name="add-assessment-tools"></a>Incorporación de herramientas de evaluación

En este artículo se describe cómo agregar herramientas de valoración en [Azure Migrate](migrate-overview.md).

Azure Migrate proporciona un centro de herramientas para la valoración y la migración a Azure. Incluye herramientas de Azure Migrate, así como otras herramientas y ofertas de fabricantes de software independientes (ISV).

Si quiere agregar una herramienta de valoración y todavía no tiene un proyecto de Azure Migrate, consulte este [artículo](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Seleccionar una herramienta

Si elige una herramienta que no sea de Azure Migrate para la valoración, empiece por obtener una licencia o regístrese para una evaluación gratuita, de acuerdo con la directiva de la herramienta. Las herramientas tienen una opción para conectarse a Azure Migrate. Siga las instrucciones y la documentación para conectar la herramienta con Azure Migrate. [Más información](migrate-services-overview.md) sobre las herramientas.


## <a name="select-an-assessment-scenario"></a>Selección de un escenario de valoración

1. En el proyecto de Azure Migrate, haga clic en **Información general**.
2. Seleccione el escenario de valoración que quiere usar:

    - Para detectar y evaluar máquinas y cargas de trabajo para la migración a Azure, seleccione **Evaluar y migrar servidores**.
    - Para evaluar máquinas SQL locales, seleccione **Evaluar y migrar bases de datos**.
    - Para evaluar aplicaciones web locales, seleccione **Evaluar y migrar aplicaciones web**.

    ![Escenario de valoración](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selección de una herramienta de valoración de servidores 

1. Haga clic en **Evaluar y migrar servidores**.
2. En **Azure Migrate - Servidores**, si no ha agregado una herramienta de valoración, vaya a **Herramientas de evaluación** y seleccione **Click here to add an assessment tool** (Haga clic aquí para agregar una herramienta de evaluación). Si ya ha agregado herramientas, en **¿Quiere agregar más herramientas de evaluación?** , seleccione **Cambiar**.

    > [!NOTE]
    > Si necesita desplazarse a otro proyecto, en **Azure Migrate - Servidores**, junto a **¿Quiere ver los detalles de un proyecto de migración diferente?** , seleccione **Haga clic aquí**.

3. En **Azure Migrate**, seleccione la herramienta de valoración que quiera usar.

    - Si usa Azure Migrate Server Assessment, puede configurar, ejecutar y ver las valoraciones directamente en el proyecto de Azure Migrate.
    - Si usa una herramienta de valoración distinta, vaya al vínculo proporcionado para el sitio y ejecute la valoración de acuerdo con las instrucciones que se proporcionan.


## <a name="select-a-database-assessment-tool"></a>Selección de una herramienta de valoración de base de datos

1. Haga clic en **Evaluar y migrar bases de datos**.
2. En **Bases de datos**, haga clic en **Agregar herramientas**.
3. En Agregar una herramienta > **Seleccione una herramienta de evaluación**, elija la herramienta que quiere usar para evaluar la base de datos.

## <a name="select-a-web-app-assessment-tool"></a>Selección de una herramienta de valoración de aplicaciones web

1. Haga clic en **Evaluar y migrar aplicaciones web**.
2. Siga el vínculo a la herramienta de migración para Azure App Service. Use la herramienta de migración para:

    - **Evaluar aplicaciones web en línea**: puede evaluar las aplicaciones con una dirección URL pública en línea mediante Migration Assistant de Azure App Service.
    - **.NET/PHP**: para las aplicaciones .NET y PHP internas, puede descargar y ejecutar Migration Assistant.



## <a name="next-steps"></a>Pasos siguientes

Pruebe una evaluación con Azure Migrate Server Assessment para máquinas virtuales de [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md) o [servidores físicos](tutorial-prepare-physical.md).
