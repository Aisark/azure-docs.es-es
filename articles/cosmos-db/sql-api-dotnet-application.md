---
title: 'Tutorial de ASP.NET Core MVC para Azure Cosmos DB: Desarrollo de aplicaciones web'
description: 'Tutorial de ASP.NET Core MVC para crear una aplicación web MVC con Azure Cosmos DB. Almacenará el código JSON y accederá a los datos desde una aplicación de tareas pendientes hospedada en Azure App Service: tutorial de ASP NET Core MVC paso a paso.'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 1cfb7718c55920d817e1f87407fa1af590e9f006
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720913"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desarrollar una aplicación web ASP.NET Core MVC con Azure Cosmos DB mediante el SDK de .NET

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

En este tutorial aprenderá a usar Azure Cosmos DB para almacenar datos, y acceder a estos, desde una aplicación de ASP.NET MVC hospedada en Azure. En este tutorial, usará el SDK de .NET V3. En la siguiente imagen se muestra la página web que se compilará mediante el ejemplo de este artículo:

![Captura de pantalla de la aplicación web MVC de lista de tareas pendientes creada por este tutorial: tutorial ASP NET Core MVC paso a paso](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Si no tiene tiempo para completar el tutorial, puede descargar el proyecto de ejemplo completo de [GitHub][GitHub].

Esta tutorial abarca lo siguiente:

> [!div class="checklist"]
>
> * Creación de una cuenta de Azure Cosmos
> * Creación de una aplicación ASP.NET Core MVC
> * Conexión de la aplicación a Azure Cosmos DB
> * Operaciones de creación, lectura, actualización y eliminación (CRUD) en los datos

> [!TIP]
> En este tutorial se supone que tiene experiencia previa con ASP.NET Core MVC y Azure App Service. Si no está familiarizado con ASP.NET Core o las [herramientas que son requisito previo](#prerequisites), recomendamos que descargue el proyecto de ejemplo completo de [GitHub][GitHub], que agregue los paquetes NuGet necesarios y que lo ejecute. Una vez compilado el proyecto, puede revisar este artículo para obtener información sobre el código en el contexto del proyecto.

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir las instrucciones del presente artículo, asegúrese de tener los siguientes recursos:

* Una cuenta de Azure activa. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas las capturas de pantallas de este artículo son de Microsoft Visual Studio Community 2019. Si usa una versión diferente, es posible que las pantallas y las opciones no coincidan exactamente. La solución funcionará si cumple los requisitos previos.

## <a name="create-an-azure-cosmos-account"></a>Paso 1: Creación de una cuenta de Azure Cosmos

Para comenzar, crearemos una cuenta de Azure Cosmos. Si ya tiene una cuenta de SQL API en Azure Cosmos DB o si usa el emulador de Azure Cosmos DB, vaya al [paso 2: Creación de una aplicación ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

En la siguiente sección, creará una aplicación de ASP.NET Core MVC.

## <a name="create-a-new-mvc-application"></a>Paso 2: Crear una aplicación ASP.NET Core MVC nueva

1. Abra Visual Studio y seleccione **Crear un proyecto**.

1. En **Crear un proyecto**, busque y elija **Aplicación web ASP.NET Core** para C#. Seleccione **Next** (Siguiente) para continuar.

   ![Crear un proyecto nuevo de aplicación web de ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. En **Configure su nuevo proyecto**, asigne al proyecto el nombre *todo* y seleccione **Crear**.

1. En **Crear una nueva aplicación web ASP.NET Core**, elija **Aplicación web (controlador de vista de modelos)** . Seleccione **Crear** para continuar.

   Visual Studio crea una aplicación MVC vacía.

1. Seleccione **Depurar** > **Iniciar depuración** o F5 para ejecutar la aplicación ASP.NET localmente.

## <a name="add-nuget-packages"></a>Paso 3: Incorporación del paquete NuGet de Azure Cosmos DB al proyecto

Ahora que tenemos la mayoría del código de plataforma de ASP.NET Core MVC que necesitamos para esta solución, vamos a agregar los paquetes NuGet necesarios para conectarse a Azure Cosmos DB.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.

1. En **Administrador de paquetes NuGet**, busque y seleccione **Microsoft.Azure.Cosmos**. Seleccione **Instalar**.

   ![Instalación de un paquete NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio descarga e instala el paquete de Azure Cosmos DB y sus dependencias.

   También puede usar la **Consola del Administrador de paquetes** para instalar el paquete NuGet. Para ello, seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**. En el símbolo del sistema, escriba el siguiente comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Paso 4: Configurar la aplicación ASP.NET Core MVC

Ahora vamos a agregar los modelos, las vistas y los controladores a esta aplicación MVC.

### <a name="add-a-model"></a> Incorporación de un modelo

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Models** y seleccione **Agregar** > **Clase**.

1. En **Agregar nuevo elemento**, asigne a la nueva clase el nombre *Item.cs* y seleccione **Agregar**.

1. Reemplace el contenido de la clase *Item.cs* por el código siguiente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

Azure Cosmos DB usa JSON para trasladar y almacenar los datos. Puede usar el atributo `JsonProperty` para controlar el modo en que JSON serializa y deserializa los objetos. La clase `Item` muestra el atributo `JsonProperty`. Este código controla el formato del nombre de propiedad que entra en JSON. También cambia el nombre de la propiedad `Completed` de .NET.

### <a name="add-views"></a>Adición de vistas

A continuación, se van a crear las tres vistas siguientes.

* Agregar una vista de elementos de lista
* Agregar una nueva vista de elementos
* Agregar una vista de edición de elementos.

#### <a name="AddItemIndexView"></a>Agregar una vista de elementos de lista

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Views** y seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Item*.

1. Haga clic con el botón derecho en la carpeta vacía **Item** y seleccione **Agregar** > **Vista**.

1. En **Agregar vista MVC**, proporcione los siguientes valores:

   * En **Nombre de la vista**, escriba *Index*.
   * En **Plantilla**, seleccione **List**.
   * En **Clase de modelo**, seleccione **Item (todo.Models)** .
   * Seleccione **Usar una página de diseño** y escriba *~/Views/Shared/_Layout.cshtml*.

   ![Captura de pantalla que muestra el cuadro de diálogo Agregar vista MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Cuando haya agregado estos valores, haga clic en **Agregar** y permita que Visual Studio cree una vista de plantilla.

Una vez hecho, Visual Studio abre el archivo *cshtml* que crea. Puede cerrar el archivo en Visual Studio. Volveremos a él más adelante.

#### <a name="AddNewIndexView"></a>Agregar una nueva vista de elementos

Lo mismo que ha creado una vista para elementos de lista, cree una vista para crear elementos mediante los pasos siguientes:

1. En el **Explorador de soluciones**, vuelva a hacer clic con el botón derecho en la carpeta **Item** y seleccione **Agregar** > **Vista**.

1. En **Agregar vista MVC**, realice los cambios siguientes:

   * En **Nombre de la vista**, escriba *Crear*.
   * En **Plantilla**, seleccione **Crear**.
   * En **Clase de modelo**, seleccione **Item (todo.Models)** .
   * Seleccione **Usar una página de diseño** y escriba *~/Views/Shared/_Layout.cshtml*.
   * Seleccione **Agregar**.

#### <a name="AddEditIndexView"></a>Agregar una vista de edición de elementos

Y por último, agregue una vista para editar un elemento con los pasos siguientes:

1. En el **Explorador de soluciones**, vuelva a hacer clic con el botón derecho en la carpeta **Item** y seleccione **Agregar** > **Vista**.

1. En **Agregar vista MVC**, realice los cambios siguientes:

   * En el cuadro **Nombre de vista**, escriba *Editar*.
   * En el cuadro **Plantilla**, seleccione **Editar**.
   * En el cuadro **Clase de modelo**, seleccione **Elemento (todo.Models)** .
   * Seleccione **Usar una página de diseño** y escriba *~/Views/Shared/_Layout.cshtml*.
   * Seleccione **Agregar**.

Una vez hecho esto, cierre todos los documentos *cshtml* en Visual Studio, porque volverá a estas vistas más adelante.

### <a name="add-a-controller"></a>Adición de un controlador

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **Controllers** y seleccione **Agregar** > **Controlador**.

1. En **Agregar scaffold**, seleccione **Controlador de MVC: en blanco** y **Agregar**.

   ![Seleccionar controlador de MVC: en blanco en Agregar scaffold](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Asigne un nombre al nuevo controlador, *ItemController*.

1. Reemplace el contenido de *ItemController.cs* por el código siguiente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

El atributo **ValidateAntiForgeryToken** se usa aquí para ayudar a proteger esta aplicación frente a los ataques de falsificación de solicitud entre sitios. Las vistas funcionarán también con este token antifalsificación. Para obtener más información y ejemplos, vea [Prevención de ataques de falsificación (CSRF) de solicitudes entre sitios en la aplicación ASP.NET MVC][Preventing Cross-Site Request Forgery]. El código de origen proporcionado en [GitHub][GitHub] tiene la implementación completa en su lugar.

También se usa el atributo **Bind** en el parámetro de método para ayudar a proteger contra ataques de publicación en exceso. Para más información, consulte [Tutorial: Implementar la funcionalidad CRUD con Entity Framework en ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Paso 5: Conexión a Azure Cosmos DB

Ahora que ya se han tratado los elementos estándar de MVC, se va a agregar el código para conectarse a Azure Cosmos DB y realizar operaciones CRUD.

### <a name="perform-crud-operations"></a>Realizar operaciones CRUD en los datos

Primero, vamos a agregar una clase que contenga la lógica para conectarse a Azure Cosmos DB y usarlo. En este tutorial, se encapsulará esta lógica en una clase llamada `CosmosDBService` y una interfaz llamada `ICosmosDBService`. Este servicio realiza las operaciones CRUD. También realiza las operaciones de fuente de lectura, como enumeración de elementos incompletos y creación, edición y eliminación de los elementos.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nueva carpeta**. Asigne a la carpeta el nombre *Services*.

1. Haga clic con el botón derecho en la carpeta **Services** y seleccione **Agregar** > **Clase**. Asigne a la nueva clase el nombre *CosmosDBService* y seleccione **Agregar**.

1. Reemplace el contenido de *CosmosDBService.cs* por el código siguiente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Repita los dos pasos anteriores, pero esta vez use el nombre *ICosmosDBService* y el código siguiente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. En el controlador **ConfigureServices**, agregue la siguiente línea:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    El código del paso anterior recibe `CosmosClient` como parte del constructor. Después de la canalización de ASP.NET Core, tenemos que ir al archivo *Startup.cs* del proyecto. El código de este paso inicializa el cliente en función de la configuración como una instancia singleton que se va a insertar mediante la [inserción de dependencias en ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. En el mismo archivo, agregue el siguiente método **InitializeCosmosClientInstanceAsync**, que lee la configuración e inicializa el cliente.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Defina la configuración en el archivo *appsettings.json* del proyecto. Abra el archivo y agregue una sección llamada **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Si ejecuta la aplicación, la canalización de ASP.NET Core crea instancias de **CosmosDbService** y mantiene una única instancia como singleton. Cuando **ItemController** procesa las solicitudes del lado cliente, recibe esta instancia única y puede usarla para las operaciones CRUD.

Si compila y ejecuta este proyecto ahora, deberá ver algo parecido a esto:

![Captura de pantalla de la aplicación web de lista de tareas pendientes creada con este tutorial de base de datos](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>Paso 6: Ejecución de la aplicación de forma local

Para probar la aplicación en el equipo local, siga estos pasos:

1. Presione F5 en Visual Studio para compilar la aplicación en modo de depuración. De esa forma, se debe compilar la aplicación e iniciar un explorador con la página de cuadrícula vacía que vimos anteriormente:

   ![Captura de pantalla de la aplicación web de lista de tareas pendientes creada con este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Seleccione el vínculo **Crear nuevo** y agregue valores a los campos **Nombre** y **Descripción**. Deje desactivada la casilla **Completado**. Cuando está activada, la aplicación agrega el nuevo elemento en estado completado. El elemento no aparece en la lista inicial.

1. Seleccione **Crear**. La aplicación le devuelve a la vista **Índice** y el elemento aparece en la lista. Puede agregar unos cuantos elementos más a la lista **To-Do** (Tareas pendientes).

    ![Captura de pantalla de la vista de índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Seleccione **Editar** junto a un **elemento** de la lista. La aplicación abre la vista **Edición**, donde puede actualizar cualquier propiedad del objeto, incluida la marca **Completado**. Si selecciona **Completado** y selecciona **Guardar**, la aplicación muestra el **elemento** como completada en la lista.

   ![Captura de pantalla de la vista de índice con el cuadro Completado activado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Compruebe el estado de los datos en el servicio Azure Cosmos DB con [Cosmos Explorer](https://cosmos.azure.com) o el Explorador de datos del Emulador de Azure Cosmos DB.

1. Una vez que haya probado la aplicación, seleccione Ctrl+F5 para detener la depuración de la aplicación. Está listo para la implementación.

## <a name="deploy-the-application-to-azure"></a>Paso 7: Implementación de la aplicación

Ahora que toda la aplicación funciona correctamente con Azure Cosmos DB, vamos a implementar esta aplicación web en Azure App Service.  

1. Para publicar esta aplicación, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Publicar**.

1. En **Elegir un destino de publicación**, seleccione **App Service**.

1. Para usar un perfil de App Service existente, elija **Seleccionar existente** y, a continuación, seleccione **Publicar**.

1. En **App Service**, seleccione una **Suscripción**. Use el filtro **Ver** para ordenar por grupo de recursos o tipo de recurso.

1. Busque el perfil y seleccione **Aceptar**. A continuación, busque la instancia de Azure App Service necesaria y seleccione **Aceptar**.

   ![Cuadro de diálogo App Service en Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Otra opción es crear un nuevo perfil:

1. Como en el procedimiento anterior, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Publicar**.
  
1. En **Elegir un destino de publicación**, seleccione **App Service**.

1. En **Elegir un destino de publicación**, seleccione **Crear nuevo** y seleccione **Publicar**.

1. En **App Service**, escriba el nombre de la aplicación web y la suscripción, el grupo de recursos y el plan de hospedaje adecuados y, luego, haga clic en **Crear**.

   ![Cuadro de diálogo Crear App Service en Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

En unos segundos, Visual Studio publica la aplicación web e inicia un explorador donde puede ver su trabajo ejecutándose en Azure.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web MVC ASP.NET Core. La aplicación puede acceder a los datos almacenados en Azure Cosmos DB. Ahora puede continuar con estos recursos:

* [Creación de particiones en Azure Cosmos DB](./partitioning-overview.md)
* [Introducción a las consultas SQL](./how-to-sql-query.md)
* [Procedimientos para modelar y crear particiones de datos en Azure Cosmos DB mediante un ejemplo real](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
