---
title: Creación de contenedores de Azure Cosmos con una clave de partición de gran tamaño
description: Aprenda a crear un contenedor en Azure Cosmos DB con una clave de partición de gran tamaño mediante Azure Portal y distintos SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 42e92fa2b088fc04d4aa670e6586fcfe26ff3a70
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441702"
---
# <a name="create-containers-with-large-partition-key"></a>Creación de contenedores con una clave de partición de gran tamaño

Azure Cosmos DB usa el esquema de partición basado en hash para lograr el escalado horizontal de los datos. Todos los contenedores de Azure Cosmos creados antes del 3 de mayo de 2019 usan una función hash que calcula el hash en función de los primeros 100 bytes de la clave de partición. Si hay varias claves de partición con los mismos 100 primeros bytes, el servicio considera que esas particiones lógicas son la misma partición lógica. Esto puede causar problemas, como que la cuota del tamaño de la partición no sea correcta y que se apliquen índices únicos en las distintas claves de partición. Para solucionar este problema, aparecieron las claves de partición de gran tamaño. Azure Cosmos DB ahora admite claves de partición de gran tamaño con valores hasta 2 KB.

Las claves de partición de gran tamaño se admiten mediante el uso de la funcionalidad de una versión mejorada de la función hash, la que puede generar un hash único a partir de claves de partición de gran tamaño de hasta 2 KB. Esta versión de hash también se recomienda para los escenarios con una cardinalidad de clave de partición alta, independientemente del tamaño de la clave de partición. Una cardinalidad de clave de partición se define como el número de particiones lógicas únicas, por ejemplo en el orden de unas 30 000 particiones lógicas en un contenedor. En este artículo se describe cómo crear un contenedor con una clave de partición de gran tamaño mediante Azure Portal y distintos SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Creación de una clave de partición de gran tamaño (Azure Portal)

Para crear una clave de partición de gran tamaño, cuando crea un contenedor con Azure Portal, active la opción **My partition key is larger than 100-bytes** (Mi clave de partición tiene más de 100 bytes). Desactive la casilla si no necesita claves de partición de gran tamaño o si tiene aplicaciones que ejecutan una versión de SDK anterior a 1.18.

![Creación de claves de partición de gran tamaño con Azure Portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Creación de una clave de partición de gran tamaño (PowerShell)

Para crear un contenedor con soporte de clave de partición grande, consulte,

* [Creación de un contenedor de Azure Cosmos con una clave de partición de gran tamaño](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Creación de una clave de partición de gran tamaño (SDK de .NET)

Para crear un contenedor con una clave de partición de gran tamaño mediante el SDK de .NET, especifique la propiedad `PartitionKeyDefinitionVersion.V2`. En el ejemplo siguiente se muestra cómo especificar la propiedad de versión dentro del objeto PartitionKeyDefinition y establecerla en PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>SDK de .NET v. 3

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>SDK de .NET v. 2

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Versiones de SDK compatibles

Las claves de partición de gran tamaño se admiten con las siguientes versiones mínimas de SDK:

|Tipo de SDK  | Versión mínima   |
|---------|---------|
|.Net     |    1.18     |
|Java Sync     |   2.4.0      |
|Java Async   |  2.5.0        |
| API DE REST | Una versión superior a `2017-05-03` mediante el uso del encabezado de solicitud`x-ms-version`.|
| Plantilla de Resource Manager | versión 2 mediante la propiedad `"version":2` dentro del objeto `partitionKey`. |

Actualmente, no se pueden usar contenedores con una clave de partición de gran tamaño dentro de Power BI ni de Azure Logic Apps. Puede usar contenedores sin una clave de partición de gran tamaño desde estas aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de particiones en Azure Cosmos DB](partitioning-overview.md)
* [Unidades de solicitud en Azure Cosmos DB](request-units.md)
* [Aprovisionamiento del rendimiento en contenedores y bases de datos](set-throughput.md)
* [Uso de la cuenta de Azure Cosmos](account-overview.md)
