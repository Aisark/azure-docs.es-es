---
title: Introducción a Azure Application Insights Agent | Microsoft Docs
description: Introducción a Application Insights Agent. Supervise el rendimiento de los sitios web sin volver a implementarlos. Funciona con las aplicaciones web de ASP.NET hospedadas en local, en las máquinas virtuales o en Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671178"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implementación de Azure Application Insights Agent para servidores locales

> [!IMPORTANT]
> Esta guía se recomienda para implementaciones de Application Insights Agent en la nube locales y en el entorno local que no sean de Azure. Este es el enfoque recomendado para [las implementaciones de máquinas virtuales y de conjuntos de escalado de máquinas virtuales de Azure](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (anteriormente conocido como Monitor de estado V2) es un módulo de PowerShell publicado en la [Galería de PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Reemplaza al [Monitor de estado](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Se envía telemetría a Azure Portal, donde puede [supervisar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) la aplicación.

> [!NOTE]
> El módulo solo admite actualmente la instrumentación sin código de aplicaciones web .NET hospedadas con IIS. Use un SDK para instrumentar las aplicaciones ASP.NET Core, Java y Node.js.

## <a name="powershell-gallery"></a>Galería de PowerShell

Application Insights Agent se encuentra aquí: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![Galería de PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Consulte las [instrucciones de introducción](status-monitor-v2-get-started.md) para empezar con ejemplos concisos de código.
- Consulte las [instrucciones detalladas](status-monitor-v2-detailed-instructions.md) para profundizar.

## <a name="powershell-api-reference"></a>Referencia de la API de PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Solución de problemas
- [Solución de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conocidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Preguntas más frecuentes

- ¿Application Insights Agent admite instalaciones de proxy?

  *Sí*. Hay varias maneras de descargar Application Insights Agent. Si el equipo tiene acceso a internet, puede incorporarlo en la Galería de PowerShell mediante los parámetros `-Proxy`.
Puede descargar manualmente el módulo e instalarlo en el equipo o usarlo directamente.
Cada una de estas opciones se describe en las [instrucciones detalladas](status-monitor-v2-detailed-instructions.md).

- ¿Admite el Monitor de estado v2 aplicaciones ASP.NET Core?

  *No*. Para instrucciones sobre cómo habilitar la supervisión de aplicaciones ASP.NET Core, consulte [Application Insights para aplicaciones ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). No es necesario instalar el Monitor de estado para una aplicación ASP.NET Core. Esto es así incluso si la aplicación ASP.NET Core se hospeda en IIS.

- ¿Cómo se puede comprobar que la habilitación se realizó correctamente?

  - Se puede usar el cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) para comprobar que la habilitación se ha realizado correctamente.
  - Se recomienda usar [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rápidamente si la aplicación envía telemetría.

  - También puede usar [Log Analytics](../log-query/get-started-portal.md) para enumerar todos los roles en la nube que están enviando actualmente telemetría:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Pasos siguientes

Vea la telemetría:

* [Explore las métricas](../../azure-monitor/app/metrics-explorer.md) para supervisar el rendimiento y el uso.
* [Busque en los eventos y los registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use Analytics](../../azure-monitor/app/analytics.md) para consultas más avanzadas.
* [Cree paneles](../../azure-monitor/app/overview-dashboard.md).

Agregue más telemetría:

* [Cree pruebas web](monitor-web-app-availability.md) para asegurarse de que el sitio permanece activo.
* [Agregue telemetría de cliente web](../../azure-monitor/app/javascript.md) para ver las excepciones de código de la página web y para habilitar las llamadas de seguimiento.
* [Agregue el SDK de Application Insights al código](../../azure-monitor/app/asp-net.md) para que pueda insertar llamadas de seguimiento y registro.

