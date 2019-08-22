---
title: Monitor Azure Functions
description: Obtenga información acerca de cómo usar Azure Application Insights con Azure Functions para supervisar la ejecución de funciones.
services: functions
author: ggailey777
manager: jeconnoc
keywords: Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 582e4d81851d570f99d25d626a1db8a9f5e98231
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881326"
---
# <a name="monitor-azure-functions"></a>Monitor Azure Functions

[Azure Functions](functions-overview.md) ofrece integración incorporada con [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para supervisar las funciones. En este artículo se muestra cómo configurar Azure Functions para enviar archivos de registro generados por el sistema a Application Insights.

Se recomienda usar Application Insights porque recopila datos de errores, de rendimiento y de registros. Detecta automáticamente anomalías en el rendimiento e incluye herramientas de análisis eficaces que ayudan a diagnosticar problemas y comprender cómo se usan las funciones. Está diseñado para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso. Incluso puede usar Application Insights durante el desarrollo local de proyectos de aplicación de función. Para más información, consulte [¿Qué es Application Insights?](../azure-monitor/app/app-insights-overview.md)

Dado que la instrumentación de Application Insights necesaria está integrada en Azure Functions, todo lo que necesita es una clave de instrumentación válida para conectar la aplicación de función a un recurso de Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Precios y límites de Application Insights

Puede probar la integración de Application Insights con instancias de Function App de forma gratuita. Hay un límite diario para la cantidad de datos que se puede procesar de forma gratuita. Podría alcanzar este límite durante las pruebas. Azure proporciona notificaciones del portal y por correo electrónico cuando se aproxima al límite diario. Si pasa por alto esas alertas y llega al límite, los nuevos registros no aparecerán en las consultas de Application Insights. Tenga en cuenta el límite para evitar el tiempo que puede perder, de forma innecesaria, solucionando problemas. Para más información, consulte [Administración de precios y volúmenes de datos de Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Habilitación de la integración de Application Insights

Para que una aplicación de función envíe datos a Application Insights tiene que conocer la clave de instrumentación de un recurso de Application Insights. La clave tiene que estar en un valor de configuración de la aplicación llamado **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nueva aplicación de función en el portal

Cuando [crea la aplicación de función en Azure Portal](functions-create-first-azure-function.md), la integración de Application Insights está habilitada de forma predeterminada. El recurso de Application Insights tiene el mismo nombre que la aplicación de función, y se crea en la misma región o en la región más cercana.

Para revisar el recurso de Application Insights que se está creando, selecciónelo para expandir la ventana **Application Insights**. Puede cambiar el valor de **Nuevo nombre de recurso**  o elegir otro valor en **Ubicación** en la [ubicación geográfica de Azure](https://azure.microsoft.com/global-infrastructure/geographies/) donde quiera almacenar los datos.

![Habilitar Application Insights al crear una aplicación de función](media/functions-monitoring/enable-ai-new-function-app.png)

Cuando elige **Crear**, se crea un recurso de Application Insights con la aplicación de función, que tiene `APPINSIGHTS_INSTRUMENTATIONKEY` establecido en la configuración de la aplicación. Todo está listo para funcionar.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adición a una aplicación de función existente 

Cuando crea una aplicación de función con la [CLI de Azure](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md) o [Visual Studio Code](functions-create-first-function-vs-code.md), debe crear el recurso de Application Insights. Luego, puede agregar la clave de instrumentación desde ese recurso como configuración de aplicación en la aplicación de función.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Las primeras versiones de Functions usaban la supervisión integrada, que ya no se recomienda. Al habilitar la integración de Application Insights para una aplicación de función tal, también debe [deshabilitar el registro integrado](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Visualización de datos de telemetría en la pestaña Supervisar

Con la [integración de Application Insights habilitada](#enable-application-insights-integration), puede ver los datos de telemetría en la pestaña **Supervisar**.

1. En la página de la aplicación de función, seleccione una función que se haya ejecutado por lo menos una vez después de que se configurase Application Insights. Luego seleccione la pestaña **Supervisar**.

   ![Selección de la pestaña Supervisar](media/functions-monitoring/monitor-tab.png)

1. Seleccione **Actualizar** periódicamente hasta que aparezca la lista de las invocaciones de funciones.

   La lista puede tardar hasta cinco minutos en aparecer, mientras el cliente de telemetría agrupa los datos en lotes para su transmisión al servidor. (Este retraso no se aplica a [Live Metrics Stream](../azure-monitor/app/live-stream.md). Este servicio se conecta al host de Functions cuando se carga la página, por lo que los registros se transmiten directamente a la página).

   ![Lista de invocaciones](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver los registros de una invocación de función determinada, seleccione el vínculo de la columna **Fecha** para esa invocación.

   ![Vínculo de detalles de la invocación](media/functions-monitoring/invocation-details-link-ai.png)

   La salida del registro para esa invocación aparece en una nueva página.

   ![Detalles de la invocación](media/functions-monitoring/invocation-details-ai.png)

Puede ver que ambas páginas tienen un vínculo **Ejecutar en Application Insights** a la consulta de Análisis de Application Insights que recupera los datos.

![Ejecución en Application Insights](media/functions-monitoring/run-in-ai.png)

Se muestra la siguiente consulta. Puede ver que la lista de invocación se limita a los últimos 30 días. La lista no muestra más de 20 filas (`where timestamp > ago(30d) | take 20`). La lista de detalles de invocación es para los últimos 30 días sin límite.

![Lista de invocación del análisis de Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Para más información, consulte la sección [Consultar datos de telemetría](#query-telemetry-data) que se encuentra más adelante en este artículo.

## <a name="view-telemetry-in-application-insights"></a>Visualización de la telemetría en Application Insights

Para abrir Application Insights desde una aplicación de función en Azure Portal, vaya a la página **Información general** de la aplicación de función. En **Características configuradas**, seleccione **Application Insights**.

![Abra Application Insights desde la página Información general de la aplicación de función](media/functions-monitoring/ai-link.png)

Para obtener más información acerca de Application Insights, consulte [Documentación de Application Insights](https://docs.microsoft.com/azure/application-insights/). En esta sección se muestran algunos ejemplos de cómo ver los datos de Application Insights. Si ya está familiarizado con Application Insights, puede ir directamente a [las secciones sobre cómo configurar y personalizar los datos de telemetría](#configure-categories-and-log-levels).

![Pestaña Información general de Application Insights](media/functions-monitoring/metrics-explorer.png)

Las siguientes áreas de Application Insights pueden ser útiles al evaluar el comportamiento, el rendimiento y los errores de las funciones:

| Tabulador | DESCRIPCIÓN |
| ---- | ----------- |
| **[Errores](../azure-monitor/app/asp-net-exceptions.md)** |  Cree alertas y gráficos basados en errores de funciones y excepciones del servidor. **Nombre de la operación** es el nombre de la función. Los errores en las dependencias no se muestran, a no ser que se implemente telemetría personalizada para las dependencias. |
| **[Rendimiento](../azure-monitor/app/performance-counters.md)** | Analice los problemas de rendimiento. |
| **Servidores** | Consulte la utilización de recursos y el rendimiento por servidor. Estos datos pueden ser útiles para la depuración de escenarios en los que las funciones están dificultando los recursos subyacentes. Los servidores se conocen como **Instancias de rol en la nube**. |
| **[Métricas](../azure-monitor/app/metrics-explorer.md)** | Cree gráficos y alertas basados en métricas. Las métricas incluyen el número de invocaciones de función, el tiempo de ejecución y las tasas de éxito. |
| **[Secuencia de métricas en directo](../azure-monitor/app/live-stream.md)** | Vea los datos de métricas a medida que se crean en tiempo real. |

## <a name="query-telemetry-data"></a>Consultar datos de telemetría

El [Análisis de Application Insights](../azure-monitor/app/analytics.md) proporciona acceso a todos los datos de telemetría en forma de tabla en una base de datos. Analytics ofrece un lenguaje de consulta para extraer, manipular y visualizar los datos.

![Seleccione Analytics](media/functions-monitoring/select-analytics.png)

![Ejemplo de Analytics](media/functions-monitoring/analytics-traces.png)

Este es un ejemplo de consulta que muestra la distribución de solicitudes por trabajador durante los últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Las tablas que están disponibles se muestran en la pestaña **Esquema** de la izquierda. Encontrará los datos generados por las invocaciones de función en las tablas siguientes:

| Tabla | DESCRIPCIÓN |
| ----- | ----------- |
| **traces** | Registros creados por el tiempo de ejecución y por el código de la función. |
| **requests** | Una solicitud por cada invocación de función. |
| **exceptions** | Cualquier excepción que produzca el tiempo de ejecución. |
| **customMetrics** | Recuento de invocaciones correctas y erróneas, tasa de éxito y duración. |
| **customEvents** | Eventos registrados por el tiempo de ejecución, por ejemplo: solicitudes HTTP que desencadenan una función. |
| **performanceCounters** | Información acerca del rendimiento de los servidores en los que se ejecutan las funciones. |

El resto de las tablas son para pruebas de disponibilidad, y telemetría de explorador y de cliente. Puede implementar telemetría personalizada para agregarles datos.

Dentro de cada tabla, algunos de los datos específicos de Functions están en un campo `customDimensions`.  Por ejemplo, la consulta siguiente recupera todos los seguimientos que tienen el nivel de registro `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

El tiempo de ejecución proporciona los campos `customDimensions.LogLevel` y `customDimensions.Category`. Puede proporcionar campos adicionales en los registros que escriba en el código de función. Consulte [Registro estructurado](#structured-logging) más adelante en este artículo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorías y niveles de registro

Puede usar Application Insights sin ninguna configuración personalizada. La configuración predeterminada puede dar lugar a grandes volúmenes de datos. Si usa una suscripción de Visual Studio Azure, es posible que alcance su límite de datos para Application Insights. Más adelante en este artículo verá cómo configurar y personalizar los datos que envían las funciones a Application Insights. Para una aplicación de función, el registro se configura en el archivo [host.json].

### <a name="categories"></a>Categorías

El registrador de Azure Functions incluye una *categoría* para cada registro. La categoría indica qué parte del código de tiempo de ejecución o del código de la función escribió el registro. 

El tiempo de ejecución de Functions crea registros con una categoría que empieza con "Host". En la versión 1.x, los registros `function started`, `function executed` y `function completed` tienen la categoría `Host.Executor`. A partir de la versión 2.x, estos registros tienen la categoría `Function.<YOUR_FUNCTION_NAME>`.

Si escribe registros en el código de la función, la categoría es `Function` en la versión 1.x del entorno de ejecución de Functions. En la versión 2.x, la categoría es `Function.<YOUR_FUNCTION_NAME>.User`.

### <a name="log-levels"></a>Niveles de registro

El registrador de Azure Functions también incluye un *nivel de registro* con cada registro. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) es una enumeración y el código entero indica la importancia relativa:

|LogLevel    |Código|
|------------|---|
|Seguimiento       | 0 |
|Depurar       | 1 |
|Information | 2 |
|Advertencia     | 3 |
|Error       | 4 |
|Crítico    | 5 |
|None        | 6 |

El nivel de registro `None` se explica en la sección siguiente. 

### <a name="log-configuration-in-hostjson"></a>Configuración del registro en host.json

El archivo [host.json] configura la cantidad de registro que envía una aplicación de la función a Application Insights. Para cada categoría, debe indicar el nivel de registro mínimo para enviar. Hay dos ejemplos: el primero tiene como destino el [tiempo de ejecución de Functions versión 2.x](functions-versions.md#version-2x) (.NET Core); y el segundo, el tiempo de ejecución de la versión 1.x.

### <a name="version-2x"></a>Versión 2.x

El entorno de ejecución de v2.x emplea ahora la [jerarquía de filtro de registro de .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versión 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

En el ejemplo siguiente se configuran las reglas siguientes:

* Para los registros con la categoría `Host.Results` o `Function`, envíe solo el nivel `Error` y superior a Application Insights. Los registros del nivel `Warning` e inferiores se omiten.
* Para los registros con la categoría `Host.Aggregator`, envíe todos los registros a Application Insights. El nivel de registro `Trace` es el mismo que algunos registradores llaman `Verbose`, pero usa `Trace` en el archivo [host.json].
* Para el resto de registros, envíe solo el nivel `Information` y superior a Application Insights.

El valor de categoría de [host.json] controla el registro de todas las categorías que comienzan con el mismo valor. `Host` en [host.json] controla el registro de `Host.General`, `Host.Executor`, `Host.Results` y así sucesivamente.

Si [host.json] incluye varias categorías que comienzan con la misma cadena, las más largas se asignan primero. Imagine que quiere que todo el tiempo de ejecución excepto `Host.Aggregator` se registre en el nivel `Error`, pero que `Host.Aggregator` lo haga en el nivel `Information`:

### <a name="version-2x"></a>Versión 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versión 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para eliminar todos los registros de una categoría, puede usar el nivel de registro `None`. No hay ningún registro escrito con esa categoría y no existe ningún nivel de registro por encima de él.

En las secciones siguientes se describen las categorías principales de registros que crea el tiempo de ejecución. 

### <a name="category-hostresults"></a>Categoría Host.Results

Estos registros se muestran como "requests" en Application Insights, e indican el éxito o fracaso de una función.

![Gráfico de solicitudes](media/functions-monitoring/requests-chart.png)

Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos.

### <a name="category-hostaggregator"></a>Categoría Host.Aggregator

Estos registros proporcionan los recuentos y promedios de las invocaciones de función en un período de tiempo [configurable](#configure-the-aggregator). El período predeterminado es 30 segundos o 1000 resultados, lo que ocurra primero. 

Los registros están disponibles en la tabla **customMetrics** de Application Insights. Algunos ejemplos son el número de ejecuciones, la tasa de éxito y la duración.

![Consulta customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos.

### <a name="other-categories"></a>Otras categorías

Todos los registros de categorías diferentes a las que ya se muestran están disponibles en la tabla **traces** en Application Insights.

![Consulta traces](media/functions-monitoring/analytics-traces.png)

Todos los registros con categorías que comienzan por `Host` los escribe el tiempo de ejecución de Functions. Los registros "Función iniciada" y "Función concluida" tienen la categoría `Host.Executor`. Para las ejecuciones correctas, estos registros son de nivel `Information`. Las excepciones se registran en el nivel `Error`. El tiempo de ejecución también crea registros del nivel `Warning`; por ejemplo, poner en cola los mensajes enviados a la cola de dudosos.

Los registros escritos por el código de función tienen la categoría `Function` y pueden tener cualquier nivel de registro.

## <a name="configure-the-aggregator"></a>Configurar el agregador

Como se indicó en la sección anterior, el tiempo de ejecución agrega datos acerca de las ejecuciones de la función durante un período de tiempo. El período predeterminado es 30 segundos o 1000 ejecuciones, lo que ocurra primero. Puede configurar este valor en el archivo [host.json].  Este es un ejemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar el muestreo

Application Insights tiene una característica de [muestreo](../azure-monitor/app/sampling.md) que le puede ayudar a impedir que se recopilen demasiados datos de telemetría sobre las ejecuciones completadas en los momentos de picos de carga. Cuando tasa de ejecuciones entrantes supera un umbral especificado, Application Insights empieza a omitir aleatoriamente algunas de las ejecuciones entrantes. La configuración predeterminada para el número máximo de ejecuciones por segundo es de 20 (cinco en la versión 1.x). Puede configurar el muestreo en [host.json].  Este es un ejemplo:

### <a name="version-2x"></a>Versión 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versión 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> El [muestreo](../azure-monitor/app/sampling.md) está habilitado de forma predeterminada. Si parece que faltan datos, es posible que simplemente deba ajustar la configuración de muestreo para adaptarla a su escenario de supervisión en particular.

## <a name="write-logs-in-c-functions"></a>Escribir registros en funciones de C#

Puede escribir registros en el código de función que aparecen como seguimientos de Application Insights.

### <a name="ilogger"></a>ILogger

Use un parámetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) en las funciones en lugar de un parámetro `TraceWriter`. Los registros creados con `TraceWriter` se dirigen a Application Insights, pero `ILogger` le permite hacer un [registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Con un objeto `ILogger`, llamará al `Log<level>` [métodos de extensión en ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para crear registros. El código siguiente escribe registros `Information` con la categoría "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro estructurado

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan en el mensaje del registro. Supongamos que tiene el siguiente código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Si mantiene la misma cadena de mensaje e invierte el orden de los parámetros, el texto del mensaje resultante tendrá los valores en los lugares incorrectos.

Los marcadores de posición se controlan de esta forma para que pueda hacer el registro estructurado. Application Insights almacena los pares de nombre-valor del parámetro y la cadena de mensaje. Como resultado, los argumentos del mensaje se convierten en campos que puede consultar.

Si la llamada de método del registrador tiene un aspecto similar al ejemplo anterior, puede consultar el campo `customDimensions.prop__rowKey`. Se agrega el prefijo `prop__` para asegurar que no haya ninguna colisión entre los campos que agrega el tiempo de ejecución y los campos que agrega el código de la función.

También puede consultar la cadena del mensaje original haciendo referencia al campo `customDimensions.prop__{OriginalFormat}`.  

Esta es una representación de JSON de ejemplo de los datos `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registro de métricas personalizadas

En funciones de secuencia de comandos de C#, puede usar el método de extensión `LogMetric` en `ILogger` para crear métricas personalizadas en Application Insights. Este es un ejemplo de una llamada de método:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Este código es una alternativa a llamar a `TrackMetric` con la API de Application Insights para .NET.

## <a name="write-logs-in-javascript-functions"></a>Escribir registros en las funciones de JavaScript

En las funciones de Node.js, use `context.log` para escribir los registros. El registro estructurado no está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registro de métricas personalizadas

Cuando ejecuta la [versión 1.x](functions-versions.md#creating-1x-apps) del sistema en tiempo de ejecución de Functions, las funciones de Node.js pueden usar el método `context.log.metric` para crear métricas personalizadas en Application Insights. Este método no se admite actualmente en la versión 2.x. Este es un ejemplo de una llamada de método:

```javascript
context.log.metric("TestMetric", 1234);
```

Este código es una alternativa a llamar a `trackMetric` con el SDK de Node.js de Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Registrar telemetría personalizada en funciones de C#

Puede usar el paquete NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar datos de telemetría personalizada a Application Insights. En el siguiente ejemplo de C# se usa la [API de telemetría personalizada](../azure-monitor/app/api-custom-events-metrics.md). El ejemplo es para una biblioteca de clases. NET, pero el código de Application Insights es el mismo para la secuencia de comandos de C#.

### <a name="version-2x"></a>Versión 2.x

El entorno de ejecución de la versión 2.x usa características más recientes de Application Insights para correlacionar automáticamente la telemetría con la operación actual. No es necesario establecer manualmente los campos de operación `Id`, `ParentId` o `Name`.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versión 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

No llame a `TrackRequest` ni a `StartOperation<RequestTelemetry>`, ya que verá solicitudes duplicadas de invocaciones de función.  El tiempo de ejecución de Functions realiza un seguimiento automático de las solicitudes.

No establezca `telemetryClient.Context.Operation.Id`. Esta configuración global provoca una correlación incorrecta cuando muchas funciones se ejecuten de manera simultánea. En su lugar, cree una instancia de telemetría nueva (`DependencyTelemetry`, `EventTelemetry`) y modifique su propiedad `Context`. Luego, pase la instancia de telemetría al método `Track` correspondiente en `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Este método garantiza que la telemetría tenga los detalles de correlación correctos para la invocación actual de la función.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registrar telemetría personalizada en funciones de JavaScript

A continuación, se muestra un fragmento de código de ejemplo que envía telemetría personalizada con el [SDK de Node.js para Application Insights](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

El parámetro `tagOverrides` establece `operation_Id` en el identificador de invocación de la función. Esta configuración le permite correlacionar toda la telemetría personalizada y generada automáticamente para una determinada invocación de función.

## <a name="dependencies"></a>Dependencias

Functions v2 recopila automáticamente las dependencias de solicitudes HTTP, ServiceBus y SQL.

Puede escribir código personalizado para mostrar las dependencias. Para ver ejemplos, consulte el código de ejemplo en la [sección de telemetría personalizada de C#](#log-custom-telemetry-in-c-functions). El código de ejemplo da como resultado un *mapa de aplicación* de Application Insights, que tiene un aspecto como el de la siguiente imagen:

![Mapa de aplicación](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Informar de problemas

Para notificar un problema con la integración de Application Insights en Functions o para realizar una solicitud o sugerencia [cree un problema en GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Registros de transmisión

A menudo, cuando desarrolla una aplicación, quiere ver lo que se escribe en los registros casi en tiempo real cuando se ejecuta en Azure.

Hay dos maneras de ver una secuencia de los archivos de registro que generan las ejecuciones de la función.

* **Streaming integrado de registros**: la plataforma de App Service le permite ver una secuencia de los archivos de registro de aplicaciones. Esto equivale a la salida que se ve al depurar las funciones durante el [desarrollo local](functions-develop-local.md) y cuando se usa la pestaña **Prueba** del portal. Se muestra toda la información basada en el registro. Para más información, vea [How to: Stream logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs) (Cómo: Transmitir registros). Este método de streaming solo admite una instancia y no se puede usar con una aplicación que se ejecuta en Linux en un plan de consumo.

* **Live Metrics Stream**: cuando la aplicación de funciones está [conectada a Application Insights](#enable-application-insights-integration), puede ver los datos de registro y otras métricas casi en tiempo real en Azure Portal mediante [Live Metrics Stream](../azure-monitor/app/live-stream.md). Use este método cuando supervise las funciones que se ejecutan en varias instancias o en Linux en un plan de consumo. En este método se usan [datos muestreados](#configure-sampling).

Las secuencias de registro se pueden ver tanto en el portal como en la mayoría de los entornos de desarrollo locales. 

### <a name="portal"></a>Portal

Puede ver ambos tipos de secuencias de registro en el portal.

#### <a name="built-in-log-streaming"></a>Streaming integrado de registros

Para ver los registros de secuencias en el portal, seleccione la pestaña **Características de la plataforma** en la aplicación de función. Luego, en **Supervisión**, elija **Secuencias de registro**.

![Habilitar los registros de secuencias en el portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Esto conecta la aplicación con el servicio de secuencias de registro, y los registros de aplicación se muestran en la ventana. Puede alternar entre **Registros de aplicaciones** y **Registros de servidor web**.  

![Ver los registros de secuencias en el portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Secuencia de métricas en directo

Para ver Live Metrics Stream de su aplicación, seleccione la pestaña **Información general** de la aplicación de funciones. Cuando haya habilitado Application Insights, verá un vínculo a **Application Insights** en **Características configuradas**. Este vínculo lo llevará a la página Application Insights correspondiente a la aplicación.

En Application Insights, seleccione **Live Metrics Stream**. Las [entradas de los registros muestreados](#configure-sampling) se muestran en **Telemetría de ejemplo**.

![Vista de Live Metrics Stream en el portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>CLI de Azure

Puede habilitar los registros de secuencias con la [CLI de Azure](/cli/azure/install-azure-cli). Use los siguientes comandos para iniciar sesión, elija su suscripción y transmita los archivos de registro:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Puede habilitar los registros de secuencias con [Azure PowerShell](/powershell/azure/overview). Para PowerShell, use los siguientes comandos para agregar su cuenta de Azure, elegir su suscripción y transmitir los archivos de registro:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Deshabilitar el registro integrado

Cuando habilite Application Insights, deshabilite el registro integrado que usa Azure Storage. El registro integrado es útil para realizar pruebas con cargas de trabajo ligeras, pero no está diseñado para su uso en producción de alta carga. Para la supervisión de producción, se recomienda Application Insights. Si el registro integrado se usa en producción, el registro resultante podría estar incompleto debido a la limitación de Azure Storage.

Para deshabilitar el registro integrado, elimine la configuración de la aplicación `AzureWebJobsDashboard`. Para obtener información acerca de cómo eliminar la configuración de la aplicación en Azure Portal, consulte la sección **Application settings** (Configuración de la aplicación) en [How to manage a function app](functions-how-to-use-azure-function-app-settings.md#settings) (Cómo administrar una aplicación de función). Antes de eliminar el valor de configuración de la aplicación, asegúrese de que no haya ninguna función existente en la misma aplicación de función que utilice dicho valor para los desencadenadores o enlaces de Azure Storage.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Application Insights](/azure/application-insights/)
* [Registro de ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
