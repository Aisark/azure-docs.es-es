---
title: Tolerancia a errores de la actividad de copia en Azure Data Factory
description: Obtenga información sobre cómo agregar una tolerancia a errores para la actividad de copia en Azure Data Factory omitiendo las filas incompatibles.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: a60cafd529db1c6726a15db2c442af8d097411cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678153"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerancia a errores de la actividad de copia en Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versión actual](copy-activity-fault-tolerance.md)

La actividad de copia de Azure Data Factory ofrece dos maneras de tratar filas incompatibles cuando se copian datos entre almacenes de datos de origen y de receptor:

- Puede anular y omitir la actividad de copia cuando se encuentren datos incompatibles (comportamiento predeterminado).
- Se puede continuar copiando todos los datos a través de la incorporación de tolerancia a errores y la omisión de filas de datos incompatibles. Además, puede registrar las filas incompatibles en Azure Blob Storage o Azure Data Lake Store. Luego puede examinar el registro para obtener información sobre la causa del error, corregir los datos en el origen de datos y reintentar la actividad de copia.

## <a name="supported-scenarios"></a>Escenarios admitidos
La actividad de copia admite tres escenarios para detectar, omitir y registrar datos incompatibles:

- **Incompatibilidad entre el tipo de datos de origen y el tipo nativo de receptor**. 

    Por ejemplo:  Copie datos desde un archivo CSV en Blob Storage a una base de datos SQL con una definición de esquema que contenga tres columnas de tipo INT. Las filas del archivo CSV que contienen datos numéricos, como 123 456 789, se copian correctamente en el almacén de receptor. Pero las filas que contienen valores no numéricos, como 123 456 abc, se detectan como incompatibles y se omiten.

- **Error de coincidencia en el número de columnas entre el origen y el receptor**.

    Por ejemplo:  Copie datos desde un archivo CSV en Blob Storage a una base de datos SQL con una definición de esquema que contenga seis columnas. Las filas del archivo CSV que contiene seis columnas se copian correctamente en el almacén de receptor. Las filas del archivo CSV que contienen más o menos de seis columnas se detectan como incompatibles y se omiten.

- **Infracción de clave principal al escribir en SQL Server, Azure SQL Database o Azure Cosmos DB**.

    Por ejemplo:  Copie datos desde un servidor SQL a una base de datos SQL. Se define una clave principal en la base de datos SQL de receptor, pero no se define en el servidor SQL de origen. Las filas duplicadas que existen en el origen no se pueden copiar en el receptor. La actividad de copia solo copia la primera fila de los datos de origen en el receptor. Las filas de origen subsiguientes que contienen el valor de clave principal duplicado se detectan como incompatibles y se omiten.

>[!NOTE]
>- Para cargar datos en SQL Data Warehouse mediante PolyBase, defina la configuración de tolerancia a errores nativa de PolyBase especificando directivas de rechazo a través de "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" en la actividad de copia. Aún puede habilitar la redirección de filas incompatibles de PolyBase a Blob o ADLS de la forma habitual, como se muestra a continuación.
>- Esta característica no se aplica cuando la actividad de copia está configurada para invoca el mecanismo [Unload de Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esta característica no se aplica cuando la actividad de copia se configura para invocar un [procedimiento almacenado desde un receptor de SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Configuración
En el ejemplo siguiente se proporciona una definición JSON para configurar la omisión de las filas incompatibles en la actividad de copia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica si se deben omitir las filas incompatibles durante la copia o no. | True<br/>False (valor predeterminado) | Sin
redirectIncompatibleRowSettings | Un grupo de propiedades que puede especificarse cuando quiere registrar las filas incompatibles. | &nbsp; | Sin
linkedServiceName | Servicio vinculado de [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para almacenar el registro que contiene las filas que se omiten. | Nombre de un servicio vinculado de tipo `AzureStorage` o `AzureDataLakeStore`, que hace referencia a la instancia que quiere usar para almacenar el archivo de registro. | Sin
path | La ruta de acceso del archivo de registro que contiene las filas que se omiten. | Especifique la ruta de acceso que quiere usar para registrar los datos incompatibles. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para usted. | Sin

## <a name="monitor-skipped-rows"></a>Supervisar filas omitidas
Una vez finalizada la ejecución de la actividad de copia, puede ver el número de filas omitidas en la salida de la actividad de copia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Si configura el registro de las filas incompatibles, encontrará el archivo de registro en esta ruta de acceso: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Los archivos de registro solo pueden ser archivos csv. Los datos originales que se omitan se registrarán con una coma como delimitador de columna, si es necesario. Se agregan dos columnas más "ErrorCode" y "ErrorMessage" a los datos de origen originales en el archivo de registro, en las que podrá ver la causa principal de la incompatibilidad. El nombre de estas dos columnas aparece entre comillas dobles. 

A continuación se muestra un ejemplo del contenido del archivo de registro:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los otros artículos de la actividad de copia:

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Rendimiento de la actividad de copia](copy-activity-performance.md)


