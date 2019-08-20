---
title: 'Inicio rápido: creación de una aplicación web con la API de Azure para MongoDB y el SDK de Java (Azure Cosmos DB)'
description: Se presenta un ejemplo de código de Java que se puede usar para conectarse a la API de Azure Cosmos DB para MongoDB y realizar consultas.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.custom: seo-java-august2019
ms.openlocfilehash: 701e6f0e93fe853a3703f9923af4dffd16090c80
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036805"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>Inicio rápido: Compilación de una aplicación web mediante la API de Azure Cosmos DB para MongoDB y el SDK de Java

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Cosmos DB. 

En este inicio rápido se muestra cómo crear una cuenta de Cosmos con la [API de Azure Cosmos DB para MongoDB](mongodb-introduction.md). Después, compilará e implementará una aplicación de consola compilada con el [controlador Java de MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
* [Instalación de JDK de Azure y JDK de Azure Stack, versión 8](https://aka.ms/azure-jdks)
* Maven (ejecute `apt-get install maven` si no tiene Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Agregar una colección

Asígnele un nombre a la base de datos nueva (**db**) y a la nueva colección (**coll**).

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra un símbolo del sistema, cree una carpeta nueva denominada ejemplos de GIT y, después, cierre el símbolo del sistema.

    ```bash
    md "C:\git-samples"
    ```

2. Abra una ventana de terminal de Git, como git bash y utilice el comando `cd` para cambiar a la nueva carpeta para instalar la aplicación de ejemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Ejecute el comando siguiente para clonar el repositorio de ejemplo. Este comando crea una copia de la aplicación de ejemplo en el equipo.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. A continuación, abra el código en su editor favorito. 

## <a name="review-the-code"></a>Revisión del código

Este paso es opcional. Si está interesado en aprender cómo se crean los recursos de base de datos en el código, puede revisar los siguientes fragmentos de código. En caso contrario, puede ir directamente a [Actualización de la cadena de conexión](#update-your-connection-string). 

Los fragmentos de código siguientes se han tomado del archivo Program.java.

* Se inicializa DocumentClient.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Se crean una base de datos y una colección.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Se insertan algunos documentos mediante `MongoCollection.insertOne`.

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Se realizan algunas consultas mediante `MongoCollection.find`.

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En la cuenta, seleccione **Inicio rápido**, elija Java y copie la cadena de conexión en el Portapapeles.

2. Abra el archivo `Program.java` y reemplace el argumento del constructor MongoClientURI por la cadena de conexión. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Ejecutar la aplicación de consola

1. Ejecute `mvn package` en un terminal para instalar los módulos npm necesarios.

2. Ejecute `mvn exec:java -D exec.mainClass=GetStarted.Program` en un terminal para iniciar la aplicación de Java.

Ahora puede usar [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) para consultar, modificar y trabajar con estos nuevos datos.

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear una cuenta de Cosmos, crear una colección y ejecutar una aplicación de consola. Ahora puede importar datos adicionales en la base de datos de Cosmos.

> [!div class="nextstepaction"]
> [Importación de datos de MongoDB a Azure Cosmos DB](mongodb-migrate.md)
