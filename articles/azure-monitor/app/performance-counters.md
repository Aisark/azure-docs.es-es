---
title: Contadores de rendimiento en Application Insights | Microsoft Docs
description: Supervise los contadores de rendimiento de .NET, tanto del sistema como personalizados, en Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256989"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de rendimiento de sistema en Application Insights

Windows proporciona una amplia variedad de [contadores de rendimiento](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), como la ocupación de la CPU, memoria, disco y uso de la red. También puede definir sus propios contadores de rendimiento. si la aplicación se ejecuta en IIS en un host local o en una máquina virtual para la que tenga acceso administrativo.

## <a name="view-counters"></a>Visualización de contadores

El panel Métricas muestra el conjunto predeterminado de contadores de rendimiento.

![Contadores de rendimiento notificados en Application Insights](./media/performance-counters/performance-counters.png)

Los contadores predeterminados actuales que se recopilan para aplicaciones web de .NET son:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Para ver todos los gráficos más útiles en un solo lugar, cree un [panel](../../azure-monitor/app/app-insights-dashboards.md) y ánclelos a él.

## <a name="add-counters"></a>Adición de contadores

Si el contador de rendimiento que quiere no está incluido en la lista de métricas, puede agregarlo.

1. Averigüe qué contadores están disponibles en el servidor mediante este comando de PowerShell en el servidor local:
   
    `Get-Counter -ListSet *`
   
    (Consulte [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)).
2. Abra ApplicationInsights.config.
   
   * Si agrega Application Insights a la aplicación durante el desarrollo, edite ApplicationInsights.config en el proyecto y vuelva a implementarlo en los servidores.
   * Si utiliza Monitor de estado para instrumentar una aplicación web en tiempo de ejecución, busque ApplicationInsights.config en el directorio raíz de la aplicación en IIS. Actualícelo allí en cada instancia del servidor.
3. Edite la directiva del recopilador de rendimiento:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Puede capturar tanto los contadores estándar como los que ha implementado usted mismo. `\Objects\Processes` es un ejemplo de contador estándar que está disponible en todos los sistemas Windows. `\Sales(photo)\# Items Sold` es un ejemplo de contador personalizado que podría implementarse en un servicio web. 

El formato es `\Category(instance)\Counter"` o, para las categorías que no tienen instancias, simplemente `\Category\Counter`.

`ReportAs` es necesario para los nombres de contadores que no coinciden `[a-zA-Z()/-_ \.]+`: es decir, que contienen caracteres que no están en los siguientes conjuntos: letras, paréntesis, barra diagonal, guión, subrayado, espacio, punto.

Si especifica una instancia, se recopilará como una dimensión "CounterInstanceName" de la métrica notificada.

### <a name="collecting-performance-counters-in-code"></a>Recopilación de contadores de rendimiento en el código
Para recopilar contadores de rendimiento del sistema y enviarlos a Application Insights, puede adaptar el siguiente fragmento:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
También puede hacer lo mismo con las métricas personalizadas que haya creado:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contadores de rendimiento en Analytics
Puede buscar y mostrar informes de contador de rendimiento en [Analytics](../../azure-monitor/app/analytics.md).

El esquema **performanceCounters** expone `category`, el nombre de `counter` y el nombre de `instance` de cada contador de rendimiento.  En la telemetría de cada aplicación, solo se ven los contadores de dicha aplicación. Por ejemplo, para ver qué contadores están disponibles: 

![Contadores de rendimiento en Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(Aquí "Instance" hace referencia a la instancia de contador de rendimiento, no al rol ni a la instancia de máquina de servidor. El nombre de la instancia del contador de rendimiento normalmente segmenta contadores, como el tiempo de procesador por el nombre del proceso o la aplicación).

Para obtener un gráfico de la memoria disponible en un período reciente: 

![Gráfico de tiempo de la memoria in Application Insights Analytics](./media/performance-counters/analytics-available-memory.png)

Al igual que otros datos de telemetría, **performanceCounters** también tiene una columna `cloud_RoleInstance` que indica la identidad de la instancia del servidor host en el que se ejecuta la aplicación. Por ejemplo, para comparar el rendimiento de una aplicación en distintas máquinas: 

![Rendimiento segmentado por instancia de rol en Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Recuentos de ASP.NET y Application Insights
*¿En qué se diferencian la tasa de excepciones y las métricas de excepciones?*

* *tasa de excepciones* es un contador de rendimiento del sistema. El CLR cuenta todas las excepciones controladas y no controladas que se producen, y divide el total de un intervalo de muestreo entre la duración del intervalo. El SDK de Application Insights recopila este resultado y lo envía al portal.

* *Excepciones* es un recuento de los informes de TrackException recibidos a través del portal en el intervalo de muestreo del gráfico. Solo incluye las excepciones controladas para las que ha escrito llamadas a TrackException en el código y no incluye todas las [excepciones no controladas](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de rendimiento en aplicaciones de ASP.NET Core
Los contadores de rendimiento se admiten únicamente si la aplicación está dirigida a la versión completa de .NET Framework. No hay ninguna posibilidad de recopilar contadores de rendimiento para aplicaciones .NET Core.

## <a name="alerts"></a>Alertas
Al igual que otras métricas, puede [establecer una alerta](../../azure-monitor/app/alerts.md) para advertirle si un contador de rendimiento queda fuera de un límite especificado. Abra el panel de alertas y haga clic en Agregar alerta.

## <a name="next"></a>Pasos siguientes
* [Seguimiento de dependencias](../../azure-monitor/app/asp-net-dependencies.md)
* [Seguimiento de excepciones](../../azure-monitor/app/asp-net-exceptions.md)

