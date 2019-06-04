---
title: Aprenda a administrar la coherencia en Azure Cosmos DB
description: Aprenda a administrar la coherencia en Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 68a1a757b5c5e4ce63d7f12a8502d57942d4ec42
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240925"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Administración de los niveles de coherencia en Azure Cosmos DB

Este artículo explica cómo administrar los niveles de coherencia en Azure Cosmos DB. Aprenderá a configurar el nivel de coherencia predeterminado, invalidar la coherencia predeterminada, administrar manualmente tokens de sesión y comprender la métrica de obsolescencia limitada por probabilidades (PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configuración del nivel de coherencia predeterminado

El [nivel de coherencia predeterminado](consistency-levels.md) es el que los clientes usan de forma predeterminada. Los clientes siempre pueden invalidarlo.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

En este ejemplo se crea una cuenta de Azure Cosmos con varias regiones de escritura habilitadas, en las regiones Este de EE. UU. y Oeste de EE. UU. El nivel de coherencia predeterminado se establece en *Sesión*.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Portal de Azure

Para ver o modificar el nivel de coherencia predeterminado, inicie sesión en Azure Portal. Busque la cuenta de Azure Cosmos y abra el panel **Coherencia predeterminada**. Seleccione el nivel de coherencia que desee como el nuevo valor predeterminado y, a continuación, seleccione **Guardar**. Azure Portal también proporciona una visualización de los diferentes niveles de coherencia con notas musicales. 

![Menú de coherencia en Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Invalidación del nivel de coherencia predeterminado

Los clientes pueden invalidar el nivel de coherencia predeterminado establecido por el servicio. El nivel de coherencia se puede establecer por cada solicitud, lo que invalida el nivel de coherencia predeterminado establecido en el nivel de cuenta.

### <a id="override-default-consistency-dotnet"></a>SDK para .NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>SDK asincrónico para Java

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>SDK sincrónico para Java

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>SDK para Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>SDK para Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Uso de tokens de sesión

Uno de los niveles de coherencia de Azure Cosmos DB es *Sesión*. Este es el nivel predeterminado que se aplica a las cuentas de Cosmos de forma predeterminada. Al trabajar con la coherencia de *Sesión*, el cliente usa un token de sesión internamente con cada solicitud de lectura/consulta para garantizar que se mantiene el nivel de coherencia establecido.

Para administrar los tokens de sesión manualmente, obtenga el token de sesión de la respuesta y establézcalos por cada solicitud. Si no tiene la necesidad de administrar manualmente los tokens de sesión, no es necesario que utilice estos ejemplos. El SDK realiza el seguimiento de los tokens de sesión automáticamente. Si no establece el token de sesión manualmente, el SDK usa el token de sesión más reciente de forma predeterminada.

### <a id="utilize-session-tokens-dotnet"></a>SDK para .NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>SDK asincrónico para Java

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>SDK sincrónico para Java

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>SDK para Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>SDK para Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Supervisión de la métrica de obsolescencia limitada de manera probabilística (PBS)

¿Cómo de eventual es la coherencia eventual? Por término medio, se puede ofrecer obsolescencia limitada con respecto al historial de versiones y la hora. La métrica [**Obsolescencia limitada de manera probabilística (PBS)** ](https://pbs.cs.berkeley.edu/) intenta cuantificar la probabilidad de obsolescencia y la muestra como una métrica. Para ver la métrica de PBS, vaya a la cuenta de Azure Cosmos en Azure Portal. Abra el panel **Métricas** y seleccione la pestaña **Coherencia**. Examine el gráfico llamado **Probabilidad de lecturas con coherencia fuerte según la carga de trabajo (consultar PBS)** .

![Gráfico de PBS en Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Pasos siguientes

Aprenda cómo administrar los conflictos de datos o pase al siguiente concepto principal de Azure Cosmos DB. Consulte los artículos siguientes:

* [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md)
* [Administración de conflictos entre regiones](how-to-manage-conflicts.md)
* [Creación de particiones y distribución de datos](partition-data.md)
* [Consistency Tradeoffs in Modern Distributed Database Systems Design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compromisos de coherencia en el diseño de sistemas modernos de bases de datos distribuidas)
* [Alta disponibilidad](high-availability.md)
* [Acuerdo de Nivel de Servicio de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
