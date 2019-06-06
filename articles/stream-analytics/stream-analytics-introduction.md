---
title: Introducción a Azure Stream Analytics
description: Obtenga información sobre Stream Analytics, un servicio administrado que le ayudará a analizar datos de streaming desde Internet de las cosas (IoT) en tiempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 05/16/2019
ms.openlocfilehash: f607c1e79262efdbec79677fa7f16863b7a9d160
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393982"
---
# <a name="what-is-azure-stream-analytics"></a>¿Qué es Azure Stream Analytics?

Azure Stream Analytics es un motor de procesamiento de eventos diseñado para examinar altos volúmenes de datos de streaming. Los patrones y las relaciones se pueden identificar en la información extraída de varios orígenes de entrada, como dispositivos, sensores, sitios web, fuentes de medios sociales y aplicaciones. Estos patrones se pueden usar para desencadenar otras acciones de bajada, como la creación alertas, la provisión de información a una herramienta de generación de informes o el almacenamiento de datos transformados para usarlos posteriormente.

Los escenarios siguientes son ejemplos de cuándo puede usar Azure Stream Analytics:

* Fusión del sensor de Internet de las cosas (IoT) y análisis en tiempo real de la telemetría de dispositivos
* Análisis clickstream y de registros web
* Análisis geoespacial para la administración de flotas y vehículos sin conductor
* Supervisión remota y mantenimiento predictivo de recursos de gran valor
* Análisis en tiempo real de datos de punto de venta para el control de inventario y la detección de anomalías

## <a name="how-does-stream-analytics-work"></a>¿Cómo funciona Stream Analytics?

Un trabajo de Azure Stream Analytics consta de una entrada, una consulta de transformación y una salida. Cuando se introducen los eventos de su software o sus dispositivos en Azure Event Hubs, Azure IoT Hub o Azure Blob Storage, puede especificar uno o varios de esos servicios como el origen de entrada para el trabajo. La consulta de transformación, que se basa en el lenguaje de consulta SQL, se utiliza para filtrar, ordenar, agregar y unir con facilidad los datos de transmisión a lo largo de un período de tiempo. Puede ajustar las opciones de ordenación de los eventos y la duración de las ventanas de tiempo cuando se realizan operaciones de agregación.

Cada trabajo tiene una salida para los datos transformados, y puede controlar lo que ocurre en respuesta a la información que ha analizado. Por ejemplo, puede:

* Enviar datos a una cola supervisada para desencadenar alertas o flujos de trabajo personalizados de bajada.
* Enviar datos al panel de Power BI para verlos en tiempo real.
* Almacenar los datos en otros servicios de Azure Storage para entrenar un modelo de Machine Learning basado en datos históricos o realizar un análisis por lotes.

La imagen siguiente muestra cómo se envían los datos a Stream Analytics, se analizan y se envían para realizar otras acciones, como el almacenamiento o la presentación:

![Canalización de introducción de Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Ventajas y principales capacidades

Azure Stream Analytics se ha diseñado para ser fácil de usar, flexible, confiable y escalable a cualquier tamaño de trabajo. Está disponible en varias regiones de Azure. La siguiente imagen muestra las principales funcionalidades de Azure Stream Analytics:

![Principales funcionalidades de Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidad para empezar

Es fácil empezar a usar Azure Stream Analytics. Con pocos clics es posible conectarse a varios orígenes y receptores, creando una canalización integral. Stream Analytics puede conectarse directamente a [Azure Event Hubs](/azure/event-hubs/) y [Azure IoT Hub](/azure/iot-hub/) para la ingesta de datos de streaming, y con el [servicio Azure Blob Storage](/azure/storage/storage-introduction) para la ingesta de datos históricos. La entrada del trabajo también puede incluir datos de referencia estáticos o que cambien lentamente de Azure Blob Storage o [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) que puede unir a los datos de streaming para realizar operaciones de búsqueda.

Stream Analytics puede enrutar la salida del trabajo a muchos sistemas de almacenamiento como [Azure Blob Storage](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/) y [Azure Cosmos DB](/azure/cosmos-db/introduction). Se pueden realizar análisis por lotes en los resultados almacenados con Azure HDInsight o enviar la salida a otro servicio, como a Event Hubs para su consumo, o a [Power BI](https://docs.microsoft.com/power-bi/) para la visualización en tiempo rea.

Para obtener la lista completa de las salidas de Stream Analytics, consulte [Información sobre las salidas desde Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Productividad del programador

Azure Stream Analytics usa un lenguaje de consulta simple basado en SQL que se ha mejorado con eficaces restricciones temporales para analizar datos en movimiento. Para definir las transformaciones de los trabajos, se utiliza un simple [lenguaje de consulta de Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) declarativo que permite crear complejos análisis y consultas temporales mediante simples construcciones SQL. Puesto que el lenguaje de consulta de Stream Analytics concuerda con el lenguaje SQL, el mero conocimiento de este permite empezar a crear trabajos. También se pueden crear trabajos mediante herramientas de desarrollo como Azure PowerShell, [herramientas de Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md), la [extensión de Visual Studio Code de Stream Analytics](quick-create-vs-code.md) o plantillas de Azure Resource Manager. Las herramientas de desarrollo permiten desarrollar consultas de transformación sin conexión y usar la [canalización de CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para enviar trabajos a Azure.

El lenguaje de consulta de Stream Analytics ofrece una amplia matriz de funciones para analizar y procesar los datos de streaming. Este lenguaje de consulta admite la manipulación simple de datos, la agregación de funciones y las complejas funciones geoespaciales. Las consultas se pueden editar en el portal y se pueden probar con datos de ejemplo que se extraen de la secuencia en directo.

Puede ampliar la funcionalidad del lenguaje de consulta si define e invoca funciones adicionales. Puede definir que las llamadas a funciones de Azure Machine Learning Service saquen provecho de las soluciones de Azure Machine Learning e integren las funciones definidas por el usuario (UDF) de JavaScript o C#, así como los agregados definidos por el usuario, para realizar cálculos complejos como parte de una consulta de Stream Analytics.

## <a name="fully-managed"></a>Completamente administrada

Azure Stream Analytics es una oferta sin servidor totalmente administrada (PaaS) en Azure. No es preciso aprovisionar hardware ni administrar clústeres para ejecutar los trabajos. Azure Stream Analytics administra totalmente el trabajo configurando clústeres de proceso complejos en la nube y ocupándose del ajuste del rendimiento necesario para ejecutar el trabajo. La integración con Azure Event Hubs y Azure IoT Hub permite que el trabajo ingiera millones de eventos por segundo procedentes de diferentes orígenes, para incluir dispositivos conectados, secuencias de clic y archivos de registro. Mediante la característica de creación de particiones de Event Hubs, puede dividir los cálculos en pasos lógicos, cada uno de ellos con la capacidad de ser subdividido aún más para aumentar la escalabilidad.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Ejecución en la nube o en el sistema de inteligencia perimetral

Azure Stream Analytics se puede ejecutar en la nube, para realizar análisis a gran escala, o en IoT Edge para un análisis con latencia muy baja. Azure Stream Analytics usa el mismo lenguaje de consulta en la nube y en el sistema perimetral, lo que permite a los desarrolladores compilar arquitecturas verdaderamente híbridas para el procesamiento de flujos.

## <a name="low-total-cost-of-ownership"></a>Costo total de propiedad bajo

Como servicio en la nube, Stream Analytics se optimiza por motivos de costo. No hay costos iniciales, solo se pagan las [unidades de streaming que se consuman](stream-analytics-streaming-unit-consumption.md) y la cantidad de datos procesados. No se requiere compromiso ni aprovisionamiento de clústeres, y puede escalar o reducir verticalmente el trabajo según las necesidades de su negocio.

## <a name="mission-critical-ready"></a>Preparado para situaciones críticas

Azure Stream Analytics está disponible en varias regiones de todo el mundo y está diseñado para ejecutar cargas de trabajo críticas al respaldar los requisitos de confiabilidad, seguridad y cumplimiento.

### <a name="reliability"></a>Confiabilidad

Azure Stream Analytics garantiza el procesamiento de eventos exactamente una vez y la entrega de eventos por lo menos una vez, así que los eventos nunca se pierden. El procesamiento exactamente una vez está garantizado con la salida seleccionada, como se describe en [Event Delivery Guarantees](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) (Garantías de entrega de contenido).

Azure Stream Analytics presenta funcionalidades de recuperación integradas en caso de que se produzca un error en la entrega de un evento. Además, Stream Analytics proporciona puntos de comprobación integrados para mantener el estado del trabajo y proporciona resultados repetibles.

Como servicio administrado, Stream Analytics garantiza un procesamiento de eventos con una disponibilidad del 99,9 % por minuto. Para más información, consulte la página [Contrato de nivel de servicio para Azure Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/). 

### <a name="security"></a>Seguridad

En cuanto a seguridad, Azure Stream Analytics cifra todas las comunicaciones entrantes y salientes y es compatible con TLS 1.2. También se cifran los puntos de control integrados. Stream Analytics no almacena los datos entrantes, ya que todo el procesamiento se realiza en memoria.

### <a name="compliance"></a>Cumplimiento normativo

Azure Stream Analytics sigue varias certificaciones de cumplimiento, como se describe en la [información general de cumplimiento de Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Rendimiento

Stream Analytics puede procesar millones de eventos por segundo y ofrecer resultados con baja latencia. Permite el escalado vertical y horizontal para controlar las aplicaciones grandes de procesamiento de eventos complejos y en tiempo real. Stream Analytics mejora el rendimiento mediante la creación de particiones, lo que permite que las consultas complejas se puedan procesar en paralelo y ejecutar en varios nodos de streaming. Azure Stream Analytics se basa en [Trill](https://github.com/Microsoft/Trill), un motor de análisis de streaming de alto rendimiento y en memoria desarrollado en colaboración con Microsoft Research.

## <a name="next-steps"></a>Pasos siguientes

Ya tiene información general acerca de Azure Stream Analytics. A continuación, puede profundizar y crear su primer trabajo de Stream Analytics:

* [Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md).
* [Creación de un trabajo de Stream Analytics mediante Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Creación de un trabajo de Stream Analytics mediante Visual Studio](stream-analytics-quick-create-vs.md).
* [Creación de un trabajo en la nube de Azure Stream Analytics en Visual Studio Code](quick-create-vs-code.md).
