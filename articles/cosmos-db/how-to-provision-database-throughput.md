---
title: Aprovisionamiento del rendimiento de base de datos en Azure Cosmos DB
description: Aprenda a aprovisionar el rendimiento en el nivel de base de datos en Azure Cosmos DB mediante Azure Portal, la CLI, PowerShell y otros SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 8b64d933057b3ddb07f5f99889c7d7c23c74f545
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873665"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Aprovisionamiento del rendimiento en una base de datos de Azure Cosmos DB

En este artículo se explica cómo aprovisionar el rendimiento en una base de datos de Azure Cosmos DB. Puede aprovisionar el rendimiento de un único [contenedor](how-to-provision-container-throughput.md), o bien de una base de datos y compartir el rendimiento entre los contenedores que se incluyen en ella. Para saber cuándo se debe usar el rendimiento de nivel de contenedor y de nivel de base de datos, consulte el artículo [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md). Para aprovisionar el rendimiento en el nivel de base de datos, se puede usar Azure Portal o los SDK de Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionamiento del rendimiento mediante Azure Portal

### <a id="portal-sql"></a>SQL API (Core)

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) o seleccione una ya existente.

1. Abra el panel de **Data Explorer** y seleccione **Nueva base de datos**. Especifique los detalles siguientes:

   * Escriba un identificador de base de datos.
   * Seleccione **Aprovisionar rendimiento**.
   * Escriba un rendimiento, por ejemplo 1000 RU.
   * Seleccione **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo Nueva base de datos](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Aprovisionamiento de la capacidad de procesamiento mediante la CLI de Azure o PowerShell

Para crear una base de datos con capacidad de procesamiento compartida, consulte:

* [Creación de una base de datos mediante la CLI de Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Creación de una base de datos mediante PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Aprovisionamiento del rendimiento mediante el SDK para .NET

> [!Note]
> Puede usar los SDK de Cosmos para SQL API para aprovisionar el rendimiento de todas las API. También puede usar el ejemplo siguiente para Cassandra API.

### <a id="dotnet-all"></a>Todas las API

### <a name="net-v2-sdk"></a>SDK para .NET V2

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>SDK para .NET V3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API
Se pueden ejecutar comandos similares a través de cualquier controlador compatible con CQL. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para obtener más información sobre el rendimiento aprovisionado en Azure Cosmos DB:

* [Escalado global del rendimiento aprovisionado](scaling-throughput.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md)
* [How to provision throughput for a container](how-to-provision-container-throughput.md) (Aprovisionamiento del rendimiento de un contenedor)
* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)
