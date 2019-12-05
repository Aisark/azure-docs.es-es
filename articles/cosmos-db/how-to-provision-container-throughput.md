---
title: Aprovisionamiento del rendimiento de contenedor en Azure Cosmos DB
description: Aprenda a aprovisionar el rendimiento en el nivel de contenedor en Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 0b48652f7b181f1254a4b20af75b83593c2aba05
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147598"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Aprovisionamiento del rendimiento en un contenedor de Azure Cosmos

En este artículo se explica cómo aprovisionar el rendimiento de un contenedor (colección, grafo, tabla) en Azure Cosmos DB. Puede aprovisionar el rendimiento de un único contenedor o [aprovisionar el de una base de datos](how-to-provision-database-throughput.md) y compartirlo entre los contenedores incluidos en ella. Para aprovisionar el rendimiento de un contenedor, use Azure Portal, la CLI de Azure o los SDK de Azure Cosmos DB.

## <a name="azure-portal"></a>Portal de Azure

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione **Nueva colección**. Después, proporcione los detalles siguientes:

   * Indique si va a crear una nueva base de datos o a usar una existente.
   * Escriba un identificador de contenedor (tabla o grafo).
   * Escriba un valor de la clave de partición (por ejemplo, `/userid`).
   * Escriba un rendimiento que quiera aprovisionar (por ejemplo, 1000 RU).
   * Seleccione **Aceptar**.

    ![Captura de pantalla de Data Explorer, con Nueva colección resaltado](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>CLI de Azure o PowerShell

Para crear un contenedor con capacidad de procesamiento dedicada, consulte:

* [Creación de un contenedor mediante la CLI de Azure](manage-with-cli.md#create-a-container)
* [Creación de un contenedor mediante PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Si va a aprovisionar el rendimiento del contenedor de una cuenta de Azure Cosmos configurada con la API de Azure Cosmos DB para MongoDB, use `/myShardKey` para la ruta de acceso de la clave de partición. Si va a aprovisionar el rendimiento del contenedor de una cuenta de Azure Cosmos configurada con Cassandra API, use `/myPrimaryKey` para la ruta de acceso de la clave de partición.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Use los SDK de Cosmos de la API de SQL para aprovisionar el rendimiento de todas las API de Cosmos DB, excepto Cassandra API.

### <a id="dotnet-most"></a>API de MongoDB y versiones de SQL, Gremlin y Table API
### <a name="net-v2-sdk"></a>SDK para .NET V2

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>SDK de .NET V3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>SDK de JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "contaierId ",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a id="dotnet-cassandra"></a>Cassandra API

Se pueden emitir comandos similares a través de cualquier controlador compatible con CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Modificación o cambio del rendimiento de la tabla de Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender sobre el aprovisionamiento del rendimiento en Azure Cosmos DB:

* [Aprovisionamiento del rendimiento en una base de datos](how-to-provision-database-throughput.md)
* [Rendimiento y unidades de solicitud en Azure Cosmos DB](request-units.md)
