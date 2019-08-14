---
title: Copia de datos a Azure Data Lake Storage Gen2 o desde él mediante Data Factory | Microsoft Docs
description: Aprenda a copiar datos hacia y desde Azure Data Lake Storage Gen2 mediante Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a0a7a413d6c3344ccf5c3f7e4d14dd3d82715034
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840311"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copia de datos con Azure Data Lake Storage Gen2 como origen o destino mediante Azure Data Factory

Azure Data Lake Storage Gen2 es un conjunto de funcionalidades dedicadas al análisis de macrodatos integrado en [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Puede usarlo para establecer una conexión con los datos usando el paradigma de sistema de archivos o el de almacenamiento de objetos.

En este artículo se explica cómo copiar datos con Azure Data Lake Storage Gen2 como origen o destino. Para información sobre Azure Data Factory, lea el [artículo de introducción](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Azure Data Lake Storage Gen2 es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)

En concreto, con este conector, puede hacer lo siguiente:

- Copiar los datos mediante la clave de cuenta, la entidad de servicio o identidades administradas para las autenticaciones de recursos de Azure.
- Copiar los archivos tal cual, o bien analizarlos o generarlos con los [códecs de compresión y los formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Si habilita el espacio de nombres jerárquico, actualmente no hay ninguna interoperabilidad de operaciones entre las API de Blob y Data Lake Storage Gen2. En caso de que se produzca el error "ErrorCode=FilesystemNotFound" con el mensaje "El sistema de archivos especificado no existe", puede que se deba a que el sistema de archivos del receptor especificado se creó en otra parte mediante la API de Blob en lugar de la API de Data Lake Storage Gen2. Para solucionar el problema, especifique un nuevo sistema de archivos con un nombre que no exista como nombre de un contenedor de blobs. Posteriormente, Data Factory creará automáticamente ese sistema de archivos durante la copia de datos.

>[!NOTE]
>Si habilita la opción **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** en la configuración del firewall de Azure Storage, Azure Integration Runtime no se conectará a Data Lake Storage Gen2 y aparecerá un error de prohibición. El mensaje de error aparece porque no se considera a Data Factory como un servicio de Microsoft de confianza. En su lugar, use el entorno de ejecución de integración autohospedado para conectarse.

## <a name="get-started"></a>Primeros pasos

>[!TIP]
>Para ver un tutorial sobre cómo usar el conector de Data Lake Storage Gen2, consulte [Carga de datos en Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporciona información acerca de las propiedades que se usan para definir entidades de Data Factory específicas para Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

El conector de Azure Data Lake Storage Gen2 admite los siguientes tipos de autenticación. Consulte las secciones correspondientes para más información:

- [Autenticación de clave de cuenta](#account-key-authentication)
- [Autenticación de entidad de servicio](#service-principal-authentication)
- [Identidades administradas para la autenticación de recursos de Azure](#managed-identity)

>[!NOTE]
>Cuando usa PolyBase para cargar datos en SQL Data Warehouse, si la instancia de origen de Data Lake Storage Gen2 está configurada con un punto de conexión de Virtual Network, deberá usar la autenticación de identidad administrada como requiere PolyBase. Consulte la sección sobre [autenticación de identidad administrada](#managed-identity) que incluye más requisitos previos de configuración.

### <a name="account-key-authentication"></a>Autenticación de clave de cuenta

Para usar la autenticación de clave de cuenta de almacenamiento, se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureBlobFS**. |Sí |
| url | Punto de conexión de Data Lake Storage Gen2 con el patrón de `https://<accountname>.dfs.core.windows.net`. | Sí |
| accountKey | Clave de cuenta para Data Lake Storage Gen2. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). |Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si el almacén de datos está en una red privada, se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado. Si no se especifica esta propiedad, se usa el valor predeterminado de Azure Integration Runtime. |Sin |

**Ejemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticación de entidad de servicio

Antes de usar la autenticación de entidad de servicio, siga estos pasos.

1. Registre una entidad de aplicación en Azure Active Directory (Azure AD) como se indica en [Registro de la aplicación con un inquilino de Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote los siguientes valores; los usará para definir el servicio vinculado:

    - Identificador de aplicación
    - Clave de la aplicación
    - Id. de inquilino

2. Conceda a la entidad de servicio el permiso adecuado. Para más información sobre cómo funciona el permiso en Data Lake Storage Gen2 consulte [Listas de control de acceso en archivos y directorios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como origen**: En el Explorador de Storage, conceda al menos permiso de **ejecución** a partir del sistema de archivos de origen, junto con el permiso de **lectura** para los archivos que se van a copiar. Como alternativa, en el control de acceso (IAM), conceda al menos el rol **Lector de datos de Storage Blob**.
    - **Como receptor**: En el Explorador de Storage, conceda al menos permiso de **ejecución** a partir del sistema de archivos de destino, junto con el permiso de **escritura** para la carpeta de destino. Como alternativa, en el control de acceso (IAM), conceda al menos el rol **Colaborador de datos de Storage Blob**.

>[!NOTE]
>Para enumerar las carpetas a partir del nivel de cuenta o para probar la conexión, tiene que establecer el permiso de la entidad de servicio que se va a conceder en **cuenta de almacenamiento con permiso de "Lector de datos de Storage Blob" en IAM**. Esto es válido cuando se usa la:
>- **Herramienta Copiar datos** para crear la canalización de la copia.
>- **Interfaz de usuario de Data Factory** para probar la conexión y el desplazamiento por las carpetas durante la creación. 
>Si le preocupa la concesión de permisos en el nivel de cuenta, durante la creación, omita la conexión de prueba y, a continuación, introduzca una ruta de acceso principal con el permiso concedido y, después, examine desde esa ruta de acceso específica. La actividad de copia funciona en tanto se conceda a la entidad de servicio el permiso adecuado para los archivos que se van a copiar.

Estas propiedades son compatibles con el servicio vinculado:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureBlobFS**. |Sí |
| url | Punto de conexión de Data Lake Storage Gen2 con el patrón de `https://<accountname>.dfs.core.windows.net`. | Sí |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí |
| servicePrincipalKey | Especifique la clave de la aplicación. Marque este campo como `SecureString` para almacenarlo de forma segura en Data Factory. O bien, puede [hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| tenant | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si el almacén de datos está en una red privada, se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado. Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. |Sin |

**Ejemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades administradas para la autenticación de recursos de Azure

Una factoría de datos se puede asociar con una [identidad administrada para recursos de Azure](data-factory-service-identity.md), que representa esa factoría de datos concreta. Puede usar directamente esta identidad administrada para la autenticación en Data Lake Storage Gen2, de forma similar a como usa su propia entidad de servicio. Permite que esta fábrica designada acceda a datos y los copie en Data Lake Storage Gen2 o desde este.

Para usar identidades administradas para la autenticación de recursos de Azure, siga estos pasos.

1. [Recupere la información de la identidad administrada de Data Factory](data-factory-service-identity.md#retrieve-managed-identity) mediante la copia del valor de **Id. de la aplicación de identidad de servicio** que se ha generado junto con la factoría.

2. Conceda el permiso adecuado de identidad administrada. Para más información sobre cómo funciona el permiso en Data Lake Storage Gen2 consulte [Listas de control de acceso en archivos y directorios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como origen**: En el Explorador de Storage, conceda al menos permiso de **ejecución** a partir del sistema de archivos de origen, junto con el permiso de **lectura** para los archivos que se van a copiar. Como alternativa, en el control de acceso (IAM), conceda al menos el rol **Lector de datos de Storage Blob**.
    - **Como receptor**: En el Explorador de Storage, conceda al menos permiso de **ejecución** a partir del sistema de archivos de destino, junto con el permiso de **escritura** para la carpeta de destino. Como alternativa, en el control de acceso (IAM), conceda al menos el rol **Colaborador de datos de Storage Blob**.

>[!NOTE]
>Para enumerar las carpetas a partir del nivel de cuenta o para probar la conexión, tiene que establecer el permiso de la identidad administrada que se va a conceder en **cuenta de almacenamiento con permiso de "Lector de datos de Storage Blob" en IAM**. Esto es válido cuando se usa la:
>- **Herramienta Copiar datos** para crear la canalización de la copia.
>- **Interfaz de usuario de Data Factory** para probar la conexión y el desplazamiento por las carpetas durante la creación. 
>Si le preocupa la concesión de permisos en el nivel de cuenta, durante la creación, omita la conexión de prueba y, a continuación, introduzca una ruta de acceso principal con el permiso concedido y, después, examine desde esa ruta de acceso específica. La actividad de copia funciona en tanto se conceda a la entidad de servicio el permiso adecuado para los archivos que se van a copiar.

>[!IMPORTANT]
>Si usa PolyBase para cargar datos desde Data Lake Storage Gen2 en SQL Data Warehouse, al utilizar la autenticación de identidad administrada de Data Lake Storage Gen2 asegúrese de que también sigue los pasos 1 y 2 de [esta guía](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) para 1) registrar el servidor de SQL Database con Azure Active Directory (Azure AD) y 2) asignar el rol Colaborador de datos de Storage Blob a su servidor de SQL Database. Lo demás lo controlará Data Factory. Si la instancia de Data Lake Storage Gen2 está configurada con un punto de conexión de Azure Virtual Network, para usar PolyBase para cargar datos desde este deberá usar la autenticación de identidad administrada como requiere PolyBase.

Estas propiedades son compatibles con el servicio vinculado:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureBlobFS**. |Sí |
| url | Punto de conexión de Data Lake Storage Gen2 con el patrón de `https://<accountname>.dfs.core.windows.net`. | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si el almacén de datos está en una red privada, se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado. Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. |Sin |

**Ejemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos](concepts-datasets-linked-services.md).

- Para el **formato binario, de texto delimitado o Parquet**, consulte la sección [Conjunto de datos con formato binario, de texto delimitado o Parquet](#format-based-dataset).
- Para otros formatos como **ORC, Avro o JSON**, consulte la sección [Otro conjunto de datos de formato](#other-format-dataset).

### <a name="format-based-dataset"></a> Conjunto de datos con formato binario, de texto delimitado o Parquet

Para copiar datos como origen y destino de **formato binario, de texto delimitado o Parquet**, consulte [Formato Parquet](format-parquet.md), [Formato de texto delimitado](format-delimited-text.md) y [Formato binario](format-binary.md) sobre conjuntos de datos basados en formato y las configuraciones admitidas. Las propiedades siguientes se admiten para Data Lake Store Gen2 en la configuración `location` del conjunto de datos basado en formato:

| Propiedad   | DESCRIPCIÓN                                                  | Obligatorio |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propiedad type de `location` del conjunto de datos debe establecerse en **AzureBlobFSLocation**. | Sí      |
| fileSystem | Nombre del sistema de archivos de Data Lake Storage Gen2.                              | Sin       |
| folderPath | Ruta de acceso a una carpeta en el sistema de archivos especificado. Si quiere usar un carácter comodín para filtrar carpetas, omita este valor y especifíquelo en la configuración del origen de actividad. | Sin       |
| fileName   | Nombre de archivo en la propiedad fileSystem + folderPath indicada. Si quiere usar un carácter comodín para filtrar archivos, omita este valor y especifíquelo en la configuración del origen de actividad. | Sin       |

> [!NOTE]
> El conjunto de datos de tipo **AzureBlobFSFile** con formato Parquet o texto que se menciona en la sección siguiente todavía se admite tal cual para la actividad copy, lookup o GetMetadata para compatibilidad con versiones anteriores. Sin embargo, no funciona con la característica Asignación de Data Flow. Se recomienda usar este nuevo modelo a partir de ahora. La interfaz de usuario de creación de Data Factory genera estos nuevos tipos.

**Ejemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Otro conjunto de datos de formato

Para copiar datos con origen y destino en Data Lake Storage Gen2 en **formato ORC, Avro o JSON**, se admiten las propiedades siguientes:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **AzureBlobFSFile**. |Sí |
| folderPath | Ruta de acceso a la carpeta de Data Lake Storage Gen2. Si no se especifica, apunta a la raíz. <br/><br/>Se admiten filtros con caracteres comodín. Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre real de la carpeta contiene un carácter comodín o este carácter de escape. <br/><br/>Ejemplos: filesystem/folder/. Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). |Sin |
| fileName | Filtro de nombre o de comodín para los archivos de la ruta "folderPath" especificada. Si no especifica ningún valor para esta propiedad, el conjunto de datos apunta a todos los archivos de la carpeta. <br/><br/>Para filtrar, los caracteres comodín permitidos son `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter).<br/>- Ejemplo 1: `"fileName": "*.csv"`<br/>- Ejemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape si el nombre de archivo real contiene un comodín o este carácter de escape.<br/><br/>Cuando fileName no se especifica para un conjunto de datos de salida y **preserveHierarchy** no se determina en el receptor de la actividad, la actividad de copia generará automáticamente el nombre de archivo con el siguiente patrón: "*Data.[GUID de Id. de ejecución de actividad].[GUID si FlattenHierarchy].[formato si se ha configurado].[compresión si se ha configurado]* ", por ejemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Si realiza una copia desde el origen tabular mediante el nombre de una tabla en lugar de una consulta, el patrón del nombre es " *[nombre de tabla].[formato].[compresión, si está configurada]* ", por ejemplo, "MyTable.csv". |Sin |
| modifiedDatetimeStart | Filtro de archivos basado en el atributo Last Modified. Los archivos se seleccionan si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> El rendimiento general del movimiento de datos se ve afectado si habilita esta configuración cuando quiere filtrar archivos con grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos. Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora. Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora.| Sin |
| modifiedDatetimeEnd | Filtro de archivos basado en el atributo Last Modified. Los archivos se seleccionan si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> El rendimiento general del movimiento de datos se ve afectado si habilita esta configuración cuando quiere filtrar archivos con grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos. Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora. Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora.| Sin |
| format | Si desea copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida.<br/><br/>Si quiere analizar o generar archivos con un formato concreto, se admiten los siguientes tipos de formato de archivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** en **format** en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs.md#orc-format) y [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo para el escenario de copia binaria) |
| compression | Especifique el tipo y el nivel de compresión de los datos. Para más información, consulte el artículo sobre [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**.<br/>Niveles admitidos son **Optimal** y **Fastest**. |Sin |

>[!TIP]
>Para copiar todos los archivos en una carpeta, especifique solo **folderPath**.<br>Para copiar un único archivo con un nombre determinado, especifique **folderPath** con un elemento de carpeta y **fileName** con un nombre de archivo.<br>Para copiar un subconjunto de archivos en una carpeta, especifique **folderPath** con un elemento de carpeta y **fileName** con un filtro de comodín. 

**Ejemplo:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte los artículos sobre [configuraciones de actividades de copia](copy-activity-overview.md#configuration) y [canalizaciones y actividades](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que el receptor y el origen de Data Lake Storage Gen2 admiten.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como tipo de origen

- Para copiar desde el **formato binario, de texto delimitado o Parquet**, consulte la sección [Origen de formato binario, de texto delimitado o Parquet](#format-based-source).
- Para copiar desde otros formatos como **ORC, Avro o JSON**, consulte la sección [Otro origen de formato](#other-format-source).

#### <a name="format-based-source"></a> Origen de formato binario, de texto delimitado o Parquet

Para copiar datos en **formato de texto binario, de texto delimitado o Parquet**, consulte [Formato Parquet](format-parquet.md), [Formato de texto delimitado](format-delimited-text.md) y [Formato binario](format-binary.md) sobre el origen de la actividad de copia basado en formato y las configuraciones admitidas. Las propiedades siguientes se admiten para Data Lake Store Gen2 en la configuración `storeSettings` en el origen de copia basado en formato:

| Propiedad                 | DESCRIPCIÓN                                                  | Obligatorio                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propiedad type de `storeSettings` se debe establecer en **AzureBlobFSReadSetting**. | Sí                                           |
| recursive                | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Cuando recursive se establece en true y el receptor es un almacén basado en archivos, no se crea una carpeta o una subcarpeta vacía en el receptor. Los valores permitidos son: **True** (valor predeterminado) y **False**. | Sin                                            |
| wildcardFolderPath       | Ruta de acceso de carpeta con caracteres comodín en el sistema de archivos especificado configurado en el conjunto de datos para filtrar las carpetas de origen. <br>Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre real de la carpeta contiene un carácter comodín o este carácter de escape. <br>Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | Sin                                            |
| wildcardFileName         | Nombre de archivo con caracteres comodín en la propiedad file system + folderPath o wildcardFolderPath indicada para filtrar los archivos de origen. <br>Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre real de la carpeta contiene un carácter comodín o este carácter de escape. Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | Sí, si no se ha especificado `fileName` en el conjunto de datos |
| modifiedDatetimeStart    | Filtro de archivos basado en el atributo Last Modified. Los archivos se seleccionan si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br> Las propiedades pueden ser NULL, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos. Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora. Cuando `modifiedDatetimeEnd` tiene el valor de fecha y hora, pero `modifiedDatetimeStart` es NULL, significa que se seleccionarán los archivos cuyo último atributo modificado sea inferior al valor de fecha y hora. | Sin                                            |
| modifiedDatetimeEnd      | Igual que el anterior.                                               | Sin                                            |
| maxConcurrentConnections | Número de conexiones para conectarse al almacén de almacenamiento de forma simultánea. Solo se especifica cuando se quiere limitar la conexión simultánea al almacén de datos. | Sin                                            |

> [!NOTE]
> Para el formato de texto delimitado o Parquet, todavía se admite tal cual el origen de actividad de copia de tipo **AzureBlobFSSource** mencionado en la sección siguiente para la compatibilidad con versiones anteriores. Se recomienda usar este nuevo modelo a partir de ahora. La interfaz de usuario de creación de Data Factory genera estos nuevos tipos.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Otro origen de formato

Para copiar datos de Data Lake Storage Gen2 en **formato ORC, Avro o JSON**, se admiten las propiedades siguientes en la sección **source** (origen) de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **AzureBlobFSSource**. |Sí |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Cuando recursive se establece en true y el receptor es un almacén basado en archivos, no se crea una carpeta o una subcarpeta vacía en el receptor.<br/>Los valores permitidos son: **True** (valor predeterminado) y **False**. | Sin |
| maxConcurrentConnections | Número de conexiones para conectarse al almacén de datos de forma simultánea. Solo se especifica cuando se quiere limitar la conexión simultánea al almacén de datos. | Sin |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 como tipo de receptor

- Para copiar en el **formato binario, de texto delimitado o Parquet**, consulte la sección [Receptor de formato binario, de texto delimitado o Parquet](#format-based-sink).
- Para copiar en otros formatos como **ORC, Avro o JSON**, consulte la sección [Otro receptor de formato](#other-format-sink).

#### <a name="format-based-sink"></a> Receptor de formato binario, de texto delimitado o Parquet

Para copiar datos en el **formato binario, de texto delimitado o Parquet**, consulte [Formato Parquet](format-parquet.md), [Formato de texto delimitado](format-delimited-text.md) y [Formato binario](format-binary.md) sobre el receptor de la actividad de copia basado en formato y las configuraciones admitidas. Las propiedades siguientes se admiten para Data Lake Store Gen2 en la configuración `storeSettings` en el destino de copia basado en formato:

| Propiedad                 | DESCRIPCIÓN                                                  | Obligatorio |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propiedad type de `storeSettings` se debe establecer en **AzureBlobFSWriteSetting**. | Sí      |
| copyBehavior             | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del archivo, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. | Sin       |
| maxConcurrentConnections | Número de conexiones para conectarse al almacén de datos de forma simultánea. Solo se especifica cuando se quiere limitar la conexión simultánea al almacén de datos. | Sin       |

> [!NOTE]
> Para el formato de texto delimitado o Parquet, todavía se admite tal cual el destino de actividad de copia de tipo **AzureBlobFSSink** mencionado en la sección siguiente para la compatibilidad con versiones anteriores. Se recomienda usar este nuevo modelo a partir de ahora. La interfaz de usuario de creación de Data Factory genera estos nuevos tipos.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Otro receptor de formato

Para copiar datos en Data Lake Storage Gen2 en **formato ORC, Avro o JSON**, se admiten las propiedades siguientes en la sección **sink** (receptor):

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del receptor de la actividad de copia debe establecerse en **AzureBlobFSSink**. |Sí |
| copyBehavior | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del archivo, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. | Sin |
| maxConcurrentConnections | Número de conexiones para conectarse al almacén de datos de forma simultánea. Solo se especifica cuando se quiere limitar la conexión simultánea al almacén de datos. | Sin |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Ejemplos de filtros de carpetas y archivos

Esta sección describe el comportamiento resultante de la ruta de acceso de la carpeta y el nombre de archivo con los filtros de carácter comodín.

| folderPath | fileName | recursive | Resultado de estructura de carpeta de origen y filtro (se recuperan los archivos en **negrita**)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vacío, usar el valor predeterminado) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Vacío, usar el valor predeterminado) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Algunos ejemplos de recursive y copyBehavior

En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de valores recursive y copyBehavior.

| recursive | copyBehavior | Estructura de carpetas de origen | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | El destino Folder1 se crea con la misma estructura que el origen:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 + File3 + File4 + File5 se combina en un archivo con un nombre de archivo generado automáticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5 | La carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 se combina en un archivo con un nombre de archivo generado automáticamente. nombre de archivo generado automáticamente para File1<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Conservación de ACL desde Data Lake Storage Gen1

>[!TIP]
>Para copiar datos desde Azure Data Lake Storage Gen1 en Gen2 en general, consulte [Copia de datos de Azure Data Lake Storage Gen1 en Gen2 con Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para ver un tutorial y los procedimientos recomendados.

Al copiar archivos de Azure Data Lake Storage Gen1 a Gen2, puede elegir conservar las listas de control de acceso (ACL) de POSIX junto con los datos. Para más información sobre el control de acceso, consulte [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) y [Control de acceso en Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Se conservan los siguientes tipos de ACL mediante el uso de la actividad de copia de Azure Data Factory. Puede seleccionar uno o varios tipos:

- **ACL**: Copie y conserve las listas de control de acceso de POSIX en archivos y directorios. Esto copia las ACL existentes completas del origen al destino. 
- **Propietario**: Copie y conserve el usuario propietario de archivos y directorios. Se requiere acceso de superusuario en Data Lake Storage Gen2 de destino.
- **Grupo**: Copie y conserve el grupo propietario de archivos y directorios. Se requiere acceso de superusuario para Data Lake Storage Gen2 de destino o el usuario propietario (si el usuario propietario también es miembro del grupo de destino).

Si especifica que desea copiar desde una carpeta, Data Factory replicará las ACL de esa carpeta concreta y los archivos y directorios incluidos en ella si `recursive` se establece en true. Si especifica que desea copiar desde un único archivo, se copiarán las ACL de ese archivo.

>[!IMPORTANT]
>Si decide conservar las ACL, asegúrese de que concede permisos lo suficientemente altos para que Data Factory funcione en la cuenta de destino de Data Lake Storage Gen2. Por ejemplo, utilice la autenticación de clave de cuenta o asigne el rol Propietario de datos de Blob Storage a la entidad de servicio o identidad administrada.

Si configura el origen como Data Lake Storage Gen1 con la opción de copia binaria o formato binario, y el destino como Data Lake Storage Gen2 con las mismas opciones, puede encontrar la opción **Conservar** en la página de **configuración de la herramienta Copiar datos** o en la pestaña **Copiar actividad** > **Configuración**.

![Opción Conservar ACL de Data Lake Storage Gen1 a Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Este es un ejemplo de configuración de JSON (consulte `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propiedades de Asignación de instancias de Data Flow

Obtenga información detallada de la [transformación de origen](data-flow-source.md) y la [transformación de receptor](data-flow-sink.md) en la característica Asignación de Data Flow.

## <a name="next-steps"></a>Pasos siguientes

Para ver la lista de almacenes de datos que la actividad de copia de Data Factory admite como orígenes y receptores consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md##supported-data-stores-and-formats).
