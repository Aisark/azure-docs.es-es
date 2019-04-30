---
title: Conjuntos de datos y servicios vinculados en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre los conjuntos de datos y los servicios vinculados en Azure Data Factory. Los servicios vinculados vinculan almacenes de datos o servicios de proceso a una factoría de datos. Los conjuntos de datos representan datos de entrada y salida.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 9e5da96cb02e681c83bd707fc038117050712ccf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262057"
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Conjuntos de datos y servicios vinculados en Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1](v1/data-factory-create-datasets.md)
> * [Versión actual](concepts-datasets-linked-services.md)

En este artículo se describe qué son los conjuntos de datos, cómo se definen en formato JSON y cómo se usan en canalizaciones de Azure Data Factory.

Si no está familiarizado con Data Factory, consulte [Introducción a Azure Data Factory](introduction.md) para obtener información general.

## <a name="overview"></a>Información general
Una factoría de datos puede tener una o más canalizaciones. Una **canalización** es una agrupación lógica de **actividades** que realizan una tarea. Las actividades de una canalización definen las acciones que se van a realizar en los datos. Por ejemplo, puede usar una actividad de copia para copiar datos de un servidor SQL Server local en una instancia de Azure Blob Storage. Después, podría usar una actividad de Hive que ejecute un script de Hive en un clúster de Azure HDInsight para procesar datos de Blob Storage con el fin de generar datos de salida. Finalmente, podría usar segunda actividad de copia para copiar los datos de salida en una instancia de Azure SQL Data Warehouse en función de qué soluciones de generación de informes de inteligencia empresarial (BI) estén integradas. Para más información sobre canalizaciones y actividades, consulte el artículo [Pipelines and activities](concepts-pipelines-activities.md) (Canalizaciones y actividades) en Azure Data Factory.

Ahora, un **conjunto de datos** es una vista con nombre de los datos que simplemente apunta o hace referencia a los datos que desea usar en sus **actividades** como entradas y salidas. Los conjuntos de datos identifican datos en distintos almacenes de datos, como tablas, archivos, carpetas y documentos. Por ejemplo, un conjunto de datos de blob de Azure especifica el contenedor de blobs y la carpeta de Blob Storage de los que la actividad debe leer los datos.

Antes de crear un conjunto de datos, debe crear un **servicio vinculado** para vincular su almacén de datos a la factoría de datos. Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: el conjunto de datos representa la estructura de los datos dentro de los almacenes de datos vinculados y el servicio vinculado define la conexión al origen de datos. Por ejemplo, un servicio vinculado Azure Storage vincula una cuenta de almacenamiento a la factoría de datos. Un conjunto de datos de blobs de Azure representa el contenedor de blobs y la carpeta dentro de esa cuenta de Azure Storage que contiene los blobs de entrada que se van a procesar.

Este es un escenario de ejemplo. Para copiar datos de Blob Storage a una base de datos SQL, creará dos servicios vinculados: Microsoft Azure Storage y Azure SQL Database. Después, creará dos conjuntos de datos: el conjunto de datos de un blob de Azure (que hace referencia al servicio vinculado Azure Storage) y el conjunto de datos de Azure SQL Table (que hace referencia al servicio vinculado Azure SQL Database). Los servicios vinculados Azure Storage y Azure SQL Database contienen cadenas de conexión que Data Factory usa en tiempo de ejecución para conectarse a las instancias de Azure Storage y Azure SQL Database, respectivamente. El conjunto de datos Azure Blob especifica el contenedor de blobs y la carpeta de blobs que contiene los blobs de entrada de Blob Storage. El conjunto de datos Azure SQL Table especifica la tabla de SQL de la base de datos SQL en la que se van a copiar los datos.

En el siguiente diagrama se muestra la relación entre la canalización, la actividad, el conjunto de datos y el servicio vinculado en Data Factory:

![Relación entre la canalización, la actividad, el conjunto de datos y los servicios vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Servicio vinculado JSON
Un servicio vinculado de Data Factory se define con formato JSON de la manera siguiente:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La tabla siguiente describe las propiedades del JSON anterior:

Propiedad | DESCRIPCIÓN | Obligatorio |
-------- | ----------- | -------- |
Nombre | Nombre del servicio vinculado. Consulte [Azure Data Factory - Naming rules](naming-rules.md) (Azure Data Factory: reglas de nomenclatura). |  Sí |
Tipo | Tipo de servicio vinculado. Por ejemplo:  AzureStorage (almacén de datos) o AzureBatch (proceso). Vea la descripción de typeProperties. | Sí |
typeProperties | Las propiedades de tipo son diferentes para cada almacén de datos o proceso. <br/><br/> Para los tipos de almacenes de datos compatibles y sus propiedades de tipo, consulte la tabla [Tipo de conjunto de datos](#dataset-type) en este artículo. Vaya al artículo del conector del almacén de datos para obtener información acerca de las propiedades de tipo específicas de un almacén de datos. <br/><br/> Para los tipos de procesos compatibles y sus propiedades de tipo, vea [Servicios de proceso vinculados](compute-linked-services.md). | Sí |
connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. | Sin 

## <a name="linked-service-example"></a>Ejemplo de servicio vinculado
El siguiente servicio vinculado no es un servicio vinculado de Azure Storage. Tenga en cuenta que el tipo está establecido en AzureStorage. Las propiedades de tipo del servicio vinculado de Azure Storage incluyen una cadena de conexión. El servicio Data Factory utiliza esta cadena de conexión para conectarse al almacén de datos en tiempo de ejecución.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>Conjunto de datos JSON
Un conjunto de datos de Data Factory se define con formato JSON de la manera siguiente:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
La tabla siguiente describe las propiedades del JSON anterior:

Propiedad | DESCRIPCIÓN | Obligatorio |
-------- | ----------- | -------- |
Nombre | Nombre del conjunto de datos. Consulte [Azure Data Factory - Naming rules](naming-rules.md) (Azure Data Factory: reglas de nomenclatura). |  Sí |
Tipo | Tipo de conjunto de datos. Especifique uno de los tipos admitidos por Data Factory (por ejemplo: AzureBlob, AzureSqlTable). <br/><br/>Para más información, consulte [Dataset types](#dataset-type) (Tipo de conjunto de datos). | Sí |
structure | Esquema del conjunto de datos. Para más información, consulte [Estructura del conjunto de datos](#dataset-structure). | Sin  |
typeProperties | Las propiedades de tipo son diferentes para cada tipo (por ejemplo: blob de Azure, tabla de Azure SQL). Para más información sobre los tipos admitidos y sus propiedades, consulte [Tipo de conjunto de datos](#dataset-type). | Sí |

## <a name="dataset-example"></a>Ejemplo de conjunto de datos
En el siguiente ejemplo, el conjunto de datos representa una tabla llamada MyTable en una base de datos SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Tenga en cuenta los siguientes puntos:

- type está establecido en AzureSqlTable.
- La propiedad de tipo tableName (específico del tipo AzureSqlTable) se establece en MyTable.
- linkedServiceName hace referencia a un servicio vinculado de tipo AzureSqlDatabase, que se define en el siguiente fragmento JSON.

## <a name="dataset-type"></a>Tipo de conjunto de datos
Hay muchos tipos diferentes de conjuntos de datos, según el almacén de datos que usa. Vea la tabla siguiente para obtener una lista de almacenes de datos compatibles con Data Factory. Haga clic en un almacén de datos para obtener información sobre cómo crear un servicio vinculado y un conjunto de datos para ese almacén de datos.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

En el ejemplo en la sección anterior, el tipo del conjunto de datos se establece en **AzureSqlTable**. De forma similar, para un conjunto de datos Azure Blob, el tipo del conjunto de datos se establece en **AzureBlob**, tal y como se muestra en el siguiente esquema JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>Estructura del conjunto de datos
La sección **structure** es opcional. Define el esquema del conjunto de datos al contener una colección de nombres y tipos de datos de columnas. La sección "structure" se usa para proporcionar el tipo de información que se utiliza para convertir tipos y columnas de mapas del origen al destino.

Cada columna de la estructura contiene las siguientes propiedades:

Propiedad | DESCRIPCIÓN | Obligatorio
-------- | ----------- | --------
Nombre | Nombre de la columna. | Sí
Tipo | Tipo de datos de la columna. Data Factory admite los siguientes tipos de datos provisionales: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset y Timespan** | Sin 
culture | Referencia cultural basada en .NET que se usará cuando se trate de un tipo .NET: `Datetime` o `Datetimeoffset`. El valor predeterminado es `en-us`. | Sin 
formato | Cadena de formato que se usará cuando se trate de un tipo .NET: `Datetime` o `Datetimeoffset`. Consulte [Cadenas con formato de fecha y hora personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) para obtener información sobre el formato de fecha y hora. | Sin 

### <a name="example"></a>Ejemplo
En el ejemplo siguiente, supongamos que los datos del blob de origen presentan el formato CSV y que contienen tres columnas: userid, name y lastlogindate. Son del tipo Int64, cadena y fecha y hora con un formato de fecha y hora personalizado mediante los nombres abreviados de los días de la semana en francés.

Defina la estructura del conjunto de datos de blob como se indica a continuación, junto con las definiciones de tipo para las columnas:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Guía

Las siguientes instrucciones le ayudan a determinar cuándo incluir información de estructura y qué incluir en la sección **structure**. Obtenga más información sobre cómo Data Factory asigna datos de origen al receptor y cuándo se debe especificar la información de estructura en [Esquema y asignación de tipos](copy-activity-schema-and-type-mapping.md).

- **Para orígenes de datos de esquema**, especifique la sección "structure" solo si desea asignar columnas de origen a columnas de receptor y sus nombres no son iguales. Este tipo de origen de datos estructurados almacena información de esquema y tipo de datos junto con los datos propiamente dichos. Ejemplos de orígenes de datos estructurados son SQL Server, Oracle y Azure SQL Database.<br/><br/>Dado que la información de tipo ya está disponible para orígenes de datos estructurados, no debe incluir información de tipo cuando se incluye la sección "structure".
- **Para orígenes de datos de esquema seguros/poco seguros, como, por ejemplo, archivos de texto en Blob Storage**, incluya también "structure" cuando el conjunto de datos sea una entrada para una actividad de copia y los tipos de datos del conjunto de datos de origen se deban convertir a tipos nativos para el receptor. Además, incluya "structure" cuando desee asignar columnas de origen a columnas de receptor.

## <a name="create-datasets"></a>Creación de conjuntos de datos
Puede crear conjuntos de datos mediante una de estas herramientas o SDK: [API de .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API de REST](quickstart-create-data-factory-rest-api.md), plantilla de Azure Resource Manager y Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Conjuntos de datos de la versión actual frente a los de la versión 1

A continuación se indican algunas diferencias entre los conjuntos de datos de Data Factory frente a los de Data Factory versión 1:

- La propiedad externa no se admite en la versión actual. Se sustituye por un [desencadenador](concepts-pipeline-execution-triggers.md).
- Las propiedades de directiva y disponibilidad no se admiten en la versión actual. La hora de inicio de una canalización depende de [desencadenadores](concepts-pipeline-execution-triggers.md).
- Los conjuntos de datos con ámbito (conjuntos de datos definidos en una canalización) no se admiten en la versión actual.

## <a name="next-steps"></a>Pasos siguientes
Consulte el siguiente tutorial para obtener instrucciones paso a paso sobre cómo crear canalizaciones y conjuntos de datos con una de estas herramientas o SDK.

- [Inicio rápido: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Crear una factoría de datos mediante .NET)
- [Inicio rápido: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Crear una factoría de datos mediante PowerShell)
- [Inicio rápido: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Crear una factoría de datos mediante la API de REST)
- Quickstart: create a data factory using Azure portal (Crear una factoría de datos mediante Azure Portal)
