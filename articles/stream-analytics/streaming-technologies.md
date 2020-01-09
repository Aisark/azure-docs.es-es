---
title: Elección de una solución de procesamiento de flujos en tiempo real en Azure
description: Obtenga información sobre cómo elegir la tecnología de análisis en tiempo real y procesamiento de flujos adecuada para compilar una aplicación en Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f3ecb3bedb8e0f55590aca1aa1c0e0c185f9acc3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369259"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Elección de la tecnología de análisis en tiempo real y procesamiento de flujos en Azure

Hay varios servicios disponibles para el análisis en tiempo real y procesamiento de flujos en Azure. Este artículo le proporciona la información que necesita para decidir qué tecnología se ajusta mejor a su aplicación.

## <a name="when-to-use-azure-stream-analytics"></a>Cuándo usar Azure Stream Analytics

Azure Stream Analytics es el servicio recomendado para el análisis de flujos en Azure. Está destinado para una amplia gama de escenarios que incluyen, pero no se limitan a:

* Paneles de visualización de datos
* [Alertas](stream-analytics-set-up-alerts.md) en tiempo real de patrones espaciales y temporales o anomalías
* Extraer, transformar y cargar (ETL)
* [Patrón Event Sourcing](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Agregar un trabajo de Azure Stream Analytics a la aplicación es la manera más rápida de obtener análisis de flujos en funcionamiento en Azure mediante el lenguaje SQL que ya conoce. Azure Stream Analytics es un servicio de trabajo, por lo que no tiene que perder el tiempo administrando clústeres, ni preocuparse por el tiempo de inactividad, con un SLA del 99,9% en el nivel de trabajo. La facturación también se realiza en el nivel de trabajo, lo que hace que los costos iniciales sean bajos (una unidad de streaming), pero escalables (hasta 192 unidades de streaming). Es mucho más rentable ejecutar algunos trabajos de Stream Analytics que ejecutar y mantener un clúster.

Azure Stream Analytics ofrece una experiencia muy completa de fábrica. Puede aprovechar inmediatamente las siguientes características sin ninguna configuración adicional:

* Operadores temporales integrados, como [agregados en ventanas](stream-analytics-window-functions.md), combinaciones temporales y funciones analíticas temporales.
* Adaptadores de [entrada](stream-analytics-add-inputs.md) y [salida](stream-analytics-define-outputs.md) nativos de Azure
* Compatibilidad con [datos de referencia](stream-analytics-use-reference-data.md) de variación lenta (también conocidos como tablas de búsqueda), incluida la unión con datos de referencia geoespaciales de geovalla.
* Soluciones integradas, como la [detección de anomalías](stream-analytics-machine-learning-anomaly-detection.md)
* Varias ventanas de tiempo en la misma consulta
* Capacidad de crear varios operadores temporales en secuencias arbitrarias.
* Latencia de un extremo a otro por debajo de 100 ms de la entrada que llega a Event Hubs, incluido el retraso de red desde y hacia Event Hubs, con alto rendimiento sostenido

## <a name="when-to-use-other-technologies"></a>Cuándo usar otras tecnologías

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Necesita una entrada o salida de o hacia Kafka

Azure Stream Analytics no tiene una entrada de Apache Kafka ni un adaptador de salida. Si tiene eventos que llegan o necesita enviar a Kafka y no tiene un requisito para ejecutar su propio clúster de Kafka, puede seguir usando Stream Analytics enviando de eventos a Event Hubs mediante la API de Kafka de Event Hubs sin cambiar el remitente del evento. Si necesita ejecutar su propio clúster de Kafka, puede usar Spark Structured Streaming, que es totalmente compatible con [Azure Databricks](../azure-databricks/index.yml), o Storm en [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Quiere escribir UDF, UDA y deserializadores personalizados en un lenguaje distinto de JavaScript o C#

Azure Stream Analytics admite funciones definidas por el usuario (UDF) o agregados definidos por el usuario (UDA) en JavaScript para los trabajos en la nube y C# para trabajos de IoT Edge. También se admiten los deserializadores definidos por el usuario de C#. Si quiere implementar un deserializador, una UDF o un UDA en otros lenguajes, como Java o Python, puede usar Spark Structured Streaming. También puede ejecutar el **EventProcessorHost** de Event Hubs en sus propias máquinas virtuales para realizar un procesamiento de flujos arbitrario.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>La solución está en un entorno de varias nubes o local

Azure Stream Analytics es la tecnología propietaria de Microsoft y solo está disponible en Azure. Si necesita que la solución sea portable entre nubes o de forma local, considere la posibilidad de tecnologías de código abierto como Storm o Spark Structured Streaming.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)
* [Creación de un trabajo de Stream Analytics mediante Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Creación de un trabajo de Stream Analytics mediante Visual Studio](stream-analytics-quick-create-vs.md)
* [Creación de un trabajo en la nube de Azure Stream Analytics en Visual Studio Code](quick-create-vs-code.md)
