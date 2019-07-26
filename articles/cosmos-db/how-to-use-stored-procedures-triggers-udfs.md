---
title: Llamada a procedimientos almacenados, desencadenadores y funciones definidas por el usuario con los SDK de Azure Cosmos DB
description: Aprenda a registrar y llamar a procedimientos almacenados, desencadenadores y funciones definidas por el usuario con los SDK de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360384"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Registro y uso de procedimientos almacenados, desencadenadores y funciones definidas por el usuario en Azure Cosmos DB

La API de SQL en Azure Cosmos DB admite el registro e invocación de procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) escritas en JavaScript. Puede usar los SDK de [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) o [Python](sql-api-sdk-python.md) de SQL API para registrar e invocar los procedimientos almacenados. Una vez que haya definido uno o varios procedimientos, desencadenadores y funciones definidas por el usuario, puede cargarlos y verlos en [Azure Portal](https://portal.azure.com/) mediante el Explorador de datos.

## <a id="stored-procedures"></a>Ejecución de procedimientos almacenados

Los procedimientos almacenados se escriben con JavaScript. Pueden crear, actualizar, leer, consultar y eliminar elementos dentro de un contenedor de Azure Cosmos. Para más información sobre cómo escribir procedimientos almacenados en Azure Cosmos DB, consulte el artículo sobre la [escritura de procedimientos almacenados en Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

Los ejemplos siguientes muestran cómo registrar y llamar a un procedimiento almacenado mediante los SDK de Azure Cosmos DB. Consulte la sección sobre la [creación de un documento](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) porque el origen para este procedimiento almacenado se guarda como `spCreateToDoItem.js`.

> [!NOTE]
> Para los contenedores con particiones, al ejecutar un procedimiento almacenado, se debe proporcionar un valor de clave de partición en las opciones de solicitud. Los procedimientos almacenados siempre se limitan a una clave de partición. Los elementos que tienen un valor de clave de partición diferente no estarán visibles para el procedimiento almacenado. Esto también se aplica a los desencadenadores.

### <a name="stored-procedures---net-sdk"></a>Procedimientos almacenados: SDK de .NET

El siguiente un ejemplo muestra cómo registrar un procedimiento almacenado con el SDK de .NET:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

El siguiente código muestra cómo llamar a un procedimiento almacenado con el SDK de .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Procedimientos almacenados: SDK de Java

El siguiente es un ejemplo de cómo registrar un procedimiento almacenado con el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

El siguiente código muestra cómo llamar a un procedimiento almacenado con el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedimientos almacenados: SDK de JavaScript

El siguiente es un ejemplo de cómo registrar un procedimiento almacenado con el SDK de JavaScript.

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

El siguiente código muestra cómo llamar a un procedimiento almacenado con el SDK de JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedimientos almacenados: SDK de Python

El siguiente es un ejemplo de cómo registrar un procedimiento almacenado con el SDK de Python

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

El siguiente código muestra cómo llamar a un procedimiento almacenado con el SDK de Python

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Ejecución de desencadenadores previos

Los ejemplos siguientes muestran cómo registrar y llamar a un desencadenador previo mediante los SDK de Azure Cosmos DB. Consulte el [ejemplo de desencadenador previo](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) porque el origen para este desencadenador previo se guarda como `trgPreValidateToDoItemTimestamp.js`.

Cuando se ejecuta, los desencadenadores previos se pasan en el objeto RequestOptions especificando `PreTriggerInclude` y, a continuación, pasando el nombre del desencadenador en un objeto de lista.

> [!NOTE]
> Aunque el nombre del desencadenador se pasa como una lista, todavía puede ejecutar solo un desencadenador por cada operación.

### <a name="pre-triggers---net-sdk"></a>Desencadenadores previos: SDK de .NET

El código siguiente muestra cómo registrar un desencadenador previo mediante el SDK de. NET:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

El código siguiente muestra cómo llamar a un desencadenador previo mediante el SDK de. NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Desencadenadores previos: SDK de Java

El código siguiente muestra cómo registrar un desencadenador previo mediante el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

El código siguiente muestra cómo llamar a un desencadenador previo mediante el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Desencadenadores previos: SDK de JavaScript

El código siguiente muestra cómo registrar un desencadenador previo mediante el SDK de JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

El código siguiente muestra cómo llamar a un desencadenador previo mediante el SDK de JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Desencadenadores previos: SDK de Python

El código siguiente muestra cómo registrar un desencadenador previo mediante el SDK de Python:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

El código siguiente muestra cómo llamar a un desencadenador previo mediante el SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Ejecución de desencadenadores posteriores

Los ejemplos siguientes muestran cómo registrar un desencadenador posterior mediante los SDK de Azure Cosmos DB. Consulte el [ejemplo de desencadenador posterior](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) porque el origen para este desencadenador previo se guarda como `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Desencadenadores posteriores: SDK de .NET

El código siguiente muestra cómo registrar un desencadenador posterior mediante el SDK de. NET:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

El código siguiente muestra cómo llamar a un desencadenador posterior mediante el SDK de. NET:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Desencadenadores posteriores: SDK de Java

El código siguiente muestra cómo registrar un desencadenador posterior mediante el SDK de. Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

El código siguiente muestra cómo llamar a un desencadenador posterior mediante el SDK de. Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Desencadenadores posteriores: SDK de JavaScript

El código siguiente muestra cómo registrar un desencadenador posterior mediante el SDK de. JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

El código siguiente muestra cómo llamar a un desencadenador posterior mediante el SDK de. JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Desencadenadores posteriores: SDK de Python

El código siguiente muestra cómo registrar un desencadenador posterior mediante el SDK de Python:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

El código siguiente muestra cómo llamar a un desencadenador posterior mediante el SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Trabajo con funciones definidas por el usuario

Los ejemplos siguientes muestran cómo registrar una función definida por el usuario mediante los SDK de Azure Cosmos DB. Consulte este [ejemplo de función definida por el usuario](how-to-write-stored-procedures-triggers-udfs.md#udfs) porque el origen para este desencadenador previo se guarda como `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Funciones definidas por el usuario (UDF): SDK de .NET

El código siguiente muestra cómo registrar una función definida por el usuario mediante el SDK de. NET:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

El código siguiente muestra cómo llamar a una función definida por el usuario mediante el SDK de. NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Funciones definidas por el usuario : SDK de Java

El código siguiente muestra cómo registrar una función definida por el usuario mediante el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

El código siguiente muestra cómo llamar a una función definida por el usuario mediante el SDK de Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funciones definidas por el usuario: SDK de JavaScript

El código siguiente muestra cómo registrar una función definida por el usuario mediante el SDK de JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

El código siguiente muestra cómo llamar a una función definida por el usuario mediante el SDK de JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funciones definidas por el usuario: SDK de Python

El código siguiente muestra cómo registrar una función definida por el usuario mediante el SDK de Python:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

El código siguiente muestra cómo llamar a una función definida por el usuario mediante el SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda más conceptos y cómo escribir o utilizar procedimientos almacenados, desencadenadores y funciones definidas por el usuario en Azure Cosmos DB:

- [Working with Azure Cosmos DB stored procedures, triggers, and user-defined functions in Azure Cosmos DB](stored-procedures-triggers-udfs.md) (Trabajo con procedimientos almacenados, desencadenadores y funciones definidas por el usuario de Azure Cosmos DB en Azure Cosmos DB)
- [Working with JavaScript language integrated query API in Azure Cosmos DB](javascript-query-api.md) (Trabajo con la API de consulta integrada del lenguaje JavaScript en Azure Cosmos DB)
- [How to write stored procedures, triggers, and user-defined functions in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md) (Escritura de procedimientos almacenados, desencadenadores y funciones definidas por el usuario [UDF] en Azure Cosmos DB)
- [How to write stored procedures and triggers using Javascript Query API in Azure Cosmos DB](how-to-write-javascript-query-api.md) (Escritura de procedimientos almacenados y desencadenadores con la API de consulta de Javascript en Azure Cosmos DB)
