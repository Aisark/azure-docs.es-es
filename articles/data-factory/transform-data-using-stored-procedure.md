---
title: Transformación de datos mediante la actividad de procedimiento almacenado en Azure Data Factory | Microsoft Docs
description: Explica cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una base de datos o una base de datos de almacenamiento de datos de Azure SQL desde una canalización de Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 806654b7586895b62b014a49b8b3a00fb18f008f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764414"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformación de datos mediante la actividad de procedimiento almacenado de SQL Server en Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-stored-proc-activity.md)
> * [Versión actual](transform-data-using-stored-procedure.md)

Las actividades de transformación en una [canalización](concepts-pipelines-activities.md) de Data Factory transforman y procesan sus datos sin procesar para convertirlos en predicciones y perspectivas. La actividad de procedimiento almacenado es una de las actividades de transformación que admite Data Factory. Este artículo se basa en el artículo sobre [transformación de datos](transform-data.md), que presenta información general de la transformación de datos y las actividades de transformación admitidas en Data Factory.

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, vea [Introducción a Azure Data Factory](introduction.md) y siga el tutorial sobre la [Transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

Puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos de la empresa o en una máquina virtual (VM) de Azure: 

- Azure SQL Database
- Azure SQL Data Warehouse
- Base de datos de SQL Server.  Si se usa SQL Server, se debe instalar el entorno de ejecución de integración autohospedado en el mismo equipo que hospeda la base de datos o en un equipo independiente que tenga acceso a la base de datos. El entorno de ejecución de integración autohospedado es un componente que conecta orígenes de datos locales o en la máquina virtual de Azure con servicios en la nube de forma segura y administrada. Consulte el artículo sobre el [entorno de ejecución de integración autohospedado](create-self-hosted-integration-runtime.md) para más información.

> [!IMPORTANT]
> Al copiar datos en Azure SQL Database o SQL Server, se puede configurar **SqlSink** en la actividad de copia para invocar un procedimiento almacenado mediante la propiedad **sqlWriterStoredProcedureName**. Para más información sobre la propiedad, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). No se admite la invocación de un procedimiento almacenado al copiar datos en Azure SQL Data Warehouse mediante una actividad de copia. Sin embargo, puede usar la actividad de procedimiento almacenado para invocar un procedimiento almacenado en un SQL Data Warehouse. 
>
> Al copiar datos de Azure SQL Database, SQL Server o Azure SQL Data Warehouse, se puede configurar **SqlSource** en la actividad de copia para invocar un procedimiento almacenado de lectura de datos mediante la propiedad **sqlReaderStoredProcedureName**. Para más información, consulte los artículos sobre los conectores siguientes: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalles de la sintaxis
Este es el formato JSON para definir una actividad de procedimiento almacenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

En la tabla siguiente se describen estas propiedades JSON:

| Propiedad                  | DESCRIPCIÓN                              | Obligatorio |
| ------------------------- | ---------------------------------------- | -------- |
| Nombre                      | Nombre de la actividad                     | Sí      |
| description               | Texto que describe para qué se usa la actividad. | Sin       |
| Tipo                      | Para la actividad de procedimiento almacenado, el tipo de actividad es **SqlServerStoredProcedure** | Sí      |
| linkedServiceName         | Referencia a **Azure SQL Database**, **Azure SQL Data Warehouse** o **SQL Server** registrada como un servicio vinculado en Data Factory. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| storedProcedureName       | Especifique el nombre del procedimiento almacenado que se invocará. | Sí      |
| storedProcedureParameters | Especifique los valores para los parámetros del procedimiento almacenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para pasar valores de parámetros y su tipo compatible con el origen de datos. Si necesita pasar NULL para un parámetro, use `"param1": { "value": null }` (todo en minúsculas). | Sin       |

## <a name="error-info"></a>Información de error

Cuando un procedimiento almacenado produce un error y devuelve detalles del error, no se puede capturar la información del error directamente en la salida de la actividad. Sin embargo, Data Factory envía todos sus eventos de ejecución de actividad a Azure Monitor. Entre los eventos que Data Factory envía a Azure Monitor, se encuentran los detalles del error. Por ejemplo, puede configurar alertas de correo electrónico de esos eventos. Para más información, consulte [Alerta y supervisión de factorías de datos mediante Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
