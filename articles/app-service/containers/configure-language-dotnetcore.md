---
title: 'Configuración de aplicaciones de ASP.NET Core: Azure App Service | Microsoft Docs'
description: Obtenga información sobre cómo configurar las aplicaciones de ASP.NET Core para que funcionen en Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990263"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configuración de una aplicación de ASP.NET Core para Azure App Service

Las aplicaciones de ASP.NET Core deben implementarse como binarios compilados. La herramienta de publicación de Visual Studio compila la solución y, a continuación, implementa los binarios compilados directamente, mientras que el motor de implementación de App Service implementa el repositorio de código en primer lugar y, a continuación, compila los binarios.

En esta guía se incluyen conceptos clave e instrucciones para los desarrolladores de ASP.NET Core que usan un contenedor Linux integrado en App Service. Si nunca ha usado Azure App Service, siga primero la [guía de inicio rápido de ASP.NET Core](quickstart-dotnetcore.md) y el [tutorial de ASP.NET Core con SQL Database](tutorial-dotnetcore-sqldb-app.md).

## <a name="show-net-core-version"></a>Mostrar la versión de .NET Core

Para mostrar la versión actual de .NET Core, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas las versiones compatibles de .NET Core, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Establecer la versión de .NET Core

Ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com) para establecer la versión 2.1 de .NET Core:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Acceso a variables de entorno

En App Service, puede [establecer la configuración de la aplicación](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fuera del código de la aplicación. Después, puede tener acceso a ella en cualquier clase mediante el patrón de inserción de dependencias de ASP.NET Core estándar:

```csharp
include Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Si configura una opción de aplicación con el mismo nombre en App Service y en *appsettings.json*, por ejemplo, el valor de App Service tiene prioridad sobre el valor de *appsettings.json*. El valor de *appsettings.json* local permite depurar la aplicación localmente, pero el valor de App Service permite la ejecución de la aplicación en el producto con la configuración de producción. Las cadenas de conexión funcionan de la misma manera. De este modo, puede conservar los secretos de aplicación fuera de su repositorio de código y tener acceso a los valores adecuados sin cambiar el código.

## <a name="get-detailed-exceptions-page"></a>Obtener la página de excepciones detalladas

Cuando la aplicación de ASP.NET genera una excepción en el depurador de Visual Studio, el explorador muestra una página de excepciones detalladas, pero en App Service, esa página se sustituye por un error genérico **HTTP 500** o **Error al procesar la solicitud**. de la directiva). Para mostrar la página de excepciones detalladas en App Service, agregue la configuración de aplicación `ASPNETCORE_ENVIRONMENT` a la aplicación con el siguiente comando en <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detección de sesión de HTTPS

En App Service, la [terminación de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produce en los equilibradores de carga de red, por lo que todas las solicitudes HTTPS llegan a su aplicación en forma de solicitudes HTTP sin cifrar. Si necesita que la lógica de aplicación sepa si las solicitudes del usuario están cifradas o no, configure el middleware de encabezados reenviados en *Startup.cs*:

- Configure el middleware con [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.
- Agregue intervalos de direcciones IP privadas a las redes conocidas, de modo que el middleware pueda confiar en el equilibrador de carga de App Service.
- Invoque el método [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) en `Startup.Configure` antes de llamar a otros middlewares.

Al colocar juntos los tres elementos, el código es similar al ejemplo siguiente:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Para obtener más información, consulte [Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Implementar soluciones de varios proyectos

Al implementar un repositorio de ASP.NET en el motor de implementación con un archivo *.csproj* en el directorio raíz, el motor implementa el proyecto. Cuando se implementa un repositorio de ASP.NET con un archivo *.sln* en el directorio raíz, el motor elige el primer sitio web o proyecto de aplicación web que encuentra como aplicación de App Service. Es posible que el motor no elija el proyecto que desea.

Para implementar una solución de varios proyectos, puede especificar el proyecto que se debe usar en App Service de dos maneras diferentes:

### <a name="using-deployment-file"></a>Uso del archivo .deployment

Agregue un archivo *.deployment* en la raíz del repositorio y agregue el código siguiente:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Uso de configuración de aplicación

En <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, agregue una configuración de aplicación a la aplicación de App Service mediante la ejecución del siguiente comando de CLI. Reemplace *\<app-name>* , *\<resource-group-name>* y *\<project-name>* por los valores adecuados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Acceso a los registros de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sesión SSH en el explorador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Aplicación de ASP.NET Core con SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [P+F sobre App Service en Linux](app-service-linux-faq.md)