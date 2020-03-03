---
title: 'Inicio rápido: Uso de Java para crear una base de datos de documentos mediante Azure Cosmos DB'
description: En este inicio rápido se presenta un ejemplo de código de Java que se puede usar para conectarse a la API de SQL de Azure Cosmos DB y realizar consultas en ellas
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: f29eeba98e0cc89c65dda814e03d63d2f3493c35
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586026"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Inicio rápido: Creación de una aplicación Java para administrar los datos de SQL API de Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

En este inicio rápido se crea y administra una cuenta de Azure Cosmos DB para SQL API desde Azure Portal, y se usa una aplicación de Java clonada desde GitHub. En primer lugar, se crea una cuenta de SQL API de Azure Cosmos DB desde Azure Portal, luego, una aplicación Java mediante el SDK de SQL Java y, después, se agregan los recursos a la cuenta de Cosmos DB mediante la aplicación de Java. Azure Cosmos DB es un servicio de base de datos multimodelo que permite crear y consultar rápidamente bases de datos de documentos, tablas, claves-valores y grafos con funcionalidades de distribución global y escala horizontal.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). O bien, [pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin una suscripción de Azure. También puede usar el [emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con el identificador URI `https://localhost:8081` y la clave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Apunte su variable de entorno `JAVA_HOME` a la carpeta donde está instalado el JDK.
- Un [archivo binario de Maven](https://maven.apache.org/download.cgi). En Ubuntu, ejecute `apt-get install maven` para instalar Maven.
- [Git](https://www.git-scm.com/downloads). En Ubuntu, ejecute `sudo apt-get install git` para instalar Git.

## <a name="introductory-notes"></a>Notas de introducción

*Estructura de una cuenta de Cosmos DB*: independientemente de la API o el lenguaje de programación, una *cuenta* de Cosmos DB contiene de cero o varias *bases de datos*; una *base de datos* contiene de cero a varios *contenedores* y un *contenedor* contiene de cero a varios elementos, como se muestra en el diagrama siguiente:

![Entidades de cuenta de Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

Puede leer más información sobre las bases de datos, los contenedores y los elementos [aquí](databases-containers-items.md). Algunas propiedades importantes se definen en el nivel del contenedor, entre ellas la *capacidad de proceso aprovisionada* y la *clave de partición*. 

La capacidad de proceso aprovisionada se mide en unidades de solicitud (*RU*) que tienen un precio monetario y son un factor determinante crucial en el costo operativo de la cuenta. La capacidad de proceso aprovisionada se puede seleccionar en una granularidad por contenedor o por base de datos; sin embargo, se suele preferir la especificación de capacidad de proceso a nivel de contenedor. Para más información sobre el aprovisionamiento de capacidad de proceso [aquí](set-throughput.md).

A medida que los elementos se insertan en un contenedor de Cosmos DB, la base de datos crece horizontalmente al agregar más almacenamiento y procesos para controlar las solicitudes. La capacidad de almacenamiento y de proceso se agregan en unidades discontinuas conocidas como *particiones* y debe elegir un campo en los documentos para que sea la clave de partición para asignar cada documento a una partición. La forma de administración de las particiones es la asignación a cada partición de un segmento aproximadamente igual fuera del intervalo de valores de la clave de partición. Por lo tanto, se recomienda elegir una clave de partición relativamente aleatoria o distribuida uniformemente. De lo contrario, algunas particiones verán bastantes más solicitudes (*partición de uso frecuente*) mientras que otras verán muchas menos (*partición de uso esporádico*), comportamiento que se debe evitar. [Aquí](partitioning-overview.md) encontrará más información sobre la creación de particiones.

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

Para poder crear una base de datos de documentos, debe crear una cuenta de SQL API con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Agregar un contenedor

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adición de datos de ejemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consulta de los datos

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a empezar a trabajar con el código. Vamos a clonar una aplicación de SQL API desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

Ejecute el comando siguiente para clonar el repositorio de ejemplo. Este comando crea una copia de la aplicación de ejemplo en el equipo.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Revisión del código

Este paso es opcional. Si está interesado en aprender cómo se crean los recursos de base de datos en el código, puede revisar los siguientes fragmentos de código. En caso contrario, puede ir directamente a [Ejecutar la aplicación](#run-the-app). 

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Administración de recursos de base de datos mediante la API sincrónica (sincronización)

* Inicialización de `CosmosClient`. `CosmosClient` proporciona una representación lógica del cliente para el servicio de base de datos de Azure Cosmos. Este cliente se usa para configurar y ejecutar solicitudes en el servicio.
    
    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="CreateSyncClient":::

* Creación de `CosmosDatabase`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="CreateDatabaseIfNotExists":::

* Creación de `CosmosContainer`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="CreateContainerIfNotExists":::

* Creación de elementos mediante el método `createItem`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="CreateItem":::
   
* Las lecturas puntuales se realizan mediante el método `readItem`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="ReadItem":::

* Las consultas SQL a través de JSON se realizan con el método `queryItems`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java" id="QueryItems":::

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Administración de recursos de base de datos mediante la API asincrónica (asincronización)

* Las llamadas API asincrónicas se devuelven inmediatamente, sin esperar una respuesta del servidor. A la vista de esto, los fragmentos de código siguientes muestran patrones de diseño adecuados para realizar todas las tareas de administración anteriores mediante la API asincrónica.

* Inicialización de `CosmosAsyncClient`. `CosmosAsyncClient` proporciona una representación lógica del cliente para el servicio de base de datos de Azure Cosmos. Este cliente se usa para configurar y ejecutar solicitudes asincrónicas en el servicio.
    
    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="CreateAsyncClient":::

* Creación de `CosmosAsyncDatabase`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="CreateDatabaseIfNotExists":::

* Creación de `CosmosAsyncContainer`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="CreateContainerIfNotExists":::

* Al igual que con la API sincrónica, la creación de elementos se realiza mediante el método `createItem`. En este ejemplo se muestra cómo emitir de forma eficaz numerosas solicitudes `createItem` asincrónicas mediante la suscripción a un flujo de Reactive Stream que emite las solicitudes e imprime las notificaciones. Como este sencillo ejemplo se ejecuta completamente y finaliza, se usan instancias de `CountDownLatch` para asegurarse de que el programa no finaliza durante la creación del elemento. **El procedimiento de programación asincrónica adecuado no bloquea llamadas asincrónicas: en casos de uso realistas, las solicitudes se generan a partir de un bucle Main () que se ejecuta de forma indefinida, lo cual elimina la necesidad de realizar un bloqueo temporal en las llamadas asincrónicas.**

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="CreateItem":::
   
* Al igual que con la API sincrónica, las lecturas puntuales se realizan mediante el método `readItem`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="ReadItem":::

* Al igual que con la API sincrónica, las consultas SQL a través de JSON se realizan mediante el método `queryItems`.

    :::code language="java" source="~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java" id="QueryItems":::

## <a name="run-the-app"></a>Ejecución la aplicación

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión e iniciar la aplicación con la información del punto de conexión. Esto permite que la aplicación se comunique con la base de datos hospedada.

1. En la ventana de terminal de GIT, `cd` a la carpeta del código de ejemplo.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. En la ventana del terminal de GIT, use el comando siguiente para instalar los paquetes Java necesarios.

    ```bash
    mvn package
    ```

3. En la ventana del terminal de GIT, use el siguiente comando para iniciar la aplicación Java (reemplace SYNCASYNCMODE por `sync` o `async` según el código de ejemplo que desea ejecutar, reemplace YOUR_COSMOS_DB_HOSTNAME por el valor del identificador URI entre comillas del portal y reemplace YOUR_COSMOS_DB_MASTER_KEY por la clave principal entre comillas del portal)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    La ventana del terminal muestra una notificación de que se ha creado la base de datos FamilyDB. 
    
4. La aplicación crea la base de datos con el nombre `AzureSampleFamilyDB`.
5. La aplicación crea un contenedor con el nombre `FamilyContainer`.
6. La aplicación realizará las lecturas puntuales con los identificadores de objeto y el valor de clave de partición (que es lastName en nuestro ejemplo). 
7. La aplicación consultará los elementos para recuperar todas las familias con el apellido en ("Andersen", "Wakefield", "Johnson").

7. La aplicación no elimina los recursos creados. Vuelva al portal para [limpiar los recursos](#clean-up-resources).  desde su cuenta para que no incurrir en gastos.

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a crear una cuenta de SQL API de Azure Cosmos DB, a crear una base de datos de documentos y un contenedor mediante Data Explorer, y a ejecutar una aplicación de Java para que haga lo mismo mediante programación. Ya puede importar datos adicionales en la cuenta de Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos a Azure Cosmos DB](import-data.md)
