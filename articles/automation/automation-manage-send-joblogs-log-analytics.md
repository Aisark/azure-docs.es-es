---
title: Reenvío de datos de un trabajo de Azure Automation a registros de Azure Monitor
description: En este artículo se muestra cómo enviar el estado de un trabajo y las transmisiones de trabajos de runbook a registros de Azure Monitor para ofrecer información y administración adicionales.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2433e8193026b8aaa3cbf29eb1411c7449a4953
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849741"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Reenvío del estado de un trabajo y de transmisiones de trabajos de Automation a registros de Azure Monitor

Automation puede enviar el estado de un trabajo del runbook y de transmisiones de trabajos al área de trabajo de Log Analytics. Este proceso no implica vincular el área de trabajo y es completamente independiente. Los registros de trabajo y flujos de trabajo están visibles en Azure Portal, o con PowerShell, para los trabajos individuales y esto permite llevar a cabo investigaciones simples. Ahora con los registros de Azure Monitor, puede:

* Obtener información sobre los trabajos de Automation.
* Desencadenar un correo electrónico o una alerta en función del estado de trabajo del runbook (por ejemplo, error o en suspensión).
* Escribir consultas avanzadas en las transmisiones de trabajos.
* Correlacionar trabajos en cuentas de Automation.
* Visualizar el historial de trabajos a lo largo del tiempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Requisitos previos y consideraciones de implementación

Para empezar a enviar los registros de Automation a registros de Azure Monitor, necesita:

* La versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un área de trabajo de Log Analytics. Para más información, consulte [Introducción a Azure Monitor](../log-analytics/log-analytics-get-started.md).
* El valor de ResourceId de su cuenta de Azure Automation.

Para encontrar el valor de ResourceId de su cuenta de Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para encontrar el valor de ResourceId del área de trabajo de Log Analytics, ejecute el código PowerShell siguiente:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si tiene varias cuentas de Automation, o áreas de trabajo, en la salida de los comandos anteriores, busque el *nombre* que tiene que configurar y copie el valor para *ResourceId*.

Si necesita encontrar el *nombre* de la cuenta de Automation, en Azure Portal seleccione su cuenta de Automation en la hoja **Cuenta de Automation** y seleccione **Toda la configuración**. En la hoja **Toda la configuración**, en **Configuración de la cuenta**, seleccione **Propiedades**.  En la hoja **Propiedades**, puede anotar estos valores.<br> ![Propiedades de una cuenta de Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configuración de la integración con registros de Azure Monitor

1. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio**.
2. Ejecute el siguiente código PowerShell y edite el valor de `[your resource id]` y `[resource id of the log analytics workspace]` con los valores del paso anterior.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Después de ejecutar este script, puede que tarde una hora antes de empezar a ver los registros de Azure Monitor de los nuevos JobLogs o JobStreams que se están escribiendo.

Para ver los registros, ejecute la siguiente consulta en la búsqueda de registros de Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`.

### <a name="verify-configuration"></a>Comprobación de la configuración

Para confirmar que la cuenta de Automation está enviando registros a su área de trabajo de Log Analytics, compruebe que los diagnósticos están configurados correctamente en la cuenta de Automation con el código PowerShell siguiente:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

En la salida asegúrese de que:

* En *Registros*, el valor de *Habilitado* es *True*.
* El valor de *WorkspaceId* se establece en el valor de ResourceId de su área de trabajo de Log Analytics.

## <a name="azure-monitor-log-records"></a>Registros de Azure Monitor

Diagnósticos de Azure Automation crea dos tipos de registros en Azure Monitor y se etiquetan como **AzureDiagnostics**. Las consultas siguientes utilizan el lenguaje de consulta actualizado en registros de Azure Monitor. Para más información acerca de las consultas comunes entre lenguaje de consulta heredado y el nuevo lenguaje de consulta de Azure Kusto, visite [Legacy to new Azure Kusto Query Language cheat sheet](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) (Hoja de características clave de lenguaje de consulta heredado a lenguaje de consulta nuevo de Azure Kusto).

### <a name="job-logs"></a>Registros de trabajo

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación. Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook. Los valores posibles son:<br>- New<br>- Created<br>Started<br>Stopped<br>Suspended<br>Con error<br>Completado |
| Category | Clasificación del tipo de datos. Para Automation, el valor será JobLogs. |
| OperationName | Especifica el tipo de operación realizada en Azure. En Automation, el valor es Job. |
| Resource | Nombre de la cuenta de Automation |
| SourceSystem | Cómo los registros de Azure Monitor recopilan los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Describe el estado de resultado del trabajo de Runbook. Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flujos de trabajo
| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| RunbookName_s |El nombre del Runbook. |
| Caller_s |La persona que inicia la operación. Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| StreamType_s |El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado |
| Tenant_g | GUID que identifica al inquilino para el llamador. |
| JobId_g |GUID que es el identificador del trabajo de Runbook. |
| ResultType |El estado del trabajo de Runbook. Los valores posibles son:<br>- In Progress |
| Category | Clasificación del tipo de datos. Para Automation, el valor es JobStreams. |
| OperationName | Especifica el tipo de operación realizada en Azure. En Automation, el valor es Job. |
| Resource | Nombre de la cuenta de Automation |
| SourceSystem | Cómo los registros de Azure Monitor recopilan los datos. Siempre *Azure* para Diagnósticos de Azure. |
| ResultDescription |Incluye la secuencia de salida del Runbook. |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| ResourceId |Especifica el identificador de recursos de la cuenta de Azure Automation del runbook. |
| SubscriptionId | El identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | Nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualización de registros de Automation en los registros de Azure Monitor

Ahora que ha iniciado el envío de los registros de los trabajos de Automation a los registros de Azure Monitor, veamos lo que puede hacer con dichos registros en los registros de Azure Monitor.

Para ver los registros, ejecute la consulta siguiente: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envío de un correo electrónico cuando se produce un error en un trabajo del runbook o se suspende
Uno de los principales clientes pregunta por la capacidad para enviar un correo electrónico o un mensaje de texto cuando aparece algún problema en un trabajo del runbook.

Para crear una regla de alertas, lo primero es crear una búsqueda de registros de los registros del trabajo del runbook que deben invocar la alerta. Haga clic en el botón **Alerta** para crear y configurar la regla de alerta.

1. En la página de información general del área de trabajo de Log Analytics, haga clic en **Ver registros**.
2. Cree una consulta de búsqueda de registros para la alerta; para ello, escriba la siguiente búsqueda en el campo de la consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  También puede agrupar por RunbookName, para lo que debe usar: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Si configura registros de más de una cuenta de Automation o suscripción a su área de trabajo, puede agrupar las alertas por suscripción o cuenta de Automation. El nombre de la cuenta de Automation puede encontrarse en el campo Recurso en la búsqueda de JobLogs.
3. Para abrir la pantalla **Crear regla**, haga clic en **+ Nueva regla de alertas** en la parte superior de la página. Para más información sobre las opciones para configurar la alerta, consulte [Alertas de registro en Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Búsqueda de todos los trabajos que se han completado con errores
Además de las alertas al producirse errores, puede saber cuando un trabajo de runbook tiene un error de no terminación. En estos casos, PowerShell genera un flujo de errores, pero los errores de no terminación no causan la suspensión ni producirán un error en el trabajo.

1. En el área de trabajo de Log Analytics, haga clic en **Registros**.
2. En el campo de consulta, escriba `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` y luego haga clic en el botón **Buscar**.

### <a name="view-job-streams-for-a-job"></a>Visualización de las transmisiones de un trabajo
Cuando se depura un trabajo, también se pueden examinar sus transmisiones. La siguiente consulta muestra todas las transmisiones de un solo trabajo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualización del estado del historial de trabajos
Por último, puede desear visualizar el historial de trabajos con el paso del tiempo. Esta consulta se puede usar para buscar el estado de los trabajos con el paso del tiempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico del historial de trabajos de Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Eliminación de la configuración de diagnóstico

Para quitar la configuración de diagnóstico de la cuenta de Automation, ejecute los siguientes comandos:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Resumen

Al enviar el estado de trabajo y los datos de flujo de Automation a los registros de Azure Monitor, puede obtener más información del estado de los trabajos de Automation mediante lo siguiente:
+ Establecimiento de alertas que le notificarán cuando haya un problema.
+ Uso de vistas personalizadas y de consultas de búsqueda para visualizar los resultados de runbook, el estado del trabajo de runbook y otras métricas o indicadores clave relacionados.

Los registros de Azure Monitor proporcionan mayor visibilidad operativa para los trabajos de Automation y pueden ayudar a resolver incidentes con más rapidez.

## <a name="next-steps"></a>Pasos siguientes

* Si necesita ayuda con la solución de problemas de análisis de registros, consulte el artículo sobre [por qué el análisis de registros ya no recopila datos](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automation con los registros de Azure Monitor, consulte [Búsquedas de registros en los registros de Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Para aprender a crear y recuperar los mensajes de salida y de error de los runbooks, consulte [Salidas de runbook y mensajes](automation-runbook-output-and-messages.md).
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md).
* Para obtener más información sobre los registros de Azure Monitor y los orígenes de recopilación de datos, consulte [Introducción a la recopilación de datos de almacenamiento en los registros de Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).

