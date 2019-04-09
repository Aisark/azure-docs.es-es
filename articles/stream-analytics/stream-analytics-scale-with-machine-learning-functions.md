---
title: Escalado de funciones de Machine Learning en Azure Stream Analytics
description: En este artículo se describe cómo escalar los trabajos de Stream Analytics que usan funciones de Machine Learning mediante la configuración de creación de particiones y unidades de streaming.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f11034a4970e3fb95333310af82a6b2a2551f1eb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257287"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Escalado del trabajo de Análisis de transmisiones con funciones de Azure Machine Learning
Resulta sencillo configurar un trabajo de Stream Analytics y ejecutar algunos datos de ejemplo mediante él. ¿Qué debemos hacer cuando tengamos que ejecutar el mismo trabajo con un volumen de datos más alto? Será necesario comprender cómo configurar el trabajo de Stream Analytics para escalarlo. En este documento, nos centraremos en los aspectos especiales de escalar trabajos de Stream Analytics con funciones de Machine Learning. Para más información sobre cómo escalar trabajos de Stream Analytics en general, consulte el artículo [Escalado de trabajos de Azure Stream Analytics para incrementar el rendimiento de procesamiento de flujo de datos](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>¿Qué es una función de Azure Machine Learning en Análisis de transmisiones?
Una función de Machine Learning en Análisis de transmisiones puede utilizarse como una llamada de función normal en el lenguaje de consulta de Análisis de transmisiones. Sin embargo, en segundo plano, las llamadas de función son en realidad solicitudes de servicio web de Azure Machine Learning. Los servicios web de Machine Learning admiten el "procesamiento por lotes" de varias filas, lo que se conoce como mini lote, en la misma llamada a la API del servicio web, con el fin de mejorar el rendimiento en general. Para obtener más información, consulte [servicios Web de Azure Machine Learning](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configuración de un trabajo de Análisis de transmisiones con funciones de Machine Learning
Al configurar una función de Machine Learning para el trabajo de Stream Analytics, hay dos parámetros a tener en cuenta: el tamaño de lote de las llamadas de función de Machine Learning y las unidades de streaming (SU) aprovisionadas para el trabajo de Stream Analytics. Para determinar los valores adecuados de estas SU, será necesario en primer lugar decidir entre latencia y rendimiento, es decir, entre la latencia del trabajo de Stream Analytics y el rendimiento de cada SU. Siempre se pueden agregar SU a un trabajo para aumentar el rendimiento de una consulta de Stream Analytics bien particionada, aunque las SU adicionales aumentarán el costo de ejecución del trabajo.

Por lo tanto, es importante determinar la *tolerancia* de la latencia en la ejecución de un trabajo de Stream Analytics. La latencia adicional derivada de ejecutar solicitudes de servicio de Azure Machine Learning aumentará naturalmente con el tamaño del lote, lo que agravará la latencia del trabajo de Stream Analytics. Por otro lado, al aumentar el tamaño del lote, el trabajo de Stream Analytics puede procesar *más eventos con el *mismo número* de solicitudes de servicio web Machine Learning. Con frecuencia, el aumento de la latencia del servicio web Machine Learning es sublineal con respecto al aumento del tamaño del lote, así que es importante tener en cuenta el tamaño de lote más rentable para un servicio web Machine Learning en cualquier situación dada. El tamaño de lote predeterminado para las solicitudes de servicio web es de 1000 y se puede modificar mediante la [API de REST de Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "API de REST de Stream Analytics") o el [cliente de PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "cliente de PowerShell para Stream Analytics").

Una vez determinado el tamaño de lote, se puede determinar el número de unidades de streaming (SU) según el número de eventos que la función tenga que procesar por segundo. Para más información sobre el ajuste de las unidades de streaming, consulte [Escalación de trabajos de Stream Analytics](stream-analytics-scale-jobs.md).

En general, hay 20 conexiones simultáneas al servicio web Machine Learning por cada 6 SU, salvo que los trabajos de 1 y 3 SU obtienen también 20 conexiones simultáneas.  Por ejemplo, si la tasa de datos de entrada es de 200 000 eventos por segundo y el tamaño de lote se deja en el valor predeterminado de 1000, la latencia de servicio web resultante con un minilote de 1000 eventos será de 200 ms. Esto significa que cada conexión puede hacer cinco solicitudes al servicio web Machine Learning en un segundo. Con 20 conexiones, el trabajo de Stream Analytics puede procesar 20 000 eventos en 200 ms y, por lo tanto, 100 000 eventos en un segundo. Así que para procesar 200 000 eventos por segundo, el trabajo de Stream Analytics necesita 40 conexiones simultáneas, lo que viene a ser 12 SU. En el diagrama siguiente se muestran las solicitudes desde el trabajo de Stream Analytics hasta el punto de conexión de servicio web Machine Learning: cada 6 SU tienen 20 conexiones simultáneas al servicio web Machine Learning como máximo.

![Ejemplo de escalado de trabajo de Stream Analytics con funciones de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Ejemplo de escalado de trabajo de Stream Analytics con funciones de Machine Learning")

En general, si ***B*** es el tamaño de lote y ***L*** es la latencia del servicio web con el tamaño de lote B en milisegundos, el rendimiento de un trabajo de Stream Analytics con ***N*** SU es:

![Fórmula de escalado de Stream Analytics con funciones de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula de escalado de Stream Analytics con funciones de Machine Learning")

Un aspecto adicional a tener en cuenta pueden ser las 'llamadas simultáneas máximas' en el lado del servicio web de Machine Learning; se recomienda que estas llamadas se establezcan en el valor máximo (actualmente 200).

Para más información sobre esta configuración, consulte el artículo de [Escalado para servicio web Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Ejemplo: Análisis de opiniones
En el ejemplo siguiente se incluye un trabajo de Análisis de transmisiones con la función de Machine Learning de análisis de opiniones, como se describe en el [tutorial de integración de Machine Learning de Análisis de transmisiones](stream-analytics-machine-learning-integration-tutorial.md).

La consulta es una consulta sencilla completamente particionada seguida de la función **sentiment**, tal como se muestra en el ejemplo siguiente:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
Considere el escenario siguiente: se debe crear un trabajo de Stream Analytics con un rendimiento de 10 000 tweets por segundo para realizar análisis de opiniones de los tweets (eventos). ¿Podría administrar este trabajo de Stream Analytics el tráfico con 1 SU? Con el tamaño de lote predeterminado de 1000, el trabajo debería poder mantenerse al nivel de la entrada. Es más, la función de Machine Learning agregada no debería generar más de un segundo de latencia, que es la latencia general predeterminada del servicio web de Machine Learning de análisis de opiniones (con un tamaño de lote predeterminado de 1000). La latencia **general** o total del trabajo de Stream Analytics sería normalmente de unos segundos. Examine con más detalle este trabajo de Análisis de transmisiones, *especialmente* las llamadas de función de Machine Learning. Si el tamaño de lote es 1000, un rendimiento de 10 000 eventos supondrá unas 10 solicitudes al servicio web. Incluso con 1 SU, hay suficientes conexiones simultáneas para dar cabida a este tráfico de entrada.

Si la tasa de eventos de entrada aumenta en 100 x, el trabajo de Stream Analytics necesita procesar 1 000 000 de tweets por segundo. Hay dos opciones para lograr una escala mayor:

1. Aumentar el tamaño de lote
2. Particionar el flujo de entrada para procesar los eventos en paralelo.

Con la primera opción, la **latencia** del trabajo aumenta.

Con la segunda opción, será necesario aprovisionar más SU y, por lo tanto, generar más solicitudes de servicio web de Machine Learning simultáneas. Esto significa que el **costo** del trabajo aumenta.

Suponga que la latencia del servicio web Machine Learning de análisis de opiniones es de 200 ms para 1000 lotes de eventos o menos, 250 ms para 5000 lotes de eventos, 300 ms para 10 000 lotes de eventos o 500 ms para 25 000 lotes de eventos.

1. Uso de la primera opción (**no** aprovisionar más SU). Se podría aumentar el tamaño de lote a **25 000**. Esto, a su vez, permitiría que el trabajo procesara 1 000 000 eventos con 20 conexiones simultáneas al servicio web Machine Learning (con una latencia de 500 ms por llamada). De modo que, la latencia adicional del trabajo de Stream Analytics debido a las solicitudes de la función sentiment contra las solicitudes de servicio web Machine Learning aumentaría de **200 ms** a **500 ms**. Sin embargo, el tamaño de lote **no** se puede aumentar de manera indefinida dado que el servicio web Machine Learning necesita que el tamaño de la carga de una solicitud sea de 4 MB o un tiempo de espera más pequeño de las solicitudes de servicio web tras 100 segundos de funcionamiento.
2. Con la segunda opción, el tamaño de lote se deja en 1000; con una latencia del servicio web de 200 ms, con cada 20 conexiones simultáneas al servicio web se podrían procesar 1000 * 20 * 5 eventos = 100 000 por segundo. Por tanto, para procesar 1 000 000 eventos por segundo, el trabajo necesitaría 60 SU. En comparación con la primera opción, el trabajo de Stream Analytics haría más solicitudes de lote de servicio web, lo que a su vez generaría un mayor costo.

A continuación se muestra una tabla del rendimiento del trabajo de Stream Analytics para diferentes SU y tamaños de lote (en número de eventos por segundo).

| Tamaño del lote (latencia de Aprendizaje automático) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 unidad de búsqueda** |2500 |5.000 |20.000 |30 000 |50.000 |
| **3 SU** |2500 |5.000 |20.000 |30 000 |50.000 |
| **6 SU** |2500 |5.000 |20.000 |30 000 |50.000 |
| **12 SU** |5.000 |10 000 |40.000 |60 000 |100 000 |
| **18 SU** |7500 |15 000 |60 000 |90 000 |150 000 |
| **24 SU** |10 000 |20.000 |80 000 |120 000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SU** |25 000 |50.000 |200 000 |300 000 |500.000 |

En este momento, ya debe saber cómo funcionan las funciones de Machine Learning en Análisis de transmisiones. Probablemente también sepa que los trabajos de Stream Analytics "extraen" datos de los orígenes de datos y que cada "extracción" devuelve un lote de eventos que procesa el trabajo de Stream Analytics. ¿Cómo afecta este modelo de extracción a las solicitudes de servicio web de Machine Learning?

Normalmente, el tamaño de lote que establecemos para las funciones de Machine Learning no será divisible exactamente por el número de eventos devueltos por cada "extracción" del trabajo de Stream Analytics. Cuando suceda esto, se llamará al servicio web Machine Learning con lotes "parciales". Esto se hace para no generar sobrecarga de latencia adicional del trabajo en la fusión de eventos de una extracción a otra.

## <a name="new-function-related-monitoring-metrics"></a>Nuevas métricas de supervisión relacionadas con la función
En el área de supervisión de un trabajo de Stream Analytics, se han agregado tres métricas adicionales relacionadas con las funciones. Y son: SOLICITUDES DE FUNCIONES, EVENTOS DE FUNCIONES y SOLICITUDES DE FUNCIONES CON ERRORES, como se muestra en el siguiente gráfico.

![Métricas de escalado de Stream Analytics con funciones de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Métricas de escalado de Stream Analytics con funciones de Machine Learning")

Se definen de la manera siguiente:

**SOLICITUDES DE FUNCIONES**: número de solicitudes de función.

**EVENTOS DE FUNCIONES**: número de eventos en las solicitudes de función.

**SOLICITUDES DE FUNCIONES CON ERRORES**: número de solicitudes de función con errores.

## <a name="key-takeaways"></a>Puntos clave
Para resumir los puntos principales, para escalar un trabajo de Stream Analytics con funciones de Machine Learning, debe tener en cuenta los siguientes elementos:

1. La tasa de eventos de entrada.
2. La latencia permitida para el trabajo de Análisis de transmisiones en ejecución (y, por tanto, el tamaño de lote de las solicitudes de servicio web de Machine Learning).
3. Las SU de Análisis de transmisiones aprovisionadas y el número de solicitudes de servicio web de Machine Learning (los costos relacionados con funciones adicionales).

Como ejemplo se ha utilizado una consulta de Stream Analytics totalmente particionada. Si se necesita una consulta más compleja, el [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) es un excelente recurso para obtener ayuda adicional del equipo de Stream Analytics.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Stream Analytics, vea:

* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalar los trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
