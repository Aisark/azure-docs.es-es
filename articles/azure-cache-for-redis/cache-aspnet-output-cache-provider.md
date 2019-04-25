---
title: Proveedor de la caché de salida de ASP.NET
description: Obtenga información acerca de cómo almacenar en caché los resultados de página de ASP.NET con Azure Cache for Redis.
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 02/14/2017
ms.author: yegu
ms.openlocfilehash: 3aa2e9e773eb0c07b5f10a57dabf1138b9f3f288
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232909"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Proveedor de caché de resultados de ASP.NET para Azure Cache for Redis
El proveedor de la caché de salida de Redis es un mecanismo de almacenamiento fuera de proceso para los datos de la caché de salida. Estos datos resultan necesarios específicamente para respuestas HTTP completas (caché de resultados de la página). El proveedor se conecta al nuevo punto de extensibilidad del proveedor de caché de salida que se introdujo en ASP.NET 4.

Para usar el proveedor de la caché de salida de Redis, configure primero la caché y luego configure la aplicación de ASP.NET mediante el paquete NuGet del proveedor de la caché de salida de Redis. En este tema se proporcionan instrucciones sobre cómo configurar la aplicación para usar el proveedor de la caché de salida de Redis. Para más información acerca de cómo crear y configurar una instancia de Azure Cache for Redis, consulte [Creación de una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Almacenamiento de los resultados de página de ASP.NET en la caché
Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet de estado de sesión de Azure Cache for Redis, en el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y en **Consola del Administrador de paquetes**.

Ejecute el siguiente comando desde la ventana `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

El paquete NuGet del proveedor de la caché de salida de Redis tiene una dependencia del paquete StackExchange.Redis.StrongName. Si el paquete StackExchange.Redis.StrongName no existe en el proyecto, se instalará. Para obtener más información sobre el paquete de NuGet del proveedor de caché de salida de Redis, vea la página [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) de NuGet.

>[!NOTE]
>Tenga en cuenta que, además del paquete StackExchange.Redis.StrongName con nombre seguro, también está la versión con nombre no seguro de StackExchange.Redis. Si su proyecto está utilizando la versión de StackExchange.Redis con nombre no seguro deberá desinstalarla, ya que de lo contrario se producirán conflictos de nomenclatura en el proyecto. Para obtener más información sobre estos paquetes, consulte [Configuración de los clientes de la caché de .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

El paquete NuGet descarga y agrega las referencias de ensamblado necesarias y agrega la siguiente sección al archivo web.config. Esta sección contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor de memoria caché de salida de Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

En la sección comentada se proporciona un ejemplo de los atributos y la configuración de ejemplo de cada uno.

Configure los atributos con los valores de la hoja de la caché en el Portal de Microsoft Azure y configure los demás valores según prefiera. Para obtener instrucciones acerca de cómo acceder a las propiedades de la caché, consulte [Configuración de Azure Cache for Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** : especifique el punto de conexión de la caché.
* **puerto** : use el puerto no SSL o SSL, según la configuración de SSL.
* **accessKey** : use la clave primaria o secundaria para la caché.
* **ssl** : true si desea proteger las comunicaciones de la caché o el cliente con SLS; de lo contrario, false. Asegúrese de especificar el puerto correcto.
  * El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Especifique true en este valor para usar el puerto SSL. Para más información sobre cómo habilitar el puerto no SSL, consulte la sección [Puertos de acceso](cache-configure.md#access-ports) del tema de [Configuración de caché](cache-configure.md).
* **databaseId** : especifique qué base de datos se usará para los datos de salida de la caché. Si no se especifica, se usa el valor predeterminado de 0.
* **applicationName`<AppName>_<SessionId>_Data`: las claves se almacenan en Redis como** . Este esquema de nomenclatura permite que varias aplicaciones compartan la misma clave. Este parámetro es opcional y, si no se especifica, se usa un valor predeterminado.
* **connectionTimeoutInMilliseconds** : esta opción le permite invalidar la configuración de connectTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de connectTimeout, que es 5000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** : esta opción le permite invalidar la configuración de syncTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de syncTimeout, que es 1000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).

Incorpore una directiva OutputCache a cada página cuyos resultados desea almacenar en caché.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

En el ejemplo anterior, los datos de la página almacenados en la memoria caché permanecerán ahí durante 60 segundos y se almacenará en la memoria caché una versión diferente de la página para cada combinación de parámetros. Para obtener más información sobre la directiva OutputCache, consulte [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Después de realizar estos pasos, la aplicación está configurada para usar el proveedor de la caché de salida de Redis.

## <a name="next-steps"></a>Pasos siguientes
Consulte el [proveedor de estado de sesión de ASP.NET para Azure Cache for Redis](cache-aspnet-session-state-provider.md).

