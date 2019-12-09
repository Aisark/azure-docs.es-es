---
title: 'Tutorial: Acceso a los datos con una identidad administrada'
description: Aprenda a hacer que la conectividad con la base de datos sea más segura con una identidad administrada y también a aplicarlo a otros servicios de Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc
ms.openlocfilehash: 53dc6291d699ce08e08d3504bfcd63a525eb9196
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671192"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Protección de la conexión con Azure SQL Database desde App Service mediante una identidad administrada

[App Service](overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático en Azure. También proporciona una [identidad administrada](overview-managed-identity.md) para la aplicación, la cual constituye una solución inmediata para proteger el acceso a [Azure SQL Database](/azure/sql-database/) y a otros servicios de Azure. Las identidades administradas de App Service hacen que su aplicación sea más segura mediante la eliminación de los secretos de aplicación como, por ejemplo, las credenciales en las cadenas de conexión. En este tutorial, agregará una identidad administrada a la aplicación web de ejemplo que se creó en los tutoriales siguientes: 

- [Tutorial: Compilación de una aplicación ASP.NET en Azure con SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Compilación de una aplicación ASP.NET Core y SQL Database en Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Cuando haya terminado, la aplicación de ejemplo se conectará a SQL Database de forma segura sin necesidad de nombres de usuario ni contraseñas.

> [!NOTE]
> Los pasos descritos en este tutorial son compatibles con las siguientes versiones:
> 
> - .NET Framework 4.7.2
> - .NET Core 2.2
>

Lo qué aprenderá:

> [!div class="checklist"]
> * Habilitar identidades administradas
> * Conceder a SQL Database acceso a la identidad administrada
> * Configurar Entity Framework para utilizar la autenticación de Azure AD con SQL Database
> * Conectarse a SQL Database desde Visual Studio mediante la autenticación de Azure AD

> [!NOTE]
>La autenticación de Azure AD es _distinta_ de la [autenticación integrada de Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) en Active Directory (AD DS) local. AD DS y Azure AD utilizan protocolos de autenticación completamente diferentes. Para más información, consulte [Documentación de Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Este artículo continúa donde lo dejó en [Tutorial: Creación de una aplicación ASP.NET en Azure con SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) o [Tutorial: Compilación de una aplicación ASP.NET Core y SQL Database en Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Si aún no lo ha hecho, siga uno de los dos tutoriales en primer lugar. Como alternativa, puede adaptar los pasos para su propia aplicación .NET con SQL Database.

Para depurar la aplicación con SQL Database como back-end, asegúrese de permitir la conexión de cliente desde el equipo. Si no es así, agregue la dirección IP del cliente siguiendo los pasos que se describen en [Administración de reglas de firewall de nivel de servidor mediante Azure Portal](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Concesión del acceso a la base de datos a un usuario de Azure AD

Primero debe habilitar la autenticación de Azure AD para SQL Database mediante la asignación de un usuario de Azure AD como administrador de Active Directory para el servidor de SQL Database. Este usuario no es la cuenta Microsoft que usó para suscribirse a Azure. Debe ser un usuario que haya creado, importado, sincronizado o invitado en Azure AD. Para más información sobre los usuarios de Azure AD permitidos, consulte las [características de Azure AD y las limitaciones de en SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Si el inquilino de Azure AD aún no tiene un usuario, cree uno siguiendo los pasos que se indican en [Incorporación o eliminación de usuarios mediante Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Busque el identificador de objeto del usuario de Azure AD mediante [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) y reemplace *\<nombre-principal-de-usuario>* . El resultado se guardará en una variable.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver la lista de todos los nombres principales de usuario de Azure AD, ejecute `az ad user list --query [].userPrincipalName`.
>

Agregue este usuario de Azure AD como administrador de Active Directory mediante el comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) en Cloud Shell. En el siguiente comando, reemplace *\<nombre del servidor>* con el nombre del servidor de SQL Database (sin el sufijo `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para más información sobre cómo agregar un administrador de Active Directory, consulte [Provision an Azure Active Directory administrator for your Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) (Aprovisionamiento de un administrador de Azure Active Directory para el servidor de Azure SQL Database)

## <a name="set-up-visual-studio"></a>Configuración de Visual Studio

### <a name="windows"></a>Windows
Visual Studio para Windows está integrado con la autenticación de Azure AD. Para habilitar el desarrollo y la depuración en Visual Studio, agregue el usuario de Azure AD en Visual Studio; para ello, seleccione **Archivo** > **Configuración de la cuenta** en el menú y haga clic en **Agregar un cuenta**.

Para establecer el usuario de Azure AD para la autenticación de servicio de Azure, seleccione **Herramientas** > **Opciones** en el menú y, después, **Azure Service Authentication** (Autenticación del servicio de Azure) > **Selección de cuentas**. Seleccione el usuario de Azure AD que agregó y haga clic en **Aceptar**.

Ahora está listo para desarrollar y depurar la aplicación con SQL Database como back-end y mediante la autenticación de Azure AD.

### <a name="macos"></a>MacOS

Visual Studio para Mac no está integrado con la autenticación de Azure AD. No obstante, la biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) que utilizará más adelante puede usar tokens de la CLI de Azure. Para habilitar el desarrollo y la depuración en Visual Studio, primero debe [instalar la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) en la máquina local.

Una vez que se haya instalado la CLI de Azure en la máquina local, inicie sesión en la CLI de Azure con el siguiente comando mediante el usuario de Azure AD:

```bash
az login --allow-no-subscriptions
```
Ahora está listo para desarrollar y depurar la aplicación con SQL Database como back-end y mediante la autenticación de Azure AD.

## <a name="modify-your-project"></a>Modificación del proyecto

Los pasos que se siguen para el proyecto dependen de si se trata de un proyecto de ASP.NET o de ASP.NET Core.

- [Modificación de ASP.NET](#modify-aspnet)
- [Modificación de ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificación de ASP.NET

En Visual Studio, abra la consola del administrador de paquetes y agregue el paquete NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

En *Web.config*, desde el principio del archivo, realice los siguientes cambios:

- En `<configSections>`, agregue la siguiente declaración de la sección:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Bajo la etiqueta de cierre `</configSections>`, agregue el siguiente código XML para `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Busque la cadena de conexión denominada `MyDbConnection` y reemplace su valor `connectionString` por `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Reemplace _\<nombre-del-servidor>_ y _\<nombre-de-la-base-de-datos>_ por el nombre del servidor y el de la base de datos.

Eso es todo lo que necesita para conectarse a SQL Database. Al depurar en Visual Studio, el código utiliza el usuario de Azure AD que configuró en [Configuración de Visual Studio.](#set-up-visual-studio) Después, configurará el servidor de SQL Database para permitir la conexión desde la identidad administrada de la aplicación App Service.

Escriba `Ctrl+F5` para ejecutar la aplicación de nuevo. Ahora, la misma aplicación CRUD del explorador se conectará a Azure SQL Database directamente con la autenticación de Azure AD. Esta configuración permite ejecutar migraciones de base de datos desde Visual Studio.

### <a name="modify-aspnet-core"></a>Modificación de ASP.NET Core

En Visual Studio, abra la consola del administrador de paquetes y agregue el paquete NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

En el [tutorial de ASP.NET Core y SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md), la cadena de conexión `MyDbConnection` no se usa porque el entorno de desarrollo local utiliza un archivo de base de datos de SQLite y el entorno de producción de Azure usa una cadena de conexión de App Service. Con la autenticación de Active Directory, es conveniente que ambos entornos usen la misma cadena de conexión. En *appsettings.json*, reemplace el valor de la cadena de conexión `MyDbConnection` por:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

En *Startup.cs*, quite la sección de código que agregó antes:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Después, reemplácela por el código siguiente:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

A continuación, proporcione el contexto de base de datos de Entity Framework con el token de acceso para la base de datos SQL Database. En *Data\MyDatabaseContext.cs*, agregue el código siguiente dentro de las llaves del constructor `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` vacío:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Este código de demostración es sincrónico para mayor claridad y simplicidad.

Eso es todo lo que necesita para conectarse a SQL Database. Al depurar en Visual Studio, el código utiliza el usuario de Azure AD que configuró en [Configuración de Visual Studio.](#set-up-visual-studio) Después, configurará el servidor de SQL Database para permitir la conexión desde la identidad administrada de la aplicación App Service. La `AzureServiceTokenProvider` clase almacena el token en la memoria y lo recupera de Azure AD justo antes de que expire. Para actualizar el token no es necesario ningún código personalizado.

> [!TIP]
> Si el usuario de Azure AD que ha configurado tiene acceso a varios inquilinos, llame a `GetAccessTokenAsync("https://database.windows.net/", tenantid)` con el identificador de inquilino que desee para recuperar el token de acceso adecuado.

Escriba `Ctrl+F5` para ejecutar la aplicación de nuevo. Ahora, la misma aplicación CRUD del explorador se conectará a Azure SQL Database directamente con la autenticación de Azure AD. Esta configuración permite ejecutar migraciones de base de datos desde Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Uso de la conectividad de la identidad administrada

A continuación, configure la aplicación de App Service para conectarse a SQL Database con una identidad administrada asignada por el sistema.

### <a name="enable-managed-identity-on-app"></a>Habilitación de la identidad administrada en la aplicación

Para habilitar una identidad administrada para la aplicación de Azure, use el comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) de Cloud Shell. En el siguiente comando, reemplace *\<nombre-de-la-aplicación>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Este es un ejemplo de la salida:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="grant-permissions-to-managed-identity"></a>Concesión	 de permisos a una identidad administrada

> [!NOTE]
> Si lo desea, puede agregar la identidad a un [grupo de Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md) y, a continuación, conceder acceso a SQL Database al grupo de Azure AD en lugar de a la identidad. Por ejemplo, los siguientes comandos agregan la identidad administrada del paso anterior a un nuevo grupo llamado _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

En Cloud Shell, inicie sesión en SQL Database mediante el comando SQLCMD. Reemplace _\<nombre-del-servidor>_ por su nombre de servidor de SQL Database, _\<nombre-de-la-base-de-datos>_ por el nombre de la base de datos que utiliza la aplicación y _\<nombre-de-usuario-de-aad>_ y _\<contraseña-de-aad>_ por las credenciales de usuario de Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

En el símbolo del sistema de SQL para la base de datos que desee, ejecute los siguientes comandos para agregar el grupo de Azure AD y conceder los permisos que la aplicación necesita. Por ejemplo, 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* es el nombre de la identidad administrada en Azure AD. Puesto que está asignada por el sistema, siempre es igual que el nombre de la aplicación de App Service. Para conceder permisos para un grupo de Azure AD, utilice en su lugar el nombre para mostrar del grupo (por ejemplo, *myAzureSQLDBAccessGroup*).

Escriba `EXIT` para volver al símbolo del sistema de Cloud Shell.

### <a name="modify-connection-string"></a>Modificación de la cadena de conexión

Recuerde que los mismos cambios que haya realizado en *Web.config* o *appsettings.json* funcionan con la identidad administrada, por lo que lo único necesario es eliminar la cadena de conexión existente de App Service, que Visual Studio creó al implementar la aplicación inicialmente. En los siguientes comandos, reemplace *\<nombre-de-la-aplicación>* por el nombre de la aplicación.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicación de los cambios

Ahora lo único que queda es publicar los cambios en Azure.

**Si venía del [Tutorial: Creación de una aplicación de ASP.NET en Azure con SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , publique los cambios en Visual Studio. En el **Explorador de soluciones**, haga clic con el botón derecho en su proyecto **DotNetAppSqlDb** y seleccione **Publicar**.

![Publicar desde el Explorador de soluciones](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

En la página de publicación, haga clic en **Publicar**. 

**Si venía del [Tutorial: Cree una aplicación ASP.NET Core y SQL Database en Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)** y publique los cambios con Git, con los siguientes comandos:

```bash
git commit -am "configure managed identity"
git push azure master
```

Cuando la página web nueva muestra su lista de tareas pendientes, la aplicación se conecta a la base de datos mediante la identidad administrada.

![Aplicación de Azure después de Migraciones de Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Ahora ya puede editar la lista de tareas pendientes como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Habilitar identidades administradas
> * Conceder a SQL Database acceso a la identidad administrada
> * Configurar Entity Framework para utilizar la autenticación de Azure AD con SQL Database
> * Conectarse a SQL Database desde Visual Studio mediante la autenticación de Azure AD

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignación de un nombre DNS personalizado existente a Azure App Service](app-service-web-tutorial-custom-domain.md)
