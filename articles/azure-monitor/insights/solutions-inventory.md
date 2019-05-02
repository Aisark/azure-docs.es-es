---
title: Detalles de la recopilación de datos para las soluciones de administración en Azure | Microsoft Docs
description: Las soluciones de administración de Azure son una colección de reglas de lógica, visualización y adquisición de datos que proporcionan métricas que giran en torno a una determinada área de problemas.  Este artículo proporciona una lista de las soluciones de administración disponibles de Microsoft y los detalles del método y la frecuencia de recopilación de datos.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 3bdadd2ac5c7790f9e1da57de925bfba659a5890
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916552"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Detalles de la recopilación de datos para las soluciones de administración en Azure
En este artículo se incluye una lista de las [soluciones de administración](solutions.md) disponibles de Microsoft con vínculos a la documentación detallada.  También se proporciona información sobre el método y la frecuencia de recopilación de datos en Azure Monitor.  Puede usar la información de este artículo para identificar las diferentes soluciones disponibles y comprender los requisitos de conexión y flujo de datos de las distintas soluciones de administración. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Lista de soluciones de administración

En la siguiente tabla se recogen las [soluciones de administración](solutions.md) en Azure que proporciona Microsoft. Una entrada en la columna significa que la solución recopila datos en Azure Monitor con ese método.  Si una solución no tiene columnas seleccionadas, eso significa que escribe directamente en Azure Monitor desde otro servicio de Azure. Siga el vínculo a la documentación detallada de cada uno de ellos para más información.

Explicaciones de las columnas:

- **Agente de supervisión de Microsoft**: agente que se usa en Windows y Linux para ejecutar el módulo de administración de SCOM y las soluciones de administración de Azure. En esta configuración, el agente está conectado directamente a Azure Monitor sin conexión a un grupo de administración de Operations Manager. 
- **Operations Manager**: agente idéntico al de supervisión de Microsoft. En esta configuración, se [conecta a un grupo de administración de Operations Manager](../../azure-monitor/platform/om-agents.md) conectado a su vez a Azure Monitor. 
-  **Azure Storage**: la solución recopila datos de una cuenta de Azure Storage. 
- **¿Se requiere Operations Manager?** Se necesita un grupo de administración de Operations Manager conectado para que la solución de administración recopile los datos. 
- **Los datos del agente de Operations Manager se envían a través del grupo de administración**: si el agente está [conectado a un grupo de administración de SCOM](../../azure-monitor/platform/om-agents.md), los datos se envían a Azure Monitor desde el servidor de administración. En este caso, el agente no necesita conectarse directamente a Azure Monitor. Si esta casilla no está seleccionada, los datos se envían desde el agente directamente a Azure Monitor, aunque el agente esté conectado a un grupo de administración de SCOM. Tendrá que comunicarse con Azure Monitor mediante la [puerta de enlace de Log Analytics](../../azure-monitor/platform/gateway.md).
- **Frecuencia de recopilación**: especifica la frecuencia con la que la solución de administración recopila los datos. 



| **Solución de administración** | **Plataforma** | **Agente de supervisión de Microsoft** | **Agente de Operations Manager** | **Almacenamiento de Azure** | **¿Se requiere Operations Manager?** | **Se envían los datos del agente de Operations Manager a través del grupo de administración** | **Frecuencia de recopilación** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Análisis de Activity Log](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | según notificación |
| [Evaluación de AD](../../azure-monitor/insights/ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 días |
| [Estado de replicación de AD](../../azure-monitor/insights/ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 días |
| [Agent Health](solution-agenthealth.md) | Windows y Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |a la llegada |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | N/D |
| [Application Insights Connector (en desuso)](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | según notificación |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | N/D |
| [Azure Application Gateway Analytics](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | según notificación |
| **Solución de administración** | **Plataforma** | **Agente de supervisión de Microsoft** | **Agente de Operations Manager** | **Almacenamiento de Azure** | **¿Se requiere Operations Manager?** | **Se envían los datos del agente de Operations Manager a través del grupo de administración** | **Frecuencia de recopilación** |
| [Azure Network Security Group Analytics (en desuso)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | según notificación |
| [Azure SQL Analytics (versión preliminar)](../../azure-monitor/insights/azure-sql.md) |  Windows | | | | | | 1 minuto |
| [Copia de seguridad](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | según notificación |
| [Capacity and Performance (versión preliminar)](../../azure-monitor/insights/capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |a la llegada |
| [Seguimiento de cambios](../../automation/change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Seguimiento de cambios](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contenedores](../../azure-monitor/insights/containers.md) | Windows y Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análisis de Key Vault](../../azure-monitor/insights/azure-key-vault.md) | Windows | | | | | |según notificación |
| [Evaluación de malware](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |Cada hora |
| [Monitor de rendimiento de red](../../azure-monitor/insights/network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | Protocolos de enlace TCP cada 5 segundos; se envían datos cada 3 minutos. |
| [Office 365 Analytics (versión preliminar)](solution-office-365.md) | Windows | | | | | |según notificación |
| **Solución de administración** | **Plataforma** | **Agente de supervisión de Microsoft** | **Agente de Operations Manager** | **Almacenamiento de Azure** | **¿Se requiere Operations Manager?** | **Se envían los datos del agente de Operations Manager a través del grupo de administración** | **Frecuencia de recopilación** |
| [Análisis de Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 minutos |
| [Mapa de servicio](../../azure-monitor/insights/service-map.md) | Windows y Linux | &#8226; | &#8226; |  |  |  | 15 segundos |
| [Evaluación de SQL](../../azure-monitor/insights/sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 días |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) | Windows |&#8226; | | | | |a la llegada |
| [System Center Operations Manager Assessment (versión preliminar)](../../azure-monitor/insights/scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | siete días |
| [Administración de actualizaciones](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |al menos 2 veces al día y 15 minutos después de instalar una actualización |
| [Preparación para la actualización](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 días |
| [VMware Monitoring (en desuso)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versión preliminar)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2 / 8.1 o posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [crear consultas](../../azure-monitor/log-query/log-query-overview.md) para analizar los datos recopilados por las soluciones de administración.
