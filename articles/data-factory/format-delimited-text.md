---
title: Formato de texto delimitado en Azure Data Factory
description: En este tema se describe cómo tratar con el formato de texto delimitado en Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 9a1a019e7dea2f0c71b40530baaffff79d003774
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548285"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado en Azure Data Factory

Siga este artículo cuando quiera **analizar los archivos de texto delimitado o escribir los datos en formato de texto delimitado**. 

El formato de texto delimitado se admite para los conectores siguientes: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) y [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de texto delimitado.

| Propiedad         | DESCRIPCIÓN                                                  | Obligatorio |
| ---------------- | ------------------------------------------------------------ | -------- |
| Tipo             | La propiedad type del conjunto de datos debe establecerse en **DelimitedText**. | Sí      |
| location         | Configuración de ubicación de los archivos. Cada conector basado en archivos tiene su propio tipo de ubicación y propiedades compatibles en `location`.  | Sí      |
| columnDelimiter  | Los caracteres usados para separar las columnas en un archivo. Actualmente, solo se admite el delimitador de varios caracteres para el flujo de datos de asignación, pero no para la actividad de copia. <br>El valor predeterminado es **coma `,`** . Cuando el delimitador de columna se define como cadena vacía —es decir, ningún delimitador—, toda la línea se toma como una sola columna. | Sin       |
| rowDelimiter     | El carácter único o "\r\n" usado para separar las filas en un archivo.<br>El valor predeterminado es cualquiera de los siguientes valores **en lectura: ["\r\n", "\r", "\n"]** , y **"\n" o "\r\n" en escritura** mediante el flujo de datos de asignación y la actividad de copia, respectivamente. <br>Cuando `rowDelimiter` se establece en ningún delimitador (cadena vacía), también `columnDelimiter` debe establecerse como sin delimitador (cadena vacía), lo que significa que se trata todo el contenido como un valor único. | Sin       |
| quoteChar        | El carácter único para entrecomillar los valores de columna si contiene el delimitador de columna. <br>El valor predeterminado es **comillas dobles** `"`. <br>Para el flujo de datos de asignación, `quoteChar` no puede ser una cadena vacía. <br>Para la actividad de copia, cuando `quoteChar` se define como una cadena vacía, significa que no hay ningún carácter de comillas y el valor de la columna no está entre comillas, y `escapeChar` se usa para escapar el delimitador de columna y a sí mismo. | Sin       |
| escapeChar       | El carácter único para escapar las comillas dentro de un valor entre comillas.<br>El valor predeterminado es **barra diagonal inversa`\`** . <br>Para el flujo de datos de asignación, `escapeChar` no puede ser una cadena vacía. <br/>Para la actividad de copia, cuando `escapeChar` se define como una cadena vacía, `quoteChar` también debe establecerse como una cadena vacía, en cuyo caso, asegúrese de que ninguno de los valores de columna contenga delimitadores. | Sin       |
| firstRowAsHeader | Especifica si se debe tratar o convertir la primera fila como una línea de encabezado con nombres de columnas.<br>Los valores permitidos son **true** y **false** (predeterminado). | Sin       |
| nullValue        | Especifica la representación de cadena del valor null. <br>El valor predeterminado es una **cadena vacía**. | Sin       |
| encodingName     | El tipo de codificación usado para leer y escribir archivos de prueba. <br>Los valores permitidos son los siguientes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", “UTF-7”, "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258”.<br>Tenga en cuenta que el flujo de datos de asignación no admite la codificación UTF-7. | Sin       |
| compressionCodec | El códec de compresión usado para leer y escribir archivos de texto. <br>Los valores permitidos son **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy** o **lz4** para usar al guardar el archivo. <br>Tenga en cuenta que actualmente la actividad de copia no admite "snappy" ni "lz4", y que el flujo de datos de asignación no admite "ZipDeflate". <br>Tenga en cuenta que cuando utilice la actividad de copia para descomprimir archivos ZipDeflate y escribir en el almacén de datos de receptores basado en archivos, los archivos se extraerán a la carpeta `<path specified in dataset>/<folder named as source zip file>/`. | Sin       |
| compressionLevel | La razón de compresión. <br>Los valores permitidos son **Optimal** o **Fastest**.<br>- **Fastest:** la operación de compresión debe completarse tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima.<br>- **Optimal**: la operación de compresión se debe comprimir óptimamente, incluso si tarda más tiempo en completarse. Para más información, consulte el tema [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Sin       |

A continuación encontrará un ejemplo de un conjunto de datos de texto delimitado en Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el origen y el receptor de texto delimitado.

### <a name="delimited-text-as-source"></a>Texto delimitado como origen 

En la sección ***\*source\**** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad       | DESCRIPCIÓN                                                  | Obligatorio |
| -------------- | ------------------------------------------------------------ | -------- |
| Tipo           | La propiedad type del origen de la actividad de copia debe establecerse en **DelimitedTextSource**. | Sí      |
| formatSettings | Un grupo de propiedades. Consulte la tabla **Configuración de lectura de texto delimitado** a continuación. | Sin       |
| storeSettings  | Un grupo de propiedades sobre cómo leer datos de un almacén de datos. Cada conector basado en archivos tiene su propia configuración de lectura admitida en `storeSettings`. | Sin       |

**Configuración de lectura de texto delimitado** admitida en `formatSettings`:

| Propiedad      | DESCRIPCIÓN                                                  | Obligatorio |
| ------------- | ------------------------------------------------------------ | -------- |
| Tipo          | El tipo de formatSettings debe establecerse en **DelimitedTextReadSetting**. | Sí      |
| skipLineCount | Indica el número de filas **no vacías** que se omitirán al leer datos de archivos de entrada. <br>Si se especifican skipLineCount y firstRowAsHeader, las líneas se omiten primero y luego la información del encabezado se lee del archivo de entrada. | Sin       |

### <a name="delimited-text-as-sink"></a>Texto delimitado como receptor

En la sección ***\*sink\**** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad       | DESCRIPCIÓN                                                  | Obligatorio |
| -------------- | ------------------------------------------------------------ | -------- |
| Tipo           | La propiedad type del origen de la actividad de copia debe establecerse en **DelimitedTextSink**. | Sí      |
| formatSettings | Un grupo de propiedades. Consulte la tabla **Configuración de escritura de texto delimitado** a continuación. |          |
| storeSettings  | Un grupo de propiedades sobre cómo escribir datos en un almacén de datos. Cada conector basado en archivos tiene su propia configuración de escritura admitida en `storeSettings`.  | Sin       |

**Configuración de escritura de texto delimitado** admitida en `formatSettings`:

| Propiedad      | DESCRIPCIÓN                                                  | Obligatorio                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Tipo          | El tipo de formatSettings debe establecerse en **DelimitedTextWriteSetting**. | Sí                                                   |
| fileExtension | La extensión de archivo que se usa para denominar los archivos de salida, por ejemplo, `.csv`, `.txt`. Debe especificarse cuando no se especifica `fileName` en el conjunto de datos DelimitedText de salida. | Sí, cuando no se especifica el nombre de archivo en el conjunto de datos de salida |

## <a name="mapping-data-flow-properties"></a>Propiedades de Asignación de instancias de Data Flow

Obtenga información detallada de la [transformación de origen](data-flow-source.md) y la [transformación de receptor](data-flow-sink.md) en la asignación de flujo de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
