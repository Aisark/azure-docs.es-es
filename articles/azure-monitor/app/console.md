---
title: Azure Application Insights para aplicaciones de consola | Microsoft Docs
description: Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: c8c71fa3798b7c56550b742a8b19c83336bb6ddf
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889148"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights para aplicaciones de consola .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) permite supervisar la disponibilidad, el rendimiento y el uso de una aplicación web.

Necesita una suscripción a [Microsoft Azure](https://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, Xbox Live u otros servicios en la nube de Microsoft. Si su equipo tiene una suscripción organizativa a Azure, el propietario puede agregarle a esta con la cuenta de Microsoft.

> [!NOTE]
> Hay un nuevo SDK de Application Insights llamado [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) que se puede usar para habilitar Application Insights en cualquier aplicación de consola. Se recomienda usar este paquete y las instrucciones asociadas que se indican [aquí](../../azure-monitor/app/worker-service.md). Este paquete va dirigido a [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard) y, por lo tanto, se puede usar en .NET Core 2.0 o una versión posterior, y .NET Framework 4.7.2 o una versión posterior.

## <a name="getting-started"></a>Introducción

* En [Azure Portal](https://portal.azure.com), [cree un recurso de Application Insights](../../azure-monitor/app/create-new-resource.md). Para el tipo de aplicación, elija **General**.
* Realice una copia de la clave de instrumentación. Busque la clave en la lista desplegable **Essentials** del recurso que creó.
* Instale el paquete [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) más reciente.
* Establezca la clave de instrumentación en el código antes de hacer el seguimiento de cualquier telemetría (o establezca la variable de entorno APPINSIGHTS_INSTRUMENTATIONKEY). Después de eso, debe ser capaz de hacer un seguimiento manual de la telemetría y verla en Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Los datos de telemetría no se envían al instante. Los elementos de telemetría se procesan por lotes y se envían mediante el SDK de ApplicationInsights. En las aplicaciones de consola, que se cierran justo después de llamar a los métodos `Track()`, es posible que no se envíen los datos de telemetría a menos que `Flush()` y `Sleep` se completen antes de que se cierre la aplicación, tal como se muestra en el [ejemplo completo](#full-example) más adelante en este artículo.


* Instale la versión más reciente de [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector), que hace automáticamente un seguimiento de HTTP, SQL o algunas llamadas de dependencias externas.

Puede inicializar y configurar Application Insights desde el código o con el archivo `ApplicationInsights.config`. Asegúrese de que la inicialización se realice tan pronto como sea posible. 

> [!NOTE]
> Las instrucciones referentes a **ApplicationInsights.config** solo son aplicables a las aplicaciones que tienen como destino .NET Framework, y no se aplican a las aplicaciones de .NET Core.

### <a name="using-config-file"></a>Uso del archivo de configuración

De manera predeterminada, el SDK de Application Insights busca el archivo `ApplicationInsights.config` en el directorio de trabajo cuando se crea `TelemetryConfiguration`

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

También puede especificar la ruta de acceso al archivo de configuración.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para más información, consulte la [referencia del archivo de configuración](configuration-with-applicationinsights-config.md).

Puede obtener un ejemplo completo del archivo de configuración mediante la instalación de la versión más reciente del paquete [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Esta es la configuración **mínima** para la colección de dependencias que es equivalente al ejemplo de código.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configuración de la colección de telemetría a partir del código
> [!NOTE]
> La lectura del archivo de configuración no se admite en .NET Core. Puede plantearse la posibilidad de usar el [SDK de Application Insights para ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Durante el inicio de la aplicación, cree y configure la instancia `DependencyTrackingTelemetryModule`, que debe ser singleton y se debe conservar por toda la duración de la aplicación.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Agregue inicializadores de telemetría comunes

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Si ha creado la configuración con un constructor `TelemetryConfiguration()` sin formato, es preciso que también habilite la compatibilidad con la correlación. **No es necesario** si lee configuración de un archivo, se usan `TelemetryConfiguration.CreateDefault()` o `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* También puede instalar e inicializar el módulo de recopilador Contador de rendimiento, como se describe[aquí](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Ejemplo completo

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Pasos siguientes
* [Supervise dependencias](../../azure-monitor/app/asp-net-dependencies.md) para ver si REST, SQL u otros recursos externos se están ralentizando.
* [Use la API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación.
