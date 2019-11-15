---
title: 'Inicio rápido: Compilación de una aplicación de Python con una cuenta de SQL API de Azure Cosmos DB'
description: En este tema se presenta un código de ejemplo de Python que se puede usar para conectarse a SQL API de Azure Cosmos DB y realizar consultas.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 82426c0093550864b421d7acc35780c4173895a8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824728"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Inicio rápido: Compilación de una aplicación de Python mediante una cuenta de SQL API de Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

En esta guía de inicio rápido se muestra cómo crear una cuenta de [SQL API](sql-api-introduction.md) de Azure Cosmos DB, una base de datos de documentos y un contenedor mediante Azure Portal. Luego se compila y se ejecuta una aplicación de consola compilada con el SDK de Python para [SQL API](sql-api-sdk-python.md).

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear y consultar documentos, claves y valores, columnas anchas y bases de datos de grafos rápidamente. Todas estas operaciones se benefician de la distribución y la escalabilidad de Azure Cosmos DB.

Este inicio rápido usa la versión 4 del [SDK de Python](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Requisitos previos

* [Python 3.6](https://www.python.org/downloads/), con el ejecutable `python` disponible en `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensión de Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Agregar un contenedor

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adición de datos de ejemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consulta de los datos

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de SQL API desde GitHub, a establecer la cadena de conexión y a ejecutarla.

1. Abra un símbolo del sistema, cree una carpeta nueva denominada ejemplos de GIT y, después, cierre el símbolo del sistema.

    ```cmd
    md "git-samples"
    ```
   Si usa un símbolo del sistema de Bash, debe usar entonces el comando siguiente:

   ```bash
   mkdir "git-samples"
   ```

2. Abra una ventana de terminal de Git, como git bash y utilice el comando `cd` para cambiar a la nueva carpeta para instalar la aplicación de ejemplo.

    ```bash
    cd "git-samples"
    ```

3. Ejecute el comando siguiente para clonar el repositorio de ejemplo. Este comando crea una copia de la aplicación de ejemplo en el equipo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](https://portal.azure.com/), en la cuenta de Azure Cosmos, en el panel de navegación izquierdo, haga clic en **Claves**. Tiene que usar los botones de copia del lado derecho de la pantalla para copiar los valores de **URI** y **Clave principal** en el archivo `cosmos_get_started.py` en el paso siguiente.

    ![Obtención de una clave de acceso y un URI en la configuración de claves de Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Abra el archivo `cosmos_get_started.py` en \git-samples\azure-cosmos-db-python-getting-started en Visual Studio Code.

3. Copie el valor del **URI** del portal (con el botón de copia) y conviértalo en el valor de la variable **endpoint** en ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Después, copie el valor de la **clave principal** del portal y conviértalo en el valor de **key** en ``cosmos_get_started.py``. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

    `key = 'FILLME'`

5. Guarde el archivo ``cosmos_get_started.py``.

## <a name="review-the-code"></a>Revisión del código

Este paso es opcional. Aprenda sobre los recursos de base de datos creados en código o vaya directamente a [Actualización de la cadena de conexión](#update-your-connection-string).

Los fragmentos de código siguientes se han tomado del archivo `cosmos_get_started.py`.

* Se inicializa CosmosClient. Asegúrese de actualizar los valores de "endpoint" y "key" según se describe en la sección [Actualización de la cadena de conexión](#update-your-connection-string). 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Se crea una base de datos.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Se crea un nuevo contenedor, con 400 RU/s de [rendimiento aprovisionado](request-units.md). Elegimos `lastName` como [clave de partición](partitioning-overview.md#choose-partitionkey), lo que nos permite realizar consultas eficaces que filtran por esta propiedad. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Se agregan algunos elementos al contenedor. Los contenedores son una colección de elementos (documentos JSON) que pueden tener un esquema variado. Los métodos auxiliares ```get_[name]_family_item``` devuelven representaciones de una familia que se almacenan en Azure Cosmos DB como documentos JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Las lecturas puntuales (búsquedas de valores de clave) se realizan mediante el método `read_item`. Se imprime la [carga de unidad de solicitud](request-units.md) de cada operación.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Se realiza una consulta con la sintaxis de consulta SQL. Dado que usamos los valores de clave de partición de ```lastName``` en la cláusula WHERE, Azure Cosmos DB enrutará eficazmente esta consulta a las particiones pertinentes, lo que mejora el rendimiento.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Ejecución de la aplicación

1. En el Visual Studio Code, seleccione **Ver** > **Paleta de comandos**. 

2. En el símbolo del sistema, escriba **Python: Select Interpreter** y seleccione la versión de Python que se va a utilizar.

    El pie de página de Visual Studio Code se actualiza para indicar el intérprete seleccionado. 

3. Seleccione **Ver** > **Terminal integrado** para abrir el terminal integrado de Visual Studio Code.

4. En la ventana del terminal integrado, asegúrese de que está en la carpeta azure-cosmos-db-python-getting-started. Si no lo está, ejecute el siguiente comando para cambiar a la carpeta de ejemplo. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Ejecute el comando siguiente para instalar el paquete azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Si aparece un error que indica que se deniega el acceso al intentar instalar azure-cosmos, será preciso [ejecutar VS Code como administrador](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Ejecute el siguiente comando para ejecutar el ejemplo y crear y almacenar documentos nuevos en Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Para confirmar que los nuevos elementos se han creado y guardado, en Azure Portal, seleccione **Data Explorer** > **AzureSampleFamilyDatabase** > **Elementos**. Vea los elementos que se han creado. Por ejemplo, este es un documento JSON de ejemplo para la familia Andersen:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Revisión de los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha aprendido a crear una cuenta de Azure Cosmos, a crear un contenedor mediante Data Explorer y a ejecutar una aplicación. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos en Azure Cosmos DB para SQL API](import-data.md)


