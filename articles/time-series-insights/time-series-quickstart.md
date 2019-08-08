---
title: 'Inicio rápido: explorador de Azure Time Series Insights | Microsoft Docs'
description: En este inicio rápido se muestra cómo empezar a trabajar con el explorador de Azure Time Series Insights en el explorador web para visualizar grandes volúmenes de datos de IoT. Recorra las características clave en un entorno de demostración.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 07/29/2019
ms.openlocfilehash: 04bd4378a0820444ab31c9c696e58fccf3026d14
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720888"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Guía de inicio rápido: Exploración de Azure Time Series Insights

Este inicio rápido del explorador de Azure Time Series Insights muestra cómo empezar a trabajar con Time Series Insights en un entorno de demostración gratuito. Con él, aprenderá a usar el explorador web para visualizar grandes volúmenes de datos de IoT y recorrerá las características clave que están disponibles de forma general en la actualidad.

Azure Time Series Insights es un servicio de análisis, almacenamiento y administración totalmente administrado que facilita el análisis y la exploración de miles de millones de eventos de IoT de forma simultánea. Proporciona una visión global de los datos, con el fin de que se pueda validar rápidamente la solución de IoT y evitar el costoso tiempo de inactividad de los dispositivos críticos. Azure Time Series Insights facilita la detección de las tendencias ocultas, descubrir anomalías y realizar análisis de causa principal casi en tiempo real.

Para mayor flexibilidad, Azure Time Series Insights se puede agregar a una aplicación existente previamente mediante sus eficaces [API REST](./time-series-insights-update-tsq.md) y [SDK de cliente](./tutorial-create-tsi-sample-spa.md). Las API se pueden usar para almacenar, consultar y consumir datos de series temporales de la aplicación cliente que prefiera. También se puede usar el SDK de cliente para agregar los componentes de la interfaz de usuario a la aplicación existente.

En este inicio rápido del explorador de Time Series Insights se ofrece un recorrido guiado por las características que ya están disponibles con carácter general.

> [!IMPORTANT]
> Si no tiene ninguna, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="prepare-the-demo-environment"></a>Preparación del entorno de demostración

1. En el explorador, vaya a la [demostración de la disponibilidad general](https://insights.timeseries.azure.com/demo).

1. Si se le solicita, inicie sesión en el explorador de Time Series Insights mediante las credenciales de la cuenta de Azure.

1. Aparece una página con un recorrido rápido por Time Series Insights. Seleccione **Siguiente** para comenzar el recorrido rápido.

   [![Seleccione Siguiente](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Exploración del entorno de demostración

1. Aparece el **panel de selección de hora**. Use este panel para seleccionar un período de tiempo que se va a visualizar.

   [![Panel de selección de hora](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Seleccione un período de tiempo y arrástrelo en la región. Luego, seleccione **Buscar**.

   [![Selección de un período de tiempo](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights muestra una visualización de gráfico para el período de tiempo que especificó. Puede realizar diversas acciones en el gráfico de líneas. Por ejemplo, puede filtrar, anclar, ordenar y apilar.

   Para volver al **panel de selección de hora**, seleccione la flecha hacia abajo como se muestra a continuación:

   [![Gráfico](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Seleccione **Agregar** en el **panel de términos** para agregar un término de búsqueda nuevo.

   [![Agregar elemento](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. En el gráfico, puede seleccionar una región, hacer clic con el botón derecho en la región y seleccionar **Explorar eventos**.

   [![Explorar eventos](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Se muestra una cuadrícula de los datos sin procesar de la región que explora.

   [![Vista de cuadrícula](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Seleccionar y filtrar datos

1. Edite los términos para cambiar los valores del gráfico. Agregue otro término para establecer una correlación entre distintos tipos de valores.

   [![Agregar un término](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Escriba un término de filtro en el cuadro **Filtrar series** para el filtrado de series improvisado. En esta guía de inicio rápido, escriba **Station5** para correlacionar la temperatura y la presión de esa estación.

   [![Filtrar series](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Cuando termine la guía de inicio rápido, podrá experimentar con el conjunto de datos de ejemplo para crear visualizaciones distintas.

## <a name="clean-up-resources"></a>Limpieza de recursos

Ahora que ha completado el tutorial, limpie los recursos que ha creado:

1. En el menú de la izquierda de [Azure Portal](https://portal.azure.com), seleccione **Todos los recursos** y busque el grupo de recursos de Azure Time Series Insights.
1. Seleccione **Eliminar** para eliminar todo el grupo de recursos (y todos los recursos que contiene), o quite los recursos de uno en uno.

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para crear su propio entorno de Time Series Insights:
> [!div class="nextstepaction"]
> [Planeamiento del entorno de Time Series Insights](time-series-insights-environment-planning.md)
