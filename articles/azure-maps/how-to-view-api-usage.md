---
title: Procedimiento para ver el uso de la API de Azure Maps | Microsoft Docs
description: Aprenda a ver las métricas de las llamadas de la API de Azure Maps en el portal.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d14088ed940ab83be29756a26f8612704bb9aebd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770149"
---
# <a name="view-azure-maps-api-usage"></a>Vista del uso de la API de Azure Maps

En este artículo se muestra cómo ver las métricas de uso de la API de su cuenta de Azure Maps en el [portal](https://portal.azure.com). Las métricas se muestran en un práctico formato gráfico a lo largo de una duración de tiempo personalizable.

## <a name="view-metric-snapshot"></a>Visualización de instantánea de métricas

Puede ver algunas métricas comunes en la página **Overview** (Información general) de su cuenta de Maps. Actualmente se muestra *Total Requests* (Solicitudes totales), *Total Errors* (Errores totales) y *Availability* (Disponibilidad) durante una longitud de tiempo seleccionable.

![Introducción a las métricas de Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Si necesita personalizar estos gráficos para su análisis en particular, continúe con la siguiente sección.

## <a name="view-detailed-metrics"></a>Visualización de métricas detalladas

1. Inicie sesión en la suscripción de Azure en el [portal](https://portal.azure.com).

2. Haga clic en el elemento de menú **Todos los recursos** en el lado izquierdo y desplácese hasta su *cuenta de Azure Maps*.

3. Una vez que se abra la cuenta de Maps, haga clic en el menú **Metrics** (Métricas) de la izquierda.

4. En el panel **Metrics** (Métricas), elija entre una de las opciones siguientes:

   1. **Availability** (Disponibilidad): que muestra el *promedio* de disponibilidad de la API durante un período de tiempo.
   2. **Usage** (Uso): que muestra cómo es el *recuento* de uso para su cuenta.

      ![Panel de métricas de Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. A continuación, puede seleccionar *Time range* (Intervalo de tiempo) haciendo clic en **Last 24 hours (Automatic)** (Últimas 24 horas [automático]). De forma predeterminada, el intervalo de tiempo se establece en 24 horas. Después de hacer clic, verá todos los intervalos de tiempo seleccionables. Puede seleccionar la *granularidad del tiempo* y elegir mostrar el tiempo como *local* o *GMT* en la misma lista desplegable. Haga clic en **Aplicar**.

    ![Intervalo de tiempo de métricas de Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Después de agregar la métrica, puede seleccionar **Add filter** (Agregar filtro) para filtrar las propiedades pertinentes para esa métrica, y luego seleccionar el valor de la propiedad que quiere ver en el gráfico.

    ![Filtro de métricas de Azure Maps](media/how-to-view-api-usage/filter.png)

7. También puede seleccionar **Apply splitting** (Aplicar división) para la métrica en función de la propiedad de métrica seleccionada. Esto permite que el gráfico se divida en varios gráficos, uno por cada valor de esa propiedad. En la imagen siguiente el color de cada gráfico corresponde al valor de propiedad que se muestra en la parte inferior del gráfico.

    ![División de métricas de Azure Maps](media/how-to-view-api-usage/splitting.png)

8. También puede observar varias métricas en el mismo gráfico con solo hacer clic en el botón **Add metric** (Agregar métrica) en la parte superior.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las API de Azure Maps de las que quiere realizar un seguimiento de uso:

> [!div class="nextstepaction"]
> [Documentación de la API REST de Azure Maps](https://docs.microsoft.com/rest/api/maps)
