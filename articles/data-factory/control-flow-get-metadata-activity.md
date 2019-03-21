---
title: Actividad de obtención de metadatos en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo puede usar la actividad GetMetadata en una canalización de Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730706"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Actividad de obtención de metadatos en Azure Data Factory

La actividad GetMetadata se puede usar para recuperar **metadatos** de cualquier dato en Azure Data Factory. Esta actividad se puede usar en los siguientes escenarios:

- Validación de la información de metadatos de datos
- Desencadenamiento de una canalización cuando los datos estén listos/disponibles.

La siguiente funcionalidad está disponible en el flujo de control:

- La salida de la actividad GetMetadata se puede usar en expresiones condicionales para llevar a cabo una validación.
- Se puede desencadenar una canalización si se satisface la condición mediante un bucle Do-Until

## <a name="supported-capabilities"></a>Funcionalidades admitidas

La actividad GetMetadata toma un conjunto de datos como entrada necesaria y genera información de metadatos disponible como salida de actividades. En la actualidad, se admiten los conectores siguientes con los metadatos recuperables correspondientes. Además, el tamaño máximo de metadatos admitido es de hasta **1 MB**.

>[!NOTE]
>Si ejecuta la actividad GetMetadata en una instancia de Integration Runtime autohospedada, se admite la funcionalidad más reciente en la versión 3.6 o una versión posterior. 

### <a name="supported-connectors"></a>Conectores compatibles

**Almacenamiento de archivos:**

| Conector/Metadatos | itemName<br>(archivo/carpeta) | itemType<br>(archivo/carpeta) | size<br>(archivo) | created<br>(archivo/carpeta) | lastModified<br>(archivo/carpeta) |childItems<br>(carpeta) |contentMD5<br>(archivo) | structure<br/>(archivo) | columnCount<br>(archivo) | exists<br>(archivo/carpeta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Blob de Azure | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Sistema de archivos | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Para Amazon S3 y Google Sloud Storage, el `lastModified` se aplica a los depósitos y la clave pero no virtual carpeta; y el `exists` se aplica a los depósitos y clave, pero no prefijo o una carpeta virtual.
- En el caso de Blob de Azure, `lastModified` se aplica al contenedor y al blob, pero no a la carpeta virtual.

**Base de datos relacional:**

| Conector/Metadatos | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Instancia administrada de Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Opciones de metadatos

Los siguientes tipos de metadatos pueden especificarse en la lista de campos de la actividad GetMetadata que se quiere recuperar:

| Tipo de metadatos | DESCRIPCIÓN |
|:--- |:--- |
| itemName | Nombre del archivo o la carpeta. |
| itemType | Tipo del archivo o la carpeta. El valor de salida es `File` o `Folder`. |
| size | Tamaño del archivo en bytes. Se aplica solo al archivo. |
| created | Fecha y hora de creación del archivo o la carpeta. |
| lastModified | Fecha y hora de la última modificación del archivo o la carpeta. |
| childItems | Lista de subcarpetas y archivos dentro de la carpeta determinada. Aplicable solo a la carpeta. El valor de salida es una lista del nombre y tipo de cada elemento secundario. |
| contentMD5 | MD5 del archivo. Se aplica solo al archivo. |
| structure | Estructura de datos en el archivo o tabla de base de datos relacional. El valor de salida es una lista del nombre de columna y el tipo de columna. |
| columnCount | Número de columnas en el archivo o la tabla relacional. |
| exists| Si un archivo/carpeta/tabla existe o no. Tenga en cuenta que, si se especifica "exists" en la lista de campos GetaMetadata, la actividad no producirá errores incluso si no existe el elemento (archivo/carpeta/tabla); en su lugar, devuelve `exists: false` en la salida. |

>[!TIP]
>Si quiere validar si un archivo/carpeta/tabla existe o no, especifique `exists` en la lista de campos de actividad GetMetadata y después compruebe el resultado `exists: true/false` en la salida de actividad. Si `exists` no está configurado en la lista de campos, la actividad GetMetadata producirá un error cuando no se encuentre el objeto.

## <a name="syntax"></a>Sintaxis

**Actividad GetMetadata:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Conjunto de datos:**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propiedades de tipo

Actualmente, la actividad GetMetadata puede capturar los siguientes tipos de información de metadatos.

Propiedad | DESCRIPCIÓN | Obligatorio
-------- | ----------- | --------
fieldList | Enumera los tipos de información de metadatos necesarios. Consulte los detalles en la sección [Opciones de metadatos](#metadata-options) sobre metadatos compatibles. | Sí 
dataset | Conjunto de datos de referencia cuya actividad de metadatos debe recuperar la actividad GetMetadata. Vea la sección [Funcionalidades admitidas](#supported-capabilities) sobre conectores compatibles y consulte el tema del conector sobre los detalles de la sintaxis de los conjuntos de datos. | Sí

## <a name="sample-output"></a>Salida de ejemplo

El resultado de GetMetadata se muestra en la salida de la actividad. A continuación se muestran dos ejemplos con opciones de metadatos exhaustivas seleccionadas en la lista de campos como referencia. Para utilizar el resultado en una actividad posterior, utilice el patrón de `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obtener los metadatos de un archivo

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obtener los metadatos de una carpeta

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
