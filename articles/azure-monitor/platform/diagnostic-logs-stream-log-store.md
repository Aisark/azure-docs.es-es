---
title: Transmisión de registros de Azure Diagnostics a Log Analytics
description: Aprenda a transmitir registros de Azure Diagnostics a un área de trabajo de Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3d187851fda9054bbfbae245ef34440b66ad017e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309322"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Transmisión de registros de Azure Diagnostics a Log Analytics

**[Los registros de Azure Diagnostics](diagnostic-logs-overview.md)** se pueden transmitir casi en tiempo real a Azure Log Analytics mediante el portal, cmdlets de PowerShell o CLI de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Qué se puede hacer con registros de diagnóstico en Log Analytics

Azure Log Analytics es una herramienta flexible de búsqueda y análisis de registros que permite obtener una visión general de los datos de registro sin procesar generados a partir de recursos de Azure. Entre estas funcionalidades, cabe destacar:

* **Búsqueda de registros**: escriba consultas avanzadas sobre los datos de registro, correlacione registros de varios orígenes e incluso genere gráficos que se pueden anclar al panel de Azure.
* **Generación de alertas**: detecte cuando uno o varios eventos coinciden con una consulta determinada y reciba notificaciones por correo electrónico o llamada de webhook.
* **Soluciones**: use vistas preintegradas y paneles que aportan información inmediata sobre los datos de registro.
* **Análisis avanzados**: aplique el aprendizaje automático y algoritmos de coincidencia de patrones para identificar posibles problemas revelados por los registros.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Habilitación de la transmisión de registros de diagnóstico a Log Analytics

Puede habilitar el streaming de registros de diagnóstico mediante programación, a través del portal o mediante la [API de REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). En cualquier caso, puede crear una configuración de diagnóstico en la que se especifica un área de trabajo de Log Analytics y las categorías de registro y métricas que desea enviar a dicha área de trabajo. Una **categoría de registro** de diagnóstico es un tipo de registro que puede proporcionar un recurso.

El área de trabajo de Log Analytics no tiene que estar en la misma suscripción que la del recurso que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

> [!NOTE]
> Actualmente no se admite el envío de métricas de varias dimensiones a través de la configuración de diagnóstico. Las métricas con dimensiones se exportan como métricas unidimensionales planas agregadas a través de los valores de dimensión.
>
> *Por ejemplo*: la métrica "Mensajes entrantes" de una instancia de Event Hub se puede explorar y representar gráficamente por colas. Sin embargo, cuando se exporta a través de la configuración de diagnóstico, la métrica se representará con todos los mensajes entrantes de todas las colas de Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Streaming de registros de diagnóstico mediante el portal
1. En el portal, desplácese a Azure Monitor y haga clic en **Configuración de diagnóstico**.

    ![Sección de supervisión de Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Si lo desea, filtre la lista por tipo de recurso o por grupo de recursos y, a continuación, haga clic en el recurso para el que desea establecer la configuración de diagnóstico.

3. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en "Activar diagnóstico".

   ![Agregar configuración de diagnóstico: sin configuración actual](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Si hay una configuración actual en el recurso, verá una lista de opciones ya configuradas en este recurso. Haga clic en "Agregar configuración de diagnóstico".

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Asigne un nombre de su configuración y active la casilla **Enviar a Log Analytics** y, después, seleccione un área de trabajo de Log Analytics.

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Haga clic en **Save**(Guardar).

Transcurridos unos instantes, la nueva opción de configuración aparece en la lista de opciones para este recurso y los registros de diagnóstico se transmiten en esa área de trabajo en cuanto se generan nuevos datos de eventos. Tenga en cuenta que pueden pasar hasta quince minutos entre cuando se emite un evento y cuando aparece en Log Analytics.

### <a name="via-powershell-cmdlets"></a>Mediante cmdlets de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar el streaming mediante [cmdlets de Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), puede utilizar el cmdlet `Set-AzDiagnosticSetting` con estos parámetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Tenga en cuenta que la propiedad workspaceID toma el identificador de recurso completo de Azure del área de trabajo, no el identificador/clave del área de trabajo que se muestra en el portal de Log Analytics.

### <a name="via-azure-cli"></a>Mediante la CLI de Azure

Para habilitar el streaming a través de la [CLI de Azure](../../azure-monitor/platform/cli-samples.md), puede usar el comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Puede agregar categorías adicionales al registro de diagnóstico mediante la incorporación de diccionarios a la matriz JSON que se pasa como el parámetro `--logs`.

El argumento `--resource-group` solo es obligatorio si `--workspace` no es un identificador de objeto.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>¿Cómo se consultan los datos en Log Analytics?

En la hoja Búsqueda de registros del portal o en la experiencia de análisis avanzado como parte de Log Analytics, puede consultar los registros de diagnóstico como parte de la solución Log Management en la tabla AzureDiagnostics. También hay [varias soluciones para recursos de Azure](../../azure-monitor/insights/solutions.md) que se pueden instalar para obtener información inmediata sobre los datos del registro que va a enviar a Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los registros de Diagnósticos de Azure](diagnostic-logs-overview.md)

