---
title: Exploración de trabajos de Azure Stream Analytics con Visual Studio Code (versión preliminar)
description: Este artículo se muestra cómo exportar un trabajo de Azure Stream Analytics a un proyecto local, enumerar los trabajos y ver las entidades de Job.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827806"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Exploración de Azure Stream Analytics con Visual Studio Code (versión preliminar)

Azure Stream Analytics para la extensión de Visual Studio Code ofrece a los desarrolladores una experiencia ligera para administrar los trabajos de Stream Analytics. Se puede usar en Windows, Mac y Linux. Con la extensión de Azure Stream Analytics puede:

- [Crear](quick-create-vs-code.md), iniciar y detener trabajos
- Exportar los trabajos existentes a un proyecto local
- Enumerar los trabajos y ver las entidades job

## <a name="export-a-job-to-a-local-project"></a>Exportar un trabajo a un proyecto local

Para exportar un trabajo a un proyecto local, busque el trabajo que desea exportar en el **explorador de Stream Analytics** en Visual Studio Code. Después seleccione una carpeta para el proyecto. El proyecto se exporta a la carpeta que seleccione, y puede seguir administrando el trabajo desde Visual Studio Code. Para más información sobre el uso de Visual Studio Code para administrar los trabajos de Stream Analytics, consulte el [inicio rápido](quick-create-vs-code.md) de Visual Studio Code.

![Exportación de un trabajo de ASA en Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Enumeración de trabajos y visualización de las entidades job

Puede usar la vista de trabajos para interactuar con trabajos de Azure Stream Analytics desde Visual Studio.


1. Haga clic en el icono de **Azure** en la barra de actividades de código de Visual Studio Code y, a continuación, expanda **nodo de Stream Analytics**. Los trabajos deben aparecer en las suscripciones.

   ![Apertura del explorador de Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda el nodo de trabajos, puede abrir y ver la consulta del trabajo, configuración, entradas, salidas y funciones. 

3. Haga clic con el botón derecho en el nodo de trabajo y elija el nodo **Open Job View in Portal** (Abrir vista de trabajo en el portal) para abrir la vista de trabajo en Azure Portal.

   ![Apertura de vista de trabajo en el portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un trabajo en la nube de Azure Stream Analytics en Visual Studio Code (versión preliminar)](quick-create-vs-code.md)
