---
title: Filtrado y preprocesamiento en el SDK de Azure Application Insights | Microsoft Docs
description: Escriba procesadores e inicializadores de telemetría para que el SDK filtre o agregue propiedades a los datos antes de enviar la telemetría al portal de Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 1e02e227180bb0082dd87ab8f5d2fe64e19b60f2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677802"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtro y preprocesamiento de la telemetría en el SDK de Application Insights

Puede escribir y configurar complementos para el SDK de Application Insights con el fin de personalizar cómo se enriquece y se procesa la telemetría antes de enviarla al servicio de Application Insights.

* [muestreo](sampling.md) reduce el volumen de la telemetría sin que ello influya en las estadísticas. Mantiene juntos los puntos de datos relacionados para que pueda navegar entre ellos a la hora de diagnosticar un problema. En el portal, se multiplican los recuentos totales para compensar el muestreo.
* El filtro con procesadores de telemetría permite filtrar la telemetría en el SDK antes de enviarla al servidor. Por ejemplo, para reducir el volumen de la telemetría puede excluir las solicitudes de robots. El filtro constituye un enfoque más básico que el muestreo para reducir el tráfico. Permite ejercer más control sobre lo que se transmite, pero debe tener en cuenta que se verán afectadas las estadísticas: por ejemplo, si filtra todas las solicitudes correctas.
* [Los inicializadores de telemetría agregan o modifican propiedades](#add-properties) a cualquier telemetría enviada desde la aplicación, incluida la de los módulos estándar. Por ejemplo, puede agregar valores calculados o números de versión para filtrar los datos en el portal.
* [API del SDK](../../azure-monitor/app/api-custom-events-metrics.md) se usa para enviar métricas y eventos personalizados.

Antes de comenzar:

* Instale el SDK adecuado para su aplicación: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [No HTTP/Trabajo para .NET/.NET Core](worker-service.md) o [Java](../../azure-monitor/app/java-get-started.md).

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrado: ITelemetryProcessor

Esta técnica le ofrece un control directo sobre lo que se incluirá en la transmisión de telemetría o lo que se excluirá de ella. El filtrado se puede usar para impedir el envío de elementos de telemetría a Application Insights. Se puede utilizar junto con el muestreo o por separado.

Para filtrar la telemetría, escriba un procesador de telemetría y regístrelo con el `TelemetryConfiguration`. Toda la telemetría pasa por el procesador. Puede optar por no incluirlo en la transmisión o por proporcionárselo al procesador siguiente en la cadena. Esto incluye la telemetría de los módulos estándar como el recopilador de solicitudes HTTP y el recopilador de dependencia, así como la telemetría de la que haya realizado un seguimiento. Por ejemplo, puede filtrar para dejar fuera la telemetría acerca de las solicitudes de robots o las llamadas de dependencia correctas.

> [!WARNING]
> El filtrado de la telemetría enviada desde el SDK usando procesadores puede sesgar las estadísticas que se ven en el portal, y dificultar el seguimiento de elementos relacionados.
>
> En su lugar, puede efectuar un [muestreo](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creación de un procesador de telemetría (C#)

1. Para crear un filtro, implemente `ITelemetryProcessor`.

    Observe que los procesadores de telemetría forman una cadena de procesamiento. Al crear instancias de un procesador de telemetría, se le proporciona una referencia al procesador siguiente en la cadena. Cuando un punto de datos de telemetría se pasa al método de proceso, realiza su trabajo y, a continuación, llama (o no) al procesador de telemetría siguiente en la cadena.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Agregue el procesador.

**Aplicaciones ASP.NET**: inserte este fragmento en ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Puede pasar valores de cadena desde el archivo .config proporcionando propiedades con nombre públicas en la clase.

> [!WARNING]
> Tenga cuidado para que el nombre de tipo y los nombres de propiedad del archivo .config coincidan con los nombres de clase y propiedad del código. Si el archivo .config hace referencia a un tipo o propiedad que no existe, el SDK puede producir un error de forma silenciosa al enviar cualquier telemetría.
>

**Alternativamente,** se puede inicializar el filtro en el código. En una clase de inicialización adecuada (por ejemplo AppStart de `Global.asax.cs`) inserte el procesador en la cadena:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Los TelemetryClients creados a partir de este punto usarán sus procesadores.

**Aplicaciones de ASP.NET Core/ Servicio de trabajo**

> [!NOTE]
> La adición de un procesador mediante `ApplicationInsights.config` o `TelemetryConfiguration.Active` no es válida para las aplicaciones de ASP.NET Core o si usa el SDK de Microsoft.ApplicationInsights.WorkerService.

Para las aplicaciones escritas mediante [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) o [WorkerService](worker-service.md#adding-telemetry-processors), la adición de un nuevo `TelemetryProcessor` se realiza mediante el método de extensión `AddApplicationInsightsTelemetryProcessor` en `IServiceCollection`, como se muestra a continuación. Se llama a este método en el método `ConfigureServices` de su clase `Startup.cs`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtros de ejemplo

#### <a name="synthetic-requests"></a>Solicitudes sintéticas

Filtrar las pruebas web y robots. Aunque el Explorador de métricas proporciona la opción para filtrar y dejar fuera los orígenes sintéticos, esta opción reduce el tráfico y el tamaño de ingesta filtrándolos en el SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Error de autenticación

Filtrar solicitudes con una respuesta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar las llamadas de dependencia rápida y remota

Si solo desea diagnosticar las llamadas que son lentas, descarte las rápidas.

> [!NOTE]
> Esto sesgará las estadísticas que se ve en el portal.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnóstico de problemas de dependencia

[este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) se describe un proyecto para diagnosticar problemas de dependencia enviando automáticamente pings regulares a las dependencias.

<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Agregar propiedades: ITelemetryInitializer

Use los inicializadores de telemetría para enriquecer la telemetría con información adicional o para invalidar las propiedades de telemetría establecidas por los módulos de telemetría estándar.

Por ejemplo, para el paquete Application Insights para web recopila telemetría acerca de las solicitudes HTTP. De forma predeterminada, marca como errónea cualquier solicitud con un código de respuesta >= 400. Pero si desea tratar 400 como correcto, puede proporcionar un inicializador de telemetría que establezca la propiedad Éxito.

Si se proporciona un inicializador de telemetría, se llama cada vez que se llama a cualquiera de los métodos Track*(). Esto incluye los métodos `Track()` llamados por los módulos de telemetría estándar. Por convención, estos módulos no establecen ninguna propiedad que ya haya sido establecida por un inicializador. Se llama a los inicializadores de telemetría antes de llamar a los procesadores de telemetría. Por tanto, cualquier enriquecimiento realizado por los inicializadores es visible para los procesadores.

**Defina su inicializador**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**Aplicaciones ASP.NET: Carga del inicializador**

En ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternativamente,* se pueden crear instancias del inicializador en el código, por ejemplo en Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Obtenga más información de este ejemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**Aplicaciones de ASP.NET Core/ Servicio de trabajo: Carga del inicializador**

> [!NOTE]
> La adición de un inicializador mediante `ApplicationInsights.config` o `TelemetryConfiguration.Active` no es válida para las aplicaciones de ASP.NET Core o si usa el SDK de Microsoft.ApplicationInsights.WorkerService.

Para las aplicaciones escritas mediante [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) o [WorkerService](worker-service.md#adding-telemetryinitializers), la adición de un nuevo elemento `TelemetryInitializer` se realiza agregándolo al contenedor de inserción de dependencias, como se muestra a continuación. Esto se hace en el método `Startup.ConfigureServices`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicializadores de telemetría de Java

[Documentación del SDK de Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

A continuación, registre al inicializador personalizado en el archivo applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetría de JavaScript
*JavaScript*

Inserte un inicializador de telemetría inmediatamente después del código de inicialización que obtuvo del portal:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Para obtener un resumen de las propiedades no personalizadas disponibles en telemetryItem, consulte [Modelo de exportación de datos de Application Insights](../../azure-monitor/app/export-data-model.md).

Puede agregar tantos inicializadores como desee. A estos se les llama en el orden en que se agregan.

### <a name="example-telemetryinitializers"></a>TelemetryInitializers de ejemplo

#### <a name="add-custom-property"></a>Adición de una propiedad personalizada

El siguiente inicializador de ejemplo agrega una propiedad personalizada a cada una de las telemetrías de las que se ha realizado un seguimiento.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Adición de un nombre de rol en la nube

El siguiente inicializador de ejemplo establece un nombre de rol en la nube a cada una de las telemetrías de las que se ha realizado un seguimiento.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

¿Cuál es la diferencia entre los procesadores de telemetría y los inicializadores de telemetría?

* Existen algunas superposiciones en cuanto a lo que podemos hacer con ellos: ambos pueden usarse para agregar o modificar propiedades de telemetría, aunque se recomienda utilizar los inicializadores con dicho fin.
* Siempre se ejecutan los objetos TelemetryInitializer antes que los objetos TelemetryProcessor.
* Se puede llamar a TelemetryInitializers más de una vez. Por convención, no establecen ninguna propiedad que ya haya sido establecida.
* Los objetos TelemetryProcessor permiten reemplazar o descartar por completo un elemento de telemetría.
* Se garantiza que se llamará a todos los TelemetryInitializer registrados para cada elemento de telemetría. En el caso de los procesadores de telemetría, el SDK garantiza la llamada al primer procesador de telemetría. Independientemente de si se llama o no al resto de los procesadores, los procesadores de telemetría anteriores son los responsables de la decisión.
* Use TelemetryInitializers para enriquecer la telemetría con propiedades adicionales o invalide la existente. Use TelemetryProcessor para filtrar la telemetría.

## <a name="troubleshooting-applicationinsightsconfig"></a>Solución de problemas de ApplicationInsights.config.

* Compruebe que el nombre del tipo completo y el nombre del ensamblado sean correctos.
* Compruebe que el archivo applicationinsights.config esté en el directorio de salida y que contenga todos los cambios recientes.

## <a name="reference-docs"></a>Documentos de referencia

* [Información general acerca de la API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Referencia de ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código del SDK

* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Pasos siguientes
* [Búsqueda de eventos y registros](../../azure-monitor/app/diagnostic-search.md)
* [Muestreo](../../azure-monitor/app/sampling.md)
* [Solución de problemas](../../azure-monitor/app/troubleshoot-faq.md)
