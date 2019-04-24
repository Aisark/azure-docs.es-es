---
title: Prepararse para el cambio de formato a los registros de diagnóstico de Azure Monitor
description: Los registros de diagnóstico de Azure pasarán a usar blobs en anexos el 1 de noviembre de 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237645"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Prepararse para el cambio de formato a los registros de diagnóstico de Azure Monitor archivados en una cuenta de almacenamiento

> [!WARNING]
> Si va a enviar [los registros de diagnóstico de recursos de Azure o las métricas a una cuenta de almacenamiento mediante la configuración de diagnóstico de recursos](./../../azure-monitor/platform/archive-diagnostic-logs.md) o [registros de actividad a una cuenta de almacenamiento mediante perfiles de registro](./../../azure-monitor/platform/archive-activity-log.md), el formato de los datos de la cuenta de almacenamiento cambiará a JSON Lines el 1 de noviembre de 2018. En las instrucciones siguientes se describe la repercusión y el modo de actualizar las herramientas para administrar el nuevo formato. 
>
> 

## <a name="what-is-changing"></a>Lo que está cambiando

Azure Monitor ofrece una funcionalidad que le permite enviar datos de diagnóstico de recursos y datos de registro de actividad en una cuenta de almacenamiento de Azure, el espacio de nombres de Event Hubs, o en un área de trabajo de Log Analytics en Azure Monitor. Para solucionar un problema de rendimiento del sistema, **a las 12:00 de la noche (UTC) del 1 de noviembre de 2018** se cambiará el formato de los datos de registro que se envían a Blob Storage. Si tiene herramientas que leen los datos fuera de Blob Storage, deberá actualizarlas para que entiendan el formato de datos nuevo.

* A las 12:00 de la noche (UTC) del jueves 1 de noviembre de 2018, el formato de blob se cambiará a [JSON Lines](http://jsonlines.org/). Esto significa que todos los registros se delimitarán mediante una nueva línea, sin matrices de registros exteriores y sin comas entre los registros JSON.
* El formato de blob cambia para todas las configuraciones de diagnóstico en todas las suscripciones a la vez. En el primer archivo PT1H.json emitido para el 1 de noviembre se usará este formato nuevo. Los nombres de blobs y contenedores se mantienen sin cambios.
* Si se establece una configuración de diagnóstico entre hoy y el 1 de noviembre, se continúa con la emisión de los datos en el formato actual hasta el 1 de noviembre.
* Este cambio se producirá al mismo tiempo en todas las regiones de la nube pública. El cambio no se producirá todavía en las nubes de Azure China, Azure Alemania o Azure Government.
* Este cambio afecta a los tipos de datos siguientes:
  * [Registros de diagnóstico de recursos de Azure](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([vea aquí la lista de los recursos](./../../azure-monitor/platform/diagnostic-logs-schema.md)).
  * [Métricas de recursos de Azure que se exportan mediante la configuración de diagnóstico](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
  * [Datos del registro de actividad de Azure que se exportan mediante perfiles de registro](./../../azure-monitor/platform/archive-activity-log.md).
* Este cambio no afecta a:
  * Los registros de flujo de red.
  * Los registros de servicio de Azure que todavía no están disponible a través de Azure Monitor (por ejemplo, los registros de diagnóstico de Azure App Service y los de análisis de almacenamiento).
  * El enrutamiento de los registros de diagnóstico de Azure y los de actividad a otros destinos (Event Hubs, Log Analytics).

### <a name="how-to-see-if-you-are-impacted"></a>Cómo comprobar si se va a ver afectado

Solo se verá afectado por este cambio si:
1. Va a enviar datos de registro a una cuenta de Azure Storage mediante una configuración de diagnóstico, y
2. Tiene herramientas que dependen de la estructura JSON de estos registros en el almacenamiento.
 
Para identificar si tiene una configuración de diagnóstico de recursos que envía datos a una cuenta de Azure Storage, puede ir a la sección **Monitor** del portal, hacer clic en **Configuración de diagnóstico** e identificar todos los recursos que tienen **Estado del diagnóstico** establecido en **Habilitado**:

![Hoja Registros de diagnóstico de Azure Monitor](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Si Estado del diagnóstico está establecido en Habilitado, tiene una configuración de diagnóstico activa en ese recurso. Haga clic en el recurso para ver si alguna configuración de diagnóstico está enviando datos a una cuenta de almacenamiento:

![Cuenta de almacenamiento habilitada](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Si tiene recursos que envían datos a una cuenta de almacenamiento mediante estas opciones de diagnóstico de recursos, el formato de los datos de esa cuenta de almacenamiento se verá afectado por este cambio. A menos que tenga herramientas personalizadas que funcionan fuera de estas cuentas de almacenamiento, no se verá afectado por el cambio de formato.

### <a name="details-of-the-format-change"></a>Detalles del cambio de formato

El formato actual del archivo PT1H.json en Azure Blob Storage usa una matriz JSON de registros. El siguiente es un ejemplo de un archivo de registro de KeyVault:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

En el formato nuevo se usa [JSON Lines](http://jsonlines.org/), donde cada evento es una línea y el carácter de nueva línea indica un evento nuevo. Este es el aspecto que tendrá el ejemplo anterior en el archivo PT1H.json después del cambio:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Este formato nuevo permite que Azure Monitor inserte los archivos de registro mediante [blobs en anexos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que son más eficaces para anexar datos de eventos nuevos de forma continua.

## <a name="how-to-update"></a>Cómo actualizar

Solo necesita realizar actualizaciones si dispone de herramientas personalizadas que ingieren estos archivos de registro para su posterior procesamiento. Si usa una herramienta SIEM o de análisis de registros externa, se recomienda [usar Event Hubs para introducir estos datos en su lugar](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). La integración de Event Hubs es más fácil en términos de procesamiento de registros de muchos servicios y de marcar la ubicación de un registro determinado.

Las herramientas personalizadas se deben actualizar para controlar el formato actual y el formato de JSON Lines descrito anteriormente. Esto garantizará que, cuando los datos comiencen a aparecer en el formato nuevo, las herramientas no se vean interrumpidas.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [archivar los registros de diagnóstico en una cuenta de almacenamiento](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Obtenga información sobre cómo [archivar los datos de registro de actividad en una cuenta de almacenamiento](./../../azure-monitor/platform/archive-activity-log.md)

