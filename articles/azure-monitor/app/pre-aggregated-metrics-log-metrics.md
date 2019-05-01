---
title: Métricas agregadas previamente y basadas en registros en Azure Application Insights | Microsoft Docs
description: Por qué usar métricas basadas en registro frente a métricas agregadas previamente en Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitalyg
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 96d4c2c8d6c2dd7722b7377dd7a0ffd42acd3126
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572519"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas agregadas previamente y basadas en registros en Application Insights

En este artículo se explica la diferencia entre las métricas de Application Insights "tradicionales" que se basan en los registros y las métricas previamente agregadas que están actualmente en versión preliminar pública. Ambos tipos de métricas están disponibles para los usuarios de Application Insights, y cada uno aporta un valor único para las funciones de supervisión del estado de mantenimiento de la aplicación, diagnóstico y análisis. Los desarrolladores que van a instrumentar aplicaciones pueden decidir qué tipo de métrica es la más adecuada para un escenario determinado, según el tamaño de la aplicación, el volumen esperado de los datos de telemetría y los requisitos empresariales de alertas y precisión de las métricas.

## <a name="log-based-metrics"></a>Métricas basadas en registros

Hasta hace poco, el modelo de datos de telemetría de supervisión de aplicaciones de Application Insights se basaba únicamente en unos pocos tipos predefinidos de eventos, tales como solicitudes, excepciones, llamadas de dependencia, vistas de página, etc. Los desarrolladores pueden usar el SDK para emitir estos eventos manualmente (escribiendo código que invoca explícitamente el SDK) o pueden usar la recopilación automática de eventos de la instrumentación automática. En cualquier caso, el servidor back-end de Application Insights almacena todos los eventos recopilados como registros y las hojas de Application Insights en Azure Portal actúan como herramienta de análisis y diagnóstico para visualizar los datos basados en eventos de los registros.

El uso de registros para conservar un conjunto completo de eventos puede aportar gran valor al análisis y el diagnóstico. Por ejemplo, puede obtener el número exacto de solicitudes enviadas a una dirección URL determinada con el número de usuarios distintos que realizaron estas llamadas. O puede obtener seguimientos de diagnóstico detallados, incluidas las excepciones y llamadas de dependencia para cualquier sesión de usuario. Con este tipo de información puede mejorar considerablemente la visibilidad sobre el estado y el uso de la aplicación, lo que permite reducir el tiempo necesario diagnosticar los problemas de una aplicación. 

Al mismo tiempo, recopilar un conjunto completo de eventos puede resultar poco práctico (o incluso imposible) en el caso de aplicaciones que generan una gran cantidad de datos de telemetría. En aquellos casos en los que el volumen de eventos es demasiado alto, Application Insights implementa varias técnicas de reducción del volumen de datos de telemetría, tales como [muestreo](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) y [filtrado](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), que reducen el número de eventos recopilados y almacenados. Lamentablemente, reducir el número de eventos almacenados reduce también la precisión de las métricas que, en segundo plano, deben realizar agregaciones en tiempo de consulta de los eventos almacenados en los registros.

> [!NOTE]
> En Application Insights, las métricas que se basan en la agregación en tiempo de consulta de los eventos y las medidas que se almacenan en los registros se denominan métricas basadas en registros. Estas métricas suelen tienen muchas dimensiones de las propiedades de los eventos, lo que hace que sean superiores para el análisis, pero la precisión de estas métricas se ve afectada negativamente por el muestreo y el filtrado.

## <a name="pre-aggregated-metrics"></a>Métricas agregadas previamente

Además de las métricas basadas en registros, en otoño de 2018, el equipo de Application Insights lanzó una versión preliminar pública de métricas que se almacenan en un repositorio especializado que está optimizado para series temporales. Las nuevas métricas ya no se conservan como eventos individuales con una gran cantidad de propiedades. En su lugar, se almacenan como series temporales previamente agregadas y solo con las dimensiones clave. Esto hace que las nuevas métricas sean superiores en tiempo de consulta: la recuperación de datos es mucho más rápida y requiere menos capacidad de proceso. Esto permite nuevos escenarios como las [alertas casi en tiempo real sobre las dimensiones de las métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts) y [paneles](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards) con más capacidad de respuesta y muchos más.

> [!IMPORTANT]
> Las métricas basadas en registros y las métricas agregadas previamente coexisten en Application Insights. Para diferenciar las dos, en la experiencia de usuario de Application Insights, las métricas agregadas previamente ahora se llaman "métricas estándar (versión preliminar)", mientras que el nombre de las métricas tradicionales de eventos ha cambiado a "métricas basadas en registros".

Los SDK más recientes (SDK de [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) o versiones posteriores para. NET) agregan previamente las métricas durante la recopilación antes de que se apliquen las técnicas de reducción del volumen de datos de telemetría. Esto significa que la precisión de las nuevas métricas no se ve afectada por el muestreo y el filtrado cuando se usan las versiones más recientes del SDK de Application Insights.

En el caso de los SDK que no implementan la agregación previa (es decir, las versiones anteriores del SDK de Application Insights o la instrumentación del explorador) el servidor back-end de Application Insights sigue agregando los eventos recibidos por la el punto de conexión de recopilación de eventos de Application Insights para rellenar las nuevas métricas. Esto significa que aunque no se beneficie del menor volumen de datos que se transmite a través del cable, podrá seguir usando las métricas agregadas previamente para experimentar un mejor rendimiento y aprovechar la compatibilidad con las alertas dimensionales casi en tiempo real con los SDK que no agregan previamente las métricas durante la recopilación.

Merece la pena mencionar que el punto de conexión de la colección agrega previamente los eventos antes de muestreo de ingesta, lo que significa que el [muestreo de ingesta](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) no afectará nunca a la precisión de las métricas agregadas previamente, independientemente de la versión SDK que use con su aplicación.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Uso de agregación previa con métricas personalizadas de Application Insights

Puede usar la agregación previa con las métricas personalizadas. Las dos ventajas principales son la capacidad de configurar y generar alertas sobre una dimensión de una métrica personalizada, y la reducción del volumen de datos que se envía desde el SDK al punto de conexión de recopilación de Application Insights.

Hay varias [maneras de enviar métricas personalizadas desde el SDK de Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Si su versión del SDK ofrece los métodos [GetMetric y TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric), esta es la manera preferida para enviar métricas personalizadas, ya que la agregación previa ocurre dentro del SDK, lo que no solo reduce el volumen de datos almacenados en Azure, sino también el volumen de datos que se transmite desde el SDK de Application Insights. En caso contrario, use el método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), que realizará la agregación previa de los eventos de métrica durante la ingesta de datos.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensiones de métricas personalizadas y agregación previa

Todas las métricas que envíe con las llamadas API [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) o [GetMetric y TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) se almacenan automáticamente en los almacenes de registros y métricas. Sin embargo, mientras que la versión basada en registros de la métrica personalizada siempre conserva todas las dimensiones, la versión de la métrica previamente agregada se almacena de forma predeterminada sin dimensiones. Puede activar la recopilación de dimensiones de las métricas personalizadas en la pestaña [Usage and estimated cost](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) (Uso y costos estimados), activando "Enable alerting on custom metric dimensions" (Habilitar las alertas en las dimensiones de métricas personalizadas): 

![Uso y costos estimados](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>¿Por qué la recopilación de dimensiones de métricas personalizadas está desactivada de forma predeterminada?

La recopilación de dimensiones de métricas personalizadas se ha desactivado de forma predeterminada porque, en el futuro, el almacenamiento de métricas personalizadas con dimensiones se facturará por separado de Application Insights, mientras que el almacenamiento de métricas personalizadas no dimensionales seguirá siendo gratuita (hasta una cuota). Para más información sobre los próximos cambios en los modelos de precios, consulte la [página de precios](https://azure.microsoft.com/pricing/details/monitor/) oficial.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Creación de gráficos y exploración de métricas estándar basadas en registros y agregadas previamente

Use [Explorador de métricas de Azure Monitor](../platform/metrics-getting-started.md) para trazar los gráficos de métricas agregadas previamente y basado en registro y los paneles de autor con gráficos. Después de seleccionar el recurso de Application Insights deseado, utilice el selector de espacios de nombres para cambiar de métricas estándar (versión preliminar) a métricas basadas en registros, o seleccione un espacio de nombres de métricas personalizadas:

![Espacio de nombres de métricas](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Pasos siguientes

* [Alertas casi en tiempo real](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric y TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)