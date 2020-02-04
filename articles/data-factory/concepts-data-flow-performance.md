---
title: Guía de optimización y rendimiento del flujo de datos de asignación
description: Conozca los factores clave que afectan al rendimiento de los flujos de datos de asignación en Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 01/25/2020
ms.openlocfilehash: ff128d148abb87959894aee94d257ae71a3ca65e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773845"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guía de optimización y rendimiento de la asignación de instancias de Data Flow

Mapping Data Flows de Azure Data Factory proporciona una interfaz sin código para diseñar, implementar y orquestar transformaciones de datos a escala. Si no está familiarizado con los flujos de datos de asignación, consulte [Introducción a Mapping Data Flow](concepts-data-flow-overview.md).

Al diseñar y probar flujos de datos desde la interfaz de usuario de ADF, asegúrese de activar el modo de depuración para ejecutar los flujos de datos en tiempo real sin esperar a que se prepare un clúster. Para más información, consulte [Modo de depuración](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Supervisión del rendimiento de flujo de datos

Al diseñar flujos de datos de asignación, puede hacer una prueba unitaria de cada transformación si hace clic en la pestaña Vista previa de datos del panel Configuración. Una vez que compruebe la lógica, pruebe el flujo de datos de un extremo a otro como una actividad en una canalización. Agregue una actividad de ejecución de Data Flow y use el botón de depuración para probar el rendimiento del flujo de datos. Para abrir el plan de ejecución y el perfil de rendimiento del flujo de datos, haga clic en el icono de anteojos en "acciones" en la pestaña Salida de la canalización.

![Supervisión de Data Flow](media/data-flow/mon002.png "Supervisión de Data Flow 2")

 Puede usar esta información para calcular el rendimiento del flujo de datos en orígenes de datos de diferente tamaño. Para más información, consulte [Supervisión de los flujos de datos de asignación](concepts-data-flow-monitoring.md).

![Supervisión de Data Flow](media/data-flow/mon003.png "Supervisión de Data Flow 3")

 En el caso de las ejecuciones de depuración de canalización, se requiere aproximadamente un minuto de tiempo de configuración del clúster en los cálculos de rendimiento generales para preparar un clúster. Si va a inicializar la instancia de Azure Integration Runtime predeterminada, el tiempo de giro puede ser de unos 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento del tamaño de proceso en Azure Integration Runtime

Un entorno Integration Runtime con más núcleos aumenta el número de nodos en los entornos de proceso de Spark y proporciona más capacidad de procesamiento para leer, escribir y transformar los datos.
* Pruebe un clúster **optimizado para proceso**, si desea que la velocidad de procesamiento sea mayor que la velocidad de entrada.
* Pruebe un clúster **optimizado para memoria** si desea almacenar en caché más datos en memoria.

![Nuevo IR](media/data-flow/ir-new.png "Nuevo IR")

Para más información sobre cómo crear un entorno de Integration Runtime, consulte [Integration Runtime en Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumento del tamaño del clúster de depuración

De forma predeterminada, al activar la depuración se usará el entorno Azure Integration Runtime predeterminado que se crea automáticamente para cada factoría de datos. Este Azure IR predeterminado se establece para ocho núcleos, cuatro para un nodo de controlador y cuatro para un nodo de trabajo, mediante propiedades de proceso generales. A medida que pruebe con datos de mayor tamaño, puede aumentar el tamaño del clúster de depuración mediante la creación de un Azure IR con configuraciones mayores y elegir este nuevo Azure IR al activar la depuración. Esto indicará a ADF que use este Azure IR para la vista previa de los datos y la depuración de la canalización con flujos de datos.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimización para Azure SQL Database y Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Creación de particiones en el origen

1. Vaya a la pestaña **Optimizar** y seleccione **Set Partitioning** (Establecer particiones).
1. Seleccione **Origen**.
1. En **Número de particiones**, establezca el número máximo de conexiones a la instancia de Azure SQL DB. Puede probar un valor más alto para obtener conexiones en paralelo a la base de datos. Pero algunos casos pueden provocar un rendimiento más rápido con un número limitado de conexiones.
1. Seleccione si desea crear particiones por una columna de tabla específica o una consulta.
1. Si seleccionó **Columna**, elija la columna de partición.
1. Si seleccionó **Consulta**, escriba una consulta que coincida con el esquema de partición de la tabla de base de datos. Esta consulta permite que el motor de base de datos de origen aproveche la eliminación de particiones. No es necesario crear particiones de las tablas de la base de datos de origen. Si todavía no se han creado particiones en el origen, ADF seguirá usando la creación de particiones en el entorno de transformación de Spark en función de la clave que seleccione en la transformación de origen.

![Elemento de origen](media/data-flow/sourcepart3.png "Elemento de origen")

### <a name="source-batch-size-input-and-isolation-level"></a>Tamaño de lote de origen, entrada y nivel de aislamiento

En **Opciones de origen**, en la transformación de origen, la siguiente configuración puede afectar al rendimiento:

* El tamaño de lote indica a ADF que almacene los datos en conjuntos en memoria en lugar de hacerlo fila a fila. El tamaño de lote es una opción opcional y es posible que se quede sin recursos en los nodos de proceso si no tienen el tamaño correcto.
* El establecimiento de una consulta puede permitirle filtrar las filas en el origen antes de que lleguen a Data Flow para procesarse. Esto puede agilizar la adquisición de datos inicial. Si usa una consulta, puede agregar sugerencias de consulta opcionales para Azure SQL DB, como READ UNCOMMITTED.
* La lectura sin confirmar proporcionará resultados de consulta más rápidos en la transformación de origen.

![Origen](media/data-flow/source4.png "Source")

### <a name="sink-batch-size"></a>Tamaño de lote del receptor

Para evitar el procesamiento fila por fila de los flujos de datos, establezca el **Tamaño de lote** en la pestaña Configuración de los receptores de Azure SQL DB y Azure SQL Data Warehouse. Si se establece el tamaño de lote, ADF procesa las operaciones de escritura de base de datos en lotes según el tamaño especificado.

![Sink](media/data-flow/sink4.png "Receptor")

### <a name="partitioning-on-sink"></a>Creación de particiones en el receptor

Incluso si no tiene los datos particionados en las tablas de destino, se recomienda que los datos se particionen en la transformación del receptor. A menudo, los datos con particiones producen una carga mucho más rápida y obligan a todas las conexiones a usar un solo nodo o partición. Vaya a la pestaña Optimizar del receptor y seleccione el particionamiento *Round Robin* para seleccionar el número ideal de particiones que se van a escribir en el receptor.

### <a name="disable-indexes-on-write"></a>Deshabilitación de índices durante la escritura

En su canalización, agregue una [actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) antes de la actividad de Data Flow que deshabilita los índices en las tablas de destino en las que se escribe desde el receptor. Después de la actividad de Data Flow, agregue otra actividad de procedimiento almacenado que habilite estos índices. O bien, use los scripts anteriores y posteriores al procesamiento en un receptor de base de datos.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumento del tamaño de las instancias de Azure SQL DB y Azure SQL Data Warehouse

Programe un cambio de tamaño del origen y el receptor de Azure SQL DB y Azure SQL DataWarehouse antes de que la canalización se ejecute para aumentar el rendimiento y minimizar la limitación de Azure una vez que se alcancen los límites de DTU. Una vez completada la ejecución de la canalización, cambie el tamaño de las bases de datos a la velocidad de ejecución normal.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Solo para Azure SQL Data Warehouse] Uso de almacenamiento provisional para cargar datos de forma masiva a través de Polybase

Para evitar las inserciones fila a fila en el almacenamiento de datos, consulte **Habilitación del almacenamiento provisional** en la configuración del receptor para que ADF pueda usar [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase permite a ADF cargar los datos de forma masiva.
* Al ejecutar la actividad de flujo de datos desde una canalización, deberá seleccionar la ubicación de almacenamiento Blob o ADLS Gen2 para almacenar provisionalmente los datos durante la carga masiva.

## <a name="optimizing-for-files"></a>Optimización de los archivos

En cada transformación, puede establecer el esquema de partición que desee que use la factoría de datos en la pestaña Optimizar.
* Para los archivos más pequeños, puede comprobar que seleccionar *Partición única* en ocasiones funciona mejor y más rápido que solicitar a Spark que cree particiones de los archivos pequeños.
* Si no tiene información suficiente sobre los datos de origen, elija la creación de particiones *Round Robin* y establezca el número de particiones.
* Si los datos tienen columnas que pueden ser claves de hash correctas, elija *Creación de particiones por hash*.

Al depurar en la vista previa de datos y la depuración de la canalización, el límite y los tamaños de muestreo de los conjuntos de datos de origen basados en archivos solo se aplican al número de filas devueltas, no al número de filas leídas. Esto puede afectar al rendimiento de sus ejecuciones de depuración y, posiblemente, hacer que se produzca un error en el flujo.
* Los clústeres de depuración son pequeños clústeres de un solo nodo de forma predeterminada, de modo que se recomienda usar archivos pequeños de ejemplo para la depuración. Vaya a Configuración de depuración y seleccione un pequeño subconjunto de sus datos mediante un archivo temporal.

    ![Configuración de depuración](media/data-flow/debugsettings3.png "Configuración de depuración")

### <a name="file-naming-options"></a>Opciones de nombre de archivo

La forma más común de escribir datos transformados en flujos de datos de asignación es el almacenamiento de blobs o de archivos ADLS. En el receptor, debe seleccionar un conjunto de datos que apunte a un contenedor o carpeta, no a un archivo con nombre. A medida que el flujo de datos de asignación usa Spark para la ejecución, el resultado se divide en varios archivos según el esquema de partición.

Un esquema de particionamiento común es elegir _Salida en un solo archivo_, que combina todos los archivos PART de salida en un solo archivo del receptor. Esta operación requiere que la salida se reduzca a una única partición en un único nodo de clúster. Si combina muchos archivos de origen grandes en un solo archivo de salida, puede quedarse sin recursos de nodo de clúster.

Para evitar que se agoten los recursos de nodo de proceso, mantenga el esquema optimizado predeterminado en el flujo de datos y agregue una actividad de copia en la canalización que combine todos los archivos PART de la carpeta de salida en un único archivo nuevo. Esta técnica separa la acción de transformación de la combinación de los archivos y consigue el mismo resultado que la configuración de _Salida a un archivo único_.

### <a name="looping-through-file-lists"></a>Bucles a través de listas de archivos

Un flujo de datos de asignación se ejecutará mejor cuando la transformación de origen recorra en iteración varios archivos en lugar de crear bucles a través de la actividad For Each. Se recomienda usar caracteres comodín o listas de archivos en la transformación de origen. El proceso de Data Flow se ejecutará más rápido si se permiten bucles dentro del clúster de Spark. Para más información, consulte [Caracteres comodín en la transformación de origen](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Por ejemplo, si tiene una lista de archivos de datos de julio de 2019 que desea procesar en una carpeta en Blob Storage, a continuación se muestra un carácter comodín que puede usar en la transformación de origen.

```DateFiles/*_201907*.txt```

Mediante el uso de caracteres comodín, la canalización solo contendrá una actividad de Data Flow. Esto funcionará mejor que una búsqueda contra el almacén de blobs que luego itera en todos los archivos coincidentes con una operación ForEach que incluya una actividad de ejecución de Data Flow.

### <a name="optimizing-for-cosmosdb"></a>Optimización de CosmosDB

Establecer las propiedades throughput y batch en los receptores de CosmosDB solo surte efecto durante la ejecución de un flujo de datos desde una actividad de flujo de datos de canalización. CosmosDB respetará la configuración de la colección original se después de la ejecución del flujo de datos.

* Tamaño de lote: Calcule el tamaño de fila aproximado de los datos y asegúrese de que el tamaño del lote de rowSize * es inferior a 2 millones. Si lo es, aumente el tamaño del lote para obtener un mejor rendimiento
* Rendimiento: establezca aquí un valor de rendimiento mayor aquí para que los documentos escriban más rápidamente en CosmosDB. Tenga en cuenta que los costos de RU son mayores ya que el valor de rendimiento es mayor.
*   Presupuesto de rendimiento de escritura: use un valor que sea menor que el total de RU por minuto. Si tiene un flujo de datos con un número elevado de particiones de Spark y establece un presupuesto de rendimiento, habrá más equilibrio entre las particiones.

## <a name="join-performance"></a>Rendimiento de combinaciones

La administración del rendimiento de las combinaciones en el flujo de datos es una operación muy común que realizará a lo largo del ciclo de vida de las transformaciones de datos. En ADF, los flujos de datos no requieren que los datos se ordenen antes de las combinaciones, ya que estas operaciones se realizan como combinaciones hash en Spark. Sin embargo, puede beneficiarse de un rendimiento mejorado con la optimización de combinaciones de "difusión". Esto evitará el orden aleatorio, ya que se inserta el contenido de cualquiera de los lados de la relación de combinación en el nodo de Spark. Esto funciona bien con las tablas más pequeñas que se usan para las búsquedas de referencias. Las tablas de mayor tamaño que pueden no caber en la memoria del nodo no son buenas candidatas para la optimización de difusión.

Otra optimización de combinaciones consiste en crear combinaciones de manera que se evite la tendencia de Spark a implementar combinaciones cruzadas. Por ejemplo, cuando se incluyen valores literales en las condiciones de combinación, Spark puede considerar esto como requisito para realizar primero un producto cartesiano completo, para luego filtrar los valores combinados. Pero si se asegura de tener valores de columna en ambos lados de la condición de combinación, puede evitar este producto cartesiano inducido por Spark y mejorar el rendimiento de las combinaciones y los flujos de datos.

## <a name="next-steps"></a>Pasos siguientes

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Pestaña Optimizar de la transformación de Mapping Data Flow](concepts-data-flow-overview.md#optimize)
- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
