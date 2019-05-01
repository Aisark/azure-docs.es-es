---
title: Uso de datos de referencia para las búsquedas en Azure Stream Analytics
description: En este artículo se describe cómo usar los datos de referencia para buscar o poner en correlación datos en el diseño de consultas de un trabajo de Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 2400f80c67527027aee3a98baaa869c5c66d46ee
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573638"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Uso de datos de referencia para las búsquedas en Stream Analytics
Los datos de referencia (también denominados tabla de consulta) son un conjunto finito de datos estáticos o de cambio lento de naturaleza, que se usan para realizar una búsqueda o para relacionarlos con el flujo de datos. Por ejemplo, en un escenario de IoT, podría almacenar los metadatos sobre sensores (que no cambian a menudo) en los datos de referencia y combinarlos con los flujos de datos de IoT en tiempo real. Azure Stream Analytics carga los datos de referencia en la memoria para lograr un procesamiento del flujo de baja latencia. Para usar los datos de referencia en el trabajo de Azure Stream Analytics, por lo general usará una [combinación de datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. 

Stream Analytics admite Azure Blob Storage y Azure SQL Database como capa de almacenamiento de los datos de referencia. También puede transformar o copiar datos de referencia a Blob Storage desde Azure Data Factory para usar [cualquier número de almacenes de datos basados en la nube y locales](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Los datos de referencia se modelan como una secuencia de blobs (que se define en la configuración de entrada) en orden ascendente por la fecha y hora que se especifique en el nombre del blob. **Solo** se admite que se agreguen al final de la secuencia con una fecha y hora **posteriores** a las especificadas en el último blob de la secuencia.

### <a name="configure-blob-reference-data"></a>Configuración de los datos de referencia de blob

Para configurar los datos de referencia, tiene que crear primero una entrada que sea del tipo **Datos de referencia**. En la siguiente tabla se explica cada propiedad que necesitará proporcionar al crear la entrada de los datos de referencia con su descripción:

|**Nombre de la propiedad**  |**Descripción**  |
|---------|---------|
|Alias de entrada   | Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.   |
|Cuenta de almacenamiento   | El nombre de la cuenta de almacenamiento donde se encuentran los blobs. Si está en la misma suscripción que su trabajo de Stream Analytics, puede seleccionarla en el menú desplegable.   |
|Clave de cuenta de almacenamiento   | La clave secreta asociada con la cuenta de almacenamiento. Se rellena automáticamente si la cuenta de almacenamiento está en la misma suscripción que el trabajo de Stream Analytics .   |
|Contenedor de almacenamiento   | Los contenedores proporcionan una agrupación lógica de los blobs almacenados en Microsoft Azure Blob service. Cuando carga un blob a Blob service, debe especificar un contenedor para ese blob.   |
|Patrón de la ruta de acceso   | La ruta de acceso para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes dos variables:<BR>{date}, {time}<BR>Ejemplo 1: products/{date}/{time}/product-list.csv<BR>Ejemplo 2: products/{date}/product-list.csv<BR>Ejemplo 3: product-list.csv<BR><br> Si el blob no existe en la ruta de acceso especificada, el trabajo de Stream Analytics esperará indefinidamente a que el blob esté disponible.   |
|Formato de fecha [opcional]   | Si ha usado {date} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de fecha en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: AAAA/MM/DD, MM/DD/AAAA, etc.   |
|Formato de hora [opcional]   | Si ha usado {time} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de hora en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: HH, HH/mm o HH:mm.  |
|Formato de serialización de eventos   | Para asegurarse de que las consultas funcionen como se espera, Stream Analytics debe saber cuál es el formato de serialización que usa para los flujos de datos entrantes. Para los datos de referencia, los formatos admitidos son CSV y JSON.  |
|Encoding   | Por el momento, UTF-8 es el único formato de codificación compatible.  |

### <a name="static-reference-data"></a>Datos de referencia estáticos

Si no se espera que cambien los datos de referencia, especifique una ruta de acceso estática en la configuración de entrada para habilitar la compatibilidad con datos de referencia estáticos. Azure Stream Analytics selecciona el blob desde la ruta de acceso especificada. Los tokens de sustitución {date} y {time} no son necesarios. Los datos de referencia son inmutables en Stream Analytics. Por lo tanto, no se recomienda sobrescribir un blob de datos de referencia estáticos.

### <a name="generate-reference-data-on-a-schedule"></a>Generación de datos de referencia en una programación

Si los datos de referencia son un conjunto de datos que cambia con poca frecuencia, se pueden actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada con los tokens de sustitución de {date} y {time}. Stream Analytics selecciona las definiciones actualizadas de los datos de referencia según este patrón de ruta de acceso. Por ejemplo, un patrón de `sample/{date}/{time}/products.csv` con un formato de fecha de **"AAAA-MM-DD"** y un formato de hora de **"HH:mm"** indica a Stream Analytics que seleccione el blob actualizado `sample/2015-04-16/17-30/products.csv` a las 17:30 del 16 de abril de 2015 (zona horaria UTC).

Azure Stream Analytics examina automáticamente los blobs de datos de referencia actualizados en un intervalo de un minuto. Si se carga un blob con 10:30:00 de la marca de tiempo con un retraso pequeño (por ejemplo, 10:30:30), observará una pequeña demora en el trabajo de Stream Analytics que hacen referencia a este blob. Para evitar tales escenarios, se recomienda para cargar el blob anteriormente a la fecha efectiva del objetivo (10: 30:00 en este ejemplo) para permitir que el trabajo de Stream Analytics tiempo suficiente para detectar y cargarlo en la memoria y realizar operaciones. 

> [!NOTE]
> En estos momentos, los trabajos de Stream Analytics buscan la actualización de blobs solo cuando la hora de la máquina coincide con la hora codificada en el nombre del blob. Por ejemplo, el trabajo buscará `sample/2015-04-16/17-30/products.csv` en cuanto sea posible, pero no antes de las 17:30 del 16 de abril de 2015 (zona horaria UTC). No buscará *nunca* un blob con una hora codificada anterior a la última detectada.
> 
> Por ejemplo, una vez que el trabajo encuentre el blob `sample/2015-04-16/17-30/products.csv`, ignorará los archivos cuya fecha codificada sea anterior a las 17:30 del 16 de abril de 2015. Por tanto, si un blob `sample/2015-04-16/17-25/products.csv` se crea en el mismo contenedor posteriormente, el trabajo no lo usará.
> 
> Del mismo modo, si `sample/2015-04-16/17-30/products.csv` solo se crea a las 22:03 del 16 de abril de 2015, pero en el contenedor no hay ningún blob con una fecha anterior, el trabajo usará este archivo a partir de las 22:03 del 16 de abril de 2015, además de los datos de referencia anteriores hasta ese momento.
> 
> Una excepción se produce cuando el trabajo debe volver a procesar datos anteriores en el tiempo o cuando se inicia por primera vez. En momento de iniciarse, el trabajo busca el blob más reciente generado antes de la hora de inicio del trabajo especificada. Esto se hace para garantizar que haya un conjunto de datos de referencia **no vacío** al iniciarse el trabajo. Si no se encuentra ninguno, el trabajo muestra el diagnóstico siguiente: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) puede usarse para organizar la tarea de crear los blobs actualizados requeridos por Stream Analytics para actualizar las definiciones de datos de referencia. Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Factoría de datos admite la [conexión a un gran número de almacenes de datos locales y en la nube](../data-factory/copy-activity-overview.md) y el desplazamiento sencillo de los datos con la regularidad que se especifique. Para obtener más información e instrucciones paso a paso sobre cómo configurar una canalización de Factoría de datos para generar datos de referencia para Stream Analytics que se actualiza según una programación predefinida, consulte este [ejemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Sugerencias sobre cómo actualizar los datos de referencia de blob

1. No sobrescriba los blobs de datos de referencia, ya que son inmutables.
2. El método recomendado para actualizar los datos de referencia es:
    * Usar {date}/{time} en el patrón de ruta de acceso
    * Agregar un nuevo blob con el mismo patrón de contenedor y ruta de acceso definido en la entrada del trabajo
    * Usar una fecha y hora **mayor** que la especificada en el último blob de la secuencia.
3. Los blobs de datos de referencia **no** se ordenan por la hora de "Última modificación" del blob sino únicamente por la fecha y la hora que se especifiquen en el nombre del blob mediante las sustituciones de {date} y {time}.
3. Para evitar tener que enumerar un gran número de blobs, considere la posibilidad de eliminar los blobs muy antiguos para los que ya no se va a realizar el procesamiento. Tenga en cuenta que ASA puede tener que reprocesar una pequeña cantidad en algunos escenarios como un reinicio.

## <a name="azure-sql-database-preview"></a>Azure SQL Database (versión preliminar)

Su trabajo de Stream Analytics recupera los datos de referencia de Azure SQL Database, que se almacenan como instantánea en la memoria para su procesamiento. La instantánea de los datos de referencia también se almacena en un contenedor en una cuenta de almacenamiento que especifique en las opciones de configuración. El contenedor se crea automáticamente cuando se inicia el trabajo. Si el trabajo se detiene o entra en un estado de error, cuando se reinicia, se eliminan los contenedores creados automáticamente.  

Si los datos de referencia están un conjunto de datos de variación lenta, deberá actualizar periódicamente la instantánea que se usa en su trabajo. Stream Analytics le permite establecer una frecuencia de actualización al configurar la conexión de entrada de Azure SQL Database. El tiempo de ejecución de Stream Analytics consultará a Azure SQL Database al intervalo especificado por la frecuencia de actualización. La frecuencia de actualización más rápida que se admite es de una vez por minuto. Para cada actualización, Stream Analytics almacena una nueva instantánea en la cuenta de almacenamiento proporcionada.

Stream Analytics proporciona dos opciones para hacer consultas en la instancia de Azure SQL Database. Una consulta de instantánea es obligatoria y debe incluirse en cada trabajo. Stream Analytics ejecuta la consulta de instantánea periódicamente según el intervalo de actualización, y usa el resultado de la consulta (la instantánea) como conjunto de datos de referencia. La consulta de instantánea debe ajustarse a la mayoría de los escenarios, pero si experimenta problemas de rendimiento con grandes conjuntos de datos y tasas de actualización rápidas, puede usar la opción de consulta delta. Las consultas que tarden más de 60 segundos para devolver el conjunto de datos de referencia dará como resultado un tiempo de espera.

Con la opción de consulta delta, Stream Analytics ejecuta la consulta de instantánea inicialmente para obtener un conjunto de datos de referencia de línea base. Después, Stream Analytics ejecuta la consulta delta periódicamente según el intervalo de actualización para recuperar los cambios incrementales. Estos cambios incrementales se aplican de manera continua al conjunto de datos de referencia para mantenerlo actualizado. Usar una consulta delta puede ayudar a reducir los costos de almacenamiento y las operaciones de E/S de la red.

### <a name="configure-sql-database-reference"></a>Configuración de la referencia SQL Database

Para configurar los datos de referencia de SQL Database, tiene que crear primero una entrada **Datos de referencia**. En la siguiente tabla se explica cada propiedad que tendrá que proporcionar al crear la entrada de los datos de referencia, junto con su descripción. Para más información, consulte [Use reference data from a SQL Database for an Azure Stream Analytics job](sql-reference-data.md) (Uso de datos de referencia de una instancia de SQL Database para un trabajo de Azure Stream Analytics).

|**Nombre de la propiedad**|**Descripción**  |
|---------|---------|
|Alias de entrada|Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.|
|Subscription|Elija una suscripción|
|Base de datos|La instancia de Azure SQL Database que contiene los datos de referencia.|
|Nombre de usuario|El nombre de usuario asociado a la instancia de Azure SQL Database.|
|Contraseña|La contraseña asociada a la instancia de Azure SQL Database.|
|Actualizar periódicamente|Esta opción le permite elegir una frecuencia de actualización. Si elige "Activado", podrá especificar la frecuencia de actualización en DD:HH:MM.|
|Consulta de instantánea|Esta es la opción de consulta predeterminada que recupera los datos de referencia de SQL Database.|
|Consulta delta|Para escenarios avanzados con grandes conjuntos de datos y una breve frecuencia de actualización, elija agregar una consulta delta.|

## <a name="size-limitation"></a>Limitación del tamaño

Stream Analytics admite datos de referencia con **tamaño máximo de 300 MB**. El tamaño máximo de 300 MB de datos de referencia es factible solo con consultas sencillas. A medida que aumenta la complejidad de la consulta para incluir el procesamiento con estado, tal como los agregados por intervalos de tiempo, combinaciones temporales y funciones de análisis temporal, se espera que se reduzca el tamaño máximo admitido de datos de referencia. Si Azure Stream Analytics no puede cargar los datos de referencia y realizar operaciones complejas, el trabajo se quedará sin memoria y producirá un error. En estos casos, la métrica de porcentaje de uso de SU llegará a 100 %.    

|**Número de unidades de streaming**  |**Tamaño máximo aprox. admitido (en MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 y más   |300   |

Aumentar el número de unidades de streaming de un trabajo a más de 6 no aumenta el tamaño máximo admitido de datos de referencia.

La compatibilidad con la compresión no está disponible para los datos de referencia. 

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
