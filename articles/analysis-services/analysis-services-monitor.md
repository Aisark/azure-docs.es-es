---
title: Supervisión de las métricas del servidor de Azure Analysis Services | Microsoft Docs
description: Aprenda a supervisar las métricas del servidor de Analysis Services en Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065070"
---
# <a name="monitor-server-metrics"></a>Supervisión de las métricas del servidor

Analysis Services cuenta con unas métricas que le permiten supervisar el rendimiento y el estado de los servidores. Por ejemplo, puede supervisar la memoria y el uso de CPU, el número de conexiones de cliente y el consumo de recursos de consulta. Analysis Services utiliza el mismo marco de supervisión que la mayoría de los demás servicios de Azure. Para más información, consulte este artículo sobre las [métricas de Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para realizar diagnósticos más detallados, hacer un seguimiento del rendimiento e identificar tendencias en diferentes recursos de servicio de un grupo de recursos o de una suscripción, use [Azure Monitor](https://azure.microsoft.com/services/monitor/). Es posible que se apliquen cargos por el servicio Azure Monitor.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para supervisar las métricas de un servidor de Analysis Services

1. En Azure Portal, seleccione **Métricas**.

    ![Monitor en Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. En **Métricas disponibles**, seleccione las métricas que desee incluir en el gráfico. 

    ![Gráfico de Monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas del servidor

Utilice esta tabla para determinar qué métricas son más adecuadas para su escenario de supervisión. Las métricas deben utilizar la misma unidad para poder aparecer en el mismo gráfico.

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Longitud de cola de trabajos de grupo de comandos|Número|Media|Número de trabajos en la cola del grupo de subprocesos de comandos.|
|CurrentConnections|Conexión: Conexiones actuales|Número|Media|Número actual de conexiones de cliente establecidas.|
|CurrentUserSessions|Sesiones de usuario actuales|Número|Media|Número actual de sesiones de usuario establecidas.|
|mashup_engine_memory_metric|Memoria del motor M|Bytes|Media|Uso de memoria por los procesos del motor de mashup|
|mashup_engine_qpu_metric|QPU de motor M|Número|Media|Uso de QPU por los procesos del motor de mashup|
|memory_metric|Memoria|Bytes|Media|Memoria. Intervalo de 0-25 GB para S1, 0-50 GB para S2 y 0-100 GB para S4|
|memory_thrashing_metric|Paginación excesiva de memoria|Percent|Media|Paginación excesiva media de memoria.|
|CleanerCurrentPrice|Memoria: precio actual de limpieza|Número|Media|Precio actual de memoria, USD/byte/tiempo, normalizado a 1000.|
|CleanerMemoryNonshrinkable|Memoria: memoria de limpieza no reducible|Bytes|Media|Cantidad de memoria, en bytes, que no purgará el limpiador en segundo plano.|
|CleanerMemoryShrinkable|Memoria: memoria de limpieza reducible|Bytes|Media|Cantidad de memoria, en bytes, que purgará el limpiador en segundo plano.|
|MemoryLimitHard|Memoria: límite de memoria física|Bytes|Media|Límite de memoria física del archivo de configuración.|
|MemoryLimitHigh|Memoria: límite alto de memoria|Bytes|Media|Límite alto de memoria del archivo de configuración.|
|MemoryLimitLow|Memoria: límite bajo de memoria|Bytes|Media|Límite bajo de memoria del archivo de configuración.|
|MemoryLimitVertiPaq|Memoria: VertiPaq de límite de memoria|Bytes|Media|Límite en memoria del archivo de configuración.|
|MemoryUsage|Memoria: Uso de la memoria|Bytes|Media|Uso de memoria del proceso de servidor tal como se usa para calcular el precio de la memoria del limpiador. Es igual que el contador Process\PrivateBytes más el tamaño de los datos asignados a la memoria; no se tiene en cuenta ninguna memoria asignada por el sistema de análisis en memoria (VertiPaq) que supere el límite de memoria del sistema.|
|Quota|Memoria: Quota|Bytes|Media|Cuota de memoria actual, en bytes. La cuota de memoria también se denomina concesión de memoria o reserva de memoria.|
|QuotaBlocked|Memoria: cuota bloqueada|Número|Media|Número actual de solicitudes de cuota que están bloqueadas hasta que se liberen otras cuotas de memoria.|
|VertiPaqNonpaged|Memoria: VertiPaq no paginado|Bytes|Media|Bytes de memoria bloqueados en el espacio de trabajo para que los use el motor en memoria.|
|VertiPaqPaged|Memoria: VertiPaq paginado|Bytes|Media|Bytes de memoria paginada en uso para datos en memoria.|
|ProcessingPoolJobQueueLength|Longitud de cola de trabajos de grupo de procesamiento|Número|Media|Número de trabajos que no son de E/S en la cola del grupo de subprocesos de procesamiento.|
|RowsConvertedPerSec|Procesamiento: filas convertidas por segundo|CountPerSecond|Media|Velocidad de filas convertidas durante el procesamiento.|
|RowsReadPerSec|Procesamiento: filas leídas por segundo|CountPerSecond|Media|Velocidad de filas leídas de todas las bases de datos relacionales.|
|RowsWrittenPerSec|Procesamiento: filas escritas por segundo|CountPerSecond|Media|Velocidad de filas escritas durante el procesamiento.|
|qpu_metric|QPU|Número|Media|QPU. Intervalo de 0-100 para S1, 0-200 para S2 y 0-400 para S4|
|QueryPoolBusyThreads|Subprocesos ocupados de grupo de consultas|Número|Media|Número de subprocesos ocupados del grupo de subprocesos de consulta.|
|SuccessfullConnectionsPerSec|Conexiones correctas por segundo|CountPerSecond|Media|Tasa de finalizaciones de conexión correctas.|
|CommandPoolBusyThreads|Subprocesos: subprocesos ocupados del grupo de comandos|Número|Media|Número de subprocesos ocupados del grupo de subprocesos de comandos.|
|CommandPoolIdleThreads|Subprocesos: subprocesos inactivos del grupo de comandos|Número|Media|Número de subprocesos inactivos del grupo de subprocesos de comandos.|
|LongParsingBusyThreads|Subprocesos: subprocesos ocupados en análisis largos|Número|Media|Número de subprocesos ocupados del grupo de subprocesos de análisis largos.|
|LongParsingIdleThreads|Subprocesos: subprocesos inactivos en análisis largos|Número|Media|Número de subprocesos inactivos del grupo de subprocesos en análisis largos.|
|LongParsingJobQueueLength|Subprocesos: longitud de cola de trabajos en análisis largos|Número|Media|Número de trabajos en la cola del grupo de subprocesos en análisis largos.|
|ProcessingPoolIOJobQueueLength|Subprocesos: Longitud de cola de trabajos de E/S de grupo de procesamiento|Número|Media|Número de trabajos de E/S en la cola del grupo de subprocesos de procesamiento.|
|ProcessingPoolBusyIOJobThreads|Subprocesos: subprocesos de trabajo de E/S ocupados del grupo de procesamiento|Número|Media|Número de subprocesos que ejecutan trabajos de E/S en el grupo de subprocesos de procesamiento.|
|ProcessingPoolBusyNonIOThreads|Subprocesos: subprocesos de trabajo ocupados que no son de E/S del grupo de procesamiento|Número|Media|Número de subprocesos que ejecutan trabajos que no son de E/S del grupo de subprocesos de procesamiento.|
|ProcessingPoolIdleIOJobThreads|Subprocesos: subprocesos de trabajo de E/S inactivos del grupo de procesamiento|Número|Media|Número de subprocesos inactivos para trabajos de E/S en el grupo de subprocesos de procesamiento.|
|ProcessingPoolIdleNonIOThreads|Subprocesos: subprocesos de trabajo inactivos que no son de E/S del grupo de procesamiento|Número|Media|Número de subprocesos inactivos del grupo de subprocesos de procesamiento dedicado a trabajos que no son de E/S.|
|QueryPoolIdleThreads|Subprocesos: subprocesos inactivos del grupo de consultas|Número|Media|Número de subprocesos inactivos para trabajos de E/S en el grupo de subprocesos de procesamiento.|
|QueryPoolJobQueueLength|Subprocesos: longitud de cola de trabajos del grupo de consultas|Número|Media|Número de trabajos en la cola del grupo de subprocesos de consultas.|
|ShortParsingBusyThreads|Subprocesos: subprocesos ocupados en análisis cortos|Número|Media|Número de subprocesos ocupados del grupo de subprocesos de análisis cortos.|
|ShortParsingIdleThreads|Subprocesos: subprocesos inactivos en análisis cortos|Número|Media|Número de subprocesos inactivos del grupo de subprocesos de análisis cortos.|
|ShortParsingJobQueueLength|Subprocesos: longitud de cola de trabajos en análisis cortos|Número|Media|Número de trabajos en la cola del grupo de subprocesos en análisis cortos.|
|TotalConnectionFailures|Número total de errores de conexión|Número|Media|Número total de intentos de conexión con error.|
|TotalConnectionRequests|Número total de solicitudes de conexión|Número|Media|Número total de solicitudes de conexión. |

## <a name="next-steps"></a>Pasos siguientes
[Supervisión en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Métricas en Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Métricas de la API de REST de Azure Monitor](/rest/api/monitor/metrics)
