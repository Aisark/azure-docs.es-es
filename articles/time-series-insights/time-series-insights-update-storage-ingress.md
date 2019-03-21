---
title: Almacenamiento y entrada de datos en la versión preliminar de Azure Time Series Insights | Microsoft Docs
description: Descripción del almacenamiento y la entrada de datos en la versión preliminar de Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: eba4c70a25cba2e456ed418a98b938f2029c4c1f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438370"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Almacenamiento y entrada de datos en la versión preliminar de Azure Time Series Insights

En este artículo se describen los cambios en el almacenamiento y la entrada de datos de la versión preliminar de Azure Time Series Insights. Se trata la estructura de almacenamiento subyacente, el formato de archivos y la propiedad Time Series ID. En el artículo también se describe el proceso de entrada subyacente, el rendimiento y las limitaciones.

## <a name="data-storage"></a>Almacenamiento de datos

Al crear un entorno de SKU de pago por uso para la versión preliminar de Time Series Insights, se generan dos recursos:

* Un entorno de Time Series Insights.
* Una cuenta de uso general V1 de Azure Storage donde se almacenarán los datos.

La versión preliminar de Time Series Insights usa Azure Blob Storage con el tipo de archivo Parquet. Time Series Insights administra todas las operaciones con datos, incluida la creación de blobs, la indexación y la creación de particiones de los datos en la cuenta de almacenamiento de Azure. Cree estos blobs con una cuenta de almacenamiento de Azure.

Al igual que otros blobs de Azure Storage, los que crea Time Series Insights le permiten leer y escribir en ellos para admitir diversos escenarios de integración.

> [!TIP]
> El rendimiento de Time Series Insights puede verse afectado si se escribe o se lee en los blobs con demasiada frecuencia.

Para información general de Azure Blob Storage, consulte el artículo de [Introducción al almacenamiento de blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Para más información sobre el tipo de archivo Parquet, consulte el artículo de [Tipos de archivo admitidos en Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Formato de archivo Parquet

Parquet es un formato de archivo de datos en columnas diseñado con fines de:

* Interoperabilidad
* Ahorro de espacio
* Eficiencia de las consultas

Time Series Insights eligió Parquet porque proporciona compresión de datos eficaz y esquemas de codificación con un rendimiento mejorado que puede controlar datos complejos en bloque.

Para entender mejor el formato de archivo Parquet, consulte la [documentación de Parquet](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Estructura de los eventos en Parquet

Time Series Insights crea blobs y almacena copias suyas en los siguientes formatos:

1. La primera, la copia inicial se divide según la hora de llegada:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Hora de creación del blob para los que se dividen por hora de llegada.

1. La segunda, la copia reparticionada se divide en agrupaciones dinámicas de propiedades Time Series ID:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Marca de tiempo de los eventos mínima en los blobs divididos por propiedades Time Series ID.

> [!NOTE]
> * `<YYYY>` se asigna a una representación de año de 4 dígitos.
> * `<MM>` se asigna a una representación de mes de 2 dígitos.
> * `<YYYYMMDDHHMMSSfff>` se asigna a una representación de marca de tiempo con los 4 dígitos del año (`YYYY`), los 2 dígitos del mes (`MM`), los 2 dígitos del día (`DD`), los 2 dígitos de la hora (`HH`), los 2 dígitos de los minutos (`MM`), los 2 dígitos de los segundos (`SS`) y los 3 dígitos de los milisegundos (`fff`).

Los eventos de Time Series Insights se asignan al contenido de los archivos Parquet como sigue:

* Cada evento se asigna a una sola fila.
* Columna **Timestamp** integrada con marca de tiempo del evento. La propiedad Timestamp nunca es null. Su valor predeterminado es **Event Source Enqueued Time** si no se especifica la propiedad Timestamp en el origen del evento. La marca de tiempo está en formato UTC. 
* Las demás propiedades se asignan a columnas que terminan en `_string` (cadena), `_bool` (booleano), `_datetime` (fecha y hora), y `_double` (doble) en función del tipo de propiedad.
* Este es el esquema de asignación de la primera versión del formato de archivo, que se conoce como **V = 1**. A medida que esta característica evoluciona, el nombre cambiará a **V = 2**, **V = 3**, y así sucesivamente.

## <a name="partitions"></a>Particiones

Cada entorno de versión preliminar de Time Series Insights debe tener una propiedad Time Series ID y una Timestamp que la identifiquen en exclusiva. Time Series ID actúa como partición lógica para los datos y le otorga al entorno de la versión preliminar de Time Series Insights un límite natural de distribución de datos entre las particiones físicas. La versión preliminar de Time Series Insights administra la partición física en una cuenta de almacenamiento de Azure.

Time Series Insights usa la creación dinámica de particiones para abandonar y volver a crear particione y así optimizar el rendimiento de las consultas y del almacenamiento. El algoritmo de creación de particiones de la versión preliminar de Time Series Insights intenta evitar que una partición física contenga datos de distintas particiones lógicas, individuales y múltiples. En otras palabras, mantiene todos los datos específicos de una Time Series ID individual únicamente en los archivos Parquet, sin que se intercale con otras propiedades de este tipo. El algoritmo de creación de particiones dinámico también intenta conservar el orden original de los eventos de una propiedad Time Series ID individual.

Inicialmente, en el momento de la entrada, datos se dividen por la marca de tiempo para que una partición lógica individual de un intervalo de tiempo determinado pueda distribuirse entre varias particiones físicas. Una partición física individual también puede contener varias particiones lógicas, incluso todas. Por las limitaciones de tamaño de los blobs, aunque la división sea óptima, una partición lógica individual puede ocupar varias físicas.

> [!NOTE]
> De forma predeterminada, el valor de Timestamp es el mensaje *Enqueued Time* del origen del evento configurado. 

Al cargar datos históricos o mensajes en lote, asigne el valor que desee almacenar con los datos en la propiedad Timestamp y que se asigne a la marca de tiempo correspondiente. La propiedad Timestamp distingue mayúsculas y minúsculas. Para más información, consulte [Time Series Model](./time-series-insights-update-tsm.md) (Modelo de Time Series).

## <a name="physical-partitions"></a>Particiones físicas

Una partición física es un blob en bloques que se almacena en la cuenta de almacenamiento. El tamaño real de los blobs puede variar, porque el tamaño depende de la velocidad de inserción. Sin embargo, se espera que los blobs tengan un tamaño aproximado de 20 MB a 50 MB. Dentro de este rango, el equipo de Time Series Insights eligió el tamaño de 20 MG para optimizar el rendimiento de las consultas. Este tamaño puede cambiar con el tiempo, dependiendo del tamaño de archivo y la velocidad de entrada de los datos.

> [!NOTE]
> * Los blobs tienen un tamaño de 20 MB.
> * En ocasiones, los blobs de Azure se abandonan y se vuelven a crear para dividirse y así mejorar su rendimiento.
> * Además, los mismos datos de Time Series Insights pueden presentarse en dos o más blobs.

## <a name="logical-partitions"></a>Particiones lógicas

Una partición lógica es aquella dentro de una partición física que almacena todos los datos asociados a un valor de clave de partición única. La versión preliminar de Time Series Insights divide lógicamente cada blob en función de dos propiedades:

* **Time Series ID**: Clave de partición para todos los datos de Time Series Insights del flujo de eventos y del modelo.
* **Timestamp**: Tiempo a partir de la entrada inicial.

La versión preliminar de Time Series Insights ofrece consultas eficientes basadas en estas dos propiedades. Estas dos propiedades también proporcionan el método más eficaz para entregar rápidamente datos de Time Series Insights.

Es importante seleccionar una propiedad Time Series ID adecuada, porque es una propiedad inmutable. Para más información, consulte el artículo sobre la [elección de Time Series ID](./time-series-insights-update-how-to-id.md).

## <a name="your-azure-storage-account"></a>Su cuenta de almacenamiento de Azure

### <a name="storage"></a>Almacenamiento

Al crear un entorno de Time Series Insights de pago por uso, se crean dos recursos: un entorno de Time Series Insights y una cuenta de uso general V1 de Azure Storage donde almacenar los datos. Hemos optado por que la cuenta de uso general V1 de Azure Storage sea el recurso predeterminado por su interoperabilidad, precio y rendimiento. 

Time Series Insights publica hasta dos copias de cada evento en la cuenta de almacenamiento de Azure. La copia inicial siempre se conserva para que pueda consultar rápidamente mediante el uso de otros servicios. Las propiedades Time Series ID se usan fácilmente con Spark, Hadoop y otras herramientas familiares en los archivos Parquet sin procesar, ya que estos motores admiten el filtrado por el nombre del archivo. El agrupamiento de blobs por año y mes resulta útil para enumerarlos en un intervalo de tiempo específico de un trabajo personalizado. 

Además, Time Series Insights vuelve a dividir los archivos Parquet para optimizarlos para sus API. También se guarda el archivo que se ha dividido más recientemente.

Durante la versión preliminar pública, los datos se almacenan indefinidamente en la cuenta de almacenamiento de Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Escritura y edición de blobs de Time Series Insights

Para garantizar el rendimiento de las consultas y la disponibilidad de los datos, no edite ni elimine los blobs que se crean mediante Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acceso a los datos de la versión preliminar de Time Series Insights y exportación

Quizá desee acceder a los datos almacenados en el explorador de la versión preliminar de Time Series Insights para usarlos con otros servicios. Por ejemplo, para crear un informe en Power BI, para el aprendizaje automático mediante Azure Machine Learning Studio o para usarlos en una aplicación de cuaderno de Jupyter Notebook.

En general, se puede acceder a los datos de tres maneras:

* Desde el explorador de la versión preliminar de Time Series Insights.
* Desde las API de la versión preliminar de Time Series Insights.
* Directamente desde una cuenta de almacenamiento de Azure.

#### <a name="from-the-time-series-insights-preview-explorer"></a>Desde el explorador de la versión preliminar de Time Series Insights

Exporte los datos como archivo .csv desde el explorador de la versión preliminar de Time Series Insights. Para más información, consulte el artículo sobre el [Explorador de la versión preliminar de Time Series Insights](./time-series-insights-update-explorer.md).

#### <a name="from-the-time-series-insights-preview-apis"></a>Desde las API de la versión preliminar de Time Series Insights

En `/getRecorded` se encuentra el punto de conexión de la API. Para más información acerca de esta API, consulte el artículo sobre las [Consultas en Time Series](./time-series-insights-update-tsq.md).

#### <a name="from-an-azure-storage-account"></a>Desde una cuenta de almacenamiento de Azure

* Para acceder a los datos de Time Series Insights necesita acceso de lectura en la cuenta que vaya a utilizar para ello. Para más información, consulte el artículo sobre la [Administración del acceso a los recursos de la cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

* Para más información sobre los métodos directos para leer datos de Azure Blob Storage, consulte el artículo sobre el [Movimiento de datos hacia y desde la cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Para exportar datos desde una cuenta de almacenamiento de Azure:

    * En primer lugar, asegúrese de que la cuenta cumple los requisitos necesarios para la exportación. Para más información, consulte el artículo de [Requisitos de importación y exportación de Storage](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

    * Para información sobre otras maneras de exportar datos desde la cuenta de almacenamiento de Azure, consulte el artículo sobre la [Importación y exportación de datos desde los blobs](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Eliminación de datos

No elimine los blobs, ya que la versión preliminar de Time Series Insights conserva metadatos de ellos.

## <a name="ingress"></a>Entrada

### <a name="time-series-insights-ingress-policies"></a>Directivas de entrada de Time Series Insights

La versión preliminar admite los mismos orígenes de eventos y tipos de archivos que los que Time Series Insights admite actualmente.

Orígenes de eventos admitidos:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Las instancias de Azure Event Hubs admiten Kafka.

Tipos de archivo admitidos:

* JSON: Para más información acerca de las formas de JSON admitidas, consulte las [Formas de JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Disponibilidad de los datos

En la versión preliminar de Time Series Insights se indexan los datos mediante una estrategia de optimización basada en el tamaño de los blobs. Los datos estarán disponibles para consulta tras la indexación, que se basa en la cantidad de datos entrantes y su velocidad.

> [!IMPORTANT]
> * La versión de disponibilidad general (GA) de Time Series Insights hará que los datos estén disponibles en un plazo de 60 segundos tras alcanzarse un origen de eventos. 
> * Durante la versión preliminar, los datos tardarán más en estar disponibles. 
> * Si experimenta una latencia significativa, póngase en contacto con nosotros.

### <a name="scale"></a>Escala

La versión preliminar de Time Series Insights permite realizar un escalado de entrada de hasta 6 megabits por segundo (Mbps) en cada entorno. La compatibilidad con el escalado se está mejorando actualmente. Tenemos previsto actualizar la documentación para reflejar esas mejoras.

## <a name="next-steps"></a>Pasos siguientes

Lea [Almacenamiento y entrada de datos en la versión preliminar de Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

Lea sobre el nuevo [Modelo de datos](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
