---
title: Exploración de registros de seguimiento de .NET en Application Insights
description: Busque registros generados con Seguimiento, NLog o Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691162"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Exploración de los registros de seguimiento de .NET y .NET Core en Application Insights

Si usa ILogger, NLog, log4Net o System.Diagnostics.Trace para realizar el seguimiento de diagnósticos en la aplicación ASP.NET o ASP.NET Core, sus registros se pueden enviar a [Azure Application Insights][start], donde puede explorarlos y realizar búsquedas en ellos. Los registros se combinarán con los otros informes de telemetría procedente de su aplicación, y así podrá identificar los seguimientos asociados con el mantenimiento de cada solicitud de usuario y correlacionarlos con otros eventos e informes de excepciones.

> [!NOTE]
> ¿Necesita el módulo de captura de registros? Es un adaptador útil para registradores de terceros, pero si aún no está usando NLog, log4Net o System.Diagnostics.Trace, considere la posibilidad de llamar directamente a [Application Insights TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) .
>
>

## <a name="install-logging-on-your-app"></a>Instalación del registro en la aplicación
Instale el marco de registro elegido en su proyecto. Esto debería producir una entrada en app.config o web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configuración de Application Insights para recopilar registros
Si todavía no lo ha hecho, **[agregue Application Insights a su proyecto](../../azure-monitor/app/asp-net.md)**. Verá una opción para incluir el compilador de registros.

O **configure Application Insights** haciendo clic con el botón derecho en el proyecto en el Explorador de soluciones. Seleccione la opción de **Configurar recolección de seguimientos**.

*¿No aparece ningún menú de Application Insights ni una opción de compilador de registros?* Pruebe la [solución de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalación manual
Utilice este método si el tipo de proyecto no es compatible con el programa de instalación de Application Insights (por ejemplo, un proyecto de escritorio de Windows).

1. Si planea usar log4Net o NLog, instálelo en su proyecto.
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Busque "Application Insights"
4. Seleccione uno de los siguientes paquetes:

   - Para ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

El paquete NuGet instala los ensamblados necesarios y, si es aplicable, modifica el archivo web.config o app.config.

## <a name="ilogger"></a>ILogger

Para ver algunos ejemplos de uso de la implementación de ILogger para Application Insights con aplicaciones de consola y de ASP.NET Core, consulte este [artículo](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Insertar llamadas de registro de diagnóstico
Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Uso de eventos EventSource
Puede configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para enviarse a Application Insights como seguimientos. Primero, instale el paquete de NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Luego, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

En cada origen, puede establecer los siguientes parámetros:
 * `Name` especifica el nombre del evento EventSource que se recopilará.
 * `Level` especifica el nivel de registro que se recopilará. Puede ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` o `Warning`.
 * `Keywords` especifica el valor del entero de combinaciones de palabras clave que se usará (opcional).

## <a name="using-diagnosticsource-events"></a>Uso de eventos de DiagnosticSource
Puede configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para enviarlos a Application Insights como seguimientos. Primero, instale el paquete de NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Seguidamente, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada instancia de DiagnosticSource que quiera seguir, agregue una entrada con el atributo `Name` establecido con el nombre de la instancia DiagnosticSource.

## <a name="using-etw-events"></a>Uso de eventos ETW
Puede configurar eventos ETW para enviarse a Application Insights como seguimientos. Primero, instale el paquete de NuGet `Microsoft.ApplicationInsights.EtwCollector`. Luego, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Solo se pueden recopilar eventos ETW si el proceso que hospeda el SDK se ejecuta bajo una identidad que sea miembro de "Usuarios del registro de rendimiento" o los administradores.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

En cada origen, puede establecer los siguientes parámetros:
 * `ProviderName` es el nombre del proveedor ETW que se recopilará.
 * `ProviderGuid` especifica el GUID del proveedor ETW que se recopilará, que puede usarse en lugar de `ProviderName`.
 * `Level` establece el nivel de registro que se recopilará. Puede ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` o `Warning`.
 * `Keywords` establece el valor del entero de combinaciones de palabras clave que se usará (opcional).

## <a name="using-the-trace-api-directly"></a>Uso de la API de seguimiento directamente
Puede llamar directamente a la API de seguimiento de Application Insights. Los adaptadores de registro usan esta API.

Por ejemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, aquí podría codificar datos POST.

Además, puede agregar un nivel de gravedad al mensaje. Y, al igual que con otra telemetría, puede agregar valores de propiedad que puede usar para ayudar a filtrar o buscar distintos conjuntos de seguimientos. Por ejemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Esto permitiría filtrar fácilmente en [Búsqueda][diagnostic] todos los mensajes de un determinado nivel de gravedad relativos a una determinada base de datos.

## <a name="explore-your-logs"></a>Explorar los registros
Ejecute la aplicación, ya sea en modo de depuración o mediante su implementación para que esté activa.

En la hoja de información general de su aplicación del [portal de Application Insights][portal], elija [Buscar][diagnostic].

Por ejemplo, puede:

* Filtrar por seguimientos de registro, o por elementos con determinadas propiedades
* Inspeccionar un elemento específico en detalle
* Encontrar otros informes de telemetría relacionados con la misma solicitud de usuario (es decir, con el mismo OperationId)
* Guardar la configuración de esta página como favorita

> [!NOTE]
> **Muestreo.** Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Aprenda más sobre el muestreo.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Pasos siguientes
[Diagnóstico de errores y excepciones en ASP.NET][exceptions]

[Aprenda más sobre la búsqueda][diagnostic].

## <a name="troubleshooting"></a>solución de problemas
### <a name="how-do-i-do-this-for-java"></a>¿Cómo se puede hacer para Java?
Utilice los [adaptadores de registro de Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>No aparece la opción de Application Insights en el menú contextual del proyecto
* Compruebe que las herramientas de Application Insights estén instaladas en este equipo de desarrollo. En el menú Herramientas de Visual Studio, en Extensiones y actualizaciones, busque Herramientas de Application Insights. Si no se encuentran en la pestaña Instalado, abra la pestaña En línea e instálelas.
* Podría tratarse de un tipo de proyecto no admitido por las herramientas de Application Insights. Use la [instalación manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>No aparece la opción de adaptador en la herramienta de configuración
* Debe instalar primero el marco de registro.
* Si usa System.Diagnostics.Trace, asegúrese de que lo ha [configurado en `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* ¿Tiene la versión más reciente de Application Insights? En el menú **Herramientas** de Visual Studio, elija **Extensiones y actualizaciones** y abra la pestaña **Actualizaciones**. Si Developer Analytics Tools está instalado, haga clic para actualizarlo.

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".
Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Puedo ver seguimientos en Búsqueda de diagnóstico, pero no los otros eventos.
A veces, el paso de todos los eventos y solicitudes por la canalización puede llevar un rato.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?
Hay varios factores que afectan a la cantidad de datos que se conservan. Consulte la sección [Límites](../../azure-monitor/app/api-custom-events-metrics.md#limits) de la página de métricas de eventos de cliente para más información. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>No veo algunas de las entradas del registro que esperaba
Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Aprenda más sobre el muestreo.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Pasos siguientes
* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
