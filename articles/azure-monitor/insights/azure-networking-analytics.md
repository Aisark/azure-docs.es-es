---
title: Solución Azure Networking Analytics en Azure Monitor | Microsoft Docs
description: Puede usar la solución Azure Networking Analytics en Azure Monitor para revisar los registros del grupo de seguridad de red de Azure y los registros de Azure Application Gateway.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.openlocfilehash: 0a5d886558e72ef24b03a49750ed75cf7130bf08
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006391"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Las soluciones en Azure Monitor de supervisión de redes de Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor ofrece las siguientes soluciones para supervisar las redes:
* Monitor de rendimiento de red (NPM)
    * Supervisión del mantenimiento de la red
* Azure Application Gateway Analytics para revisar
    * Registros de Azure Application Gateway
    * Métricas de Azure Application Gateway
* Soluciones para supervisar y auditar la actividad de red en la red de la nube
    * [Análisis de tráfico](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM) (monitor de rendimiento de red)

La Solución de administración [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) supervisa el mantenimiento, la disponibilidad y accesibilidad de las redes.  Se utiliza para supervisar la conectividad entre:

* Nube pública y entorno local
* Centros de datos y ubicaciones de usuario (sucursales)
* Subredes que hospedan distintos niveles de una aplicación de varios niveles.

Para más información consulte [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway y Network Security Group Analytics
Para usar las soluciones:
1. Agregar la solución de administración a Azure Monitor, y
2. Habilitar los diagnósticos dirigir los diagnósticos a un área de trabajo de Log Analytics en Azure Monitor. No se requiere escribir los registros en Azure Blob Storage.

Puede habilitar los diagnósticos y la solución correspondiente solo para Application Gateway, solo para Networking Security Groups o para ambas.

Si no habilita el registro de diagnósticos para un tipo de recurso en particular, pero instala la solución, las hojas de panel para ese recurso estarán en blanco y mostrarán un mensaje de error.

> [!NOTE]
> En enero de 2017, la forma de enviar los registros de Application Gateway y grupos de seguridad de red a un área de trabajo de Log Analytics puede cambiar. Si consulta la solución **Azure Networking Analytics (en desuso)**, remítase a la sección [Migración desde la solución Networking Analytics antigua ](#migrating-from-the-old-networking-analytics-solution) para conocer los pasos que debe seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Revisión de los detalles de recopilación de datos de redes de Azure
Las soluciones de administración Azure Application Gateway Analytics y Network Security Group Analytics recopilan registros de diagnósticos directamente de Azure Application Gateways y Network Security Groups. No es necesario escribir los registros en Azure Blob Storage y no se requiere ningún agente para la recopilación de datos.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos análisis de Azure Application Gateway y Network Security Group.

| Plataforma | Agente directo | Agente System Center Operations Manager | Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Cuando se inicia sesión |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Solución de Azure Application Gateway analytics en Azure Monitor

![Símbolo de Azure Application Gateway Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

Para Application Gateway se admiten los siguientes registros:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Las métricas siguientes son compatibles con Application Gateway:


* Rendimiento de 5 minutos

### <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución Azure Application Gateway, siga estas instrucciones:

1. Habilite la solución de análisis de Azure Application Gateway desde [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) o mediante el proceso descrito en [soluciones de agregar Azure Monitor desde la Galería de soluciones](../../azure-monitor/insights/solutions.md).
2. Habilite el registro de diagnósticos para las [Application Gateway](../../application-gateway/application-gateway-diagnostics.md) que desea supervisar.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Habilitación de los diagnósticos de Azure Application Gateway en el portal

1. En el portal de Azure, navegue hasta el recurso de Application Gateway para supervisar.
2. Seleccione *los registros de diagnóstico* para abrir la página siguiente.

   ![imagen del recurso de Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Haga clic en *Activar diagnósticos* para abrir la página siguiente.

   ![imagen del recurso de Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Para activar los diagnósticos, haga clic en *en* en *estado*.
5. Haga clic en la casilla de verificación *enviar a Log Analytics*.
6. Seleccione un área de trabajo de Log Analytics existente o cree un área de trabajo.
7. Haga clic en la casilla de verificación bajo **registro** para cada uno de los tipos de registro para recopilar.
8. Haga clic en *guardar* para habilitar el registro de diagnósticos en Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Habilitación de los diagnósticos de red de Azure con PowerShell

El siguiente script de PowerShell proporciona un ejemplo de cómo habilitar el registro de diagnósticos puertas de enlace de aplicaciones.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Uso de análisis de Azure Application Gateway
![imagen del icono de análisis de Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Tras hacer clic en el icono **Azure Application Gateway analytics** (Análisis de Azure Application Gateway) en Overview (Información general), puede ver resúmenes de los registros y desplazarse hasta los detalles de las categorías siguientes:

* Registros de acceso de Application Gateway
  * Errores de cliente y servidor de los registros de acceso de Application Gateway
  * Solicitudes por hora para cada Application Gateway
  * Solicitudes fallidas por hora para cada Application Gateway
  * Errores por agente de usuario de las puertas de enlace de las aplicaciones
* Rendimiento de Application Gateway
  * Estado de host de Application Gateway
  * Percentil 95 y máximo para las solicitudes fallidas de Application Gateway

![imagen del panel de análisis de Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway01.png)

![imagen del panel de análisis de Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway02.png)

En el panel **Azure Application Gateway analytics** (Análisis de Azure Application Gateway), revise la información de resumen en una de las hojas y haga clic en una para obtener información detallada sobre la página de búsqueda de registros.

En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Solución de Azure Network Security Group analytics en Azure Monitor

![Símbolo de Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> La solución de análisis Grupo de seguridad de red se traslada a la compatibilidad con la comunidad ya que su funcionalidad se ha reemplazado por [Análisis de tráfico](../../network-watcher/traffic-analytics.md).
> - La solución ahora está disponible en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) y pronto ya no estarán disponibles en Azure Marketplace.
> - Para los clientes existentes que ya agregaron la solución a su área de trabajo, seguirá funcionando sin cambios.
> - Microsoft seguirá admitiendo el envío de registros de diagnóstico de grupos de seguridad de red al área de trabajo mediante Configuración de diagnóstico.

Para los grupos de seguridad de red se admiten los siguientes registros:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución Azure Networking Analytics, siga estas instrucciones:

1. Habilite la solución de Azure Network Security Group analytics desde [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) o mediante el proceso descrito en [soluciones de agregar Azure Monitor desde la Galería de soluciones](../../azure-monitor/insights/solutions.md).
2. Habilite el registro de diagnósticos para los recursos de [Network Security Group](../../virtual-network/virtual-network-nsg-manage-log.md) que desea supervisar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Habilitación de los diagnósticos de Azure Network Security Group en el portal

1. En Azure Portal, navegue hasta el recurso Grupo de seguridad de red que se va a supervisar.
2. Seleccione *Registros de diagnósticos* para abrir la página siguiente.

   ![imagen del recurso Grupo de seguridad de red de Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Haga clic en *Activar diagnósticos* para abrir la página siguiente.

   ![imagen del recurso Grupo de seguridad de red de Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Para activar los diagnósticos, haga clic en *Activar* en *Estado*.
5. Haga clic en la casilla *Send to Log Analytics* (Enviar a Log Analytics).
6. Seleccione un área de trabajo de Log Analytics existente o cree un área de trabajo.
7. En **Registro**, haga clic en la casilla correspondiente a cada uno de los tipos de registro para recopilar.
8. Haga clic en *Guardar* para habilitar el registro de diagnósticos en Log Analytics.

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Habilitación de los diagnósticos de red de Azure con PowerShell

El siguiente script de PowerShell proporciona un ejemplo de cómo habilitar el registro de diagnósticos para grupos de seguridad de red:
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Uso de Azure Network Security Group Analytics
Tras hacer clic en el icono **Azure Network Security Group Analytics** en Overview (Información general), puede ver resúmenes de los registros y desplazarse hasta los detalles de las categorías siguientes:

* Flujos bloqueados de grupos de seguridad de red
  * Reglas de los grupos de seguridad de red con flujos bloqueados
  * Direcciones MAC con flujos bloqueados
* Flujos permitidos en los grupos de seguridad de red
  * Reglas de los grupos de seguridad de red con flujos permitidos
  * Direcciones MAC con flujos permitidos

![imagen del panel Azure Network Security Group Analytics](media/azure-networking-analytics/log-analytics-nsg01.png)

![imagen del panel Azure Network Security Group Analytics](media/azure-networking-analytics/log-analytics-nsg02.png)

En el panel **Azure Network Security Group Analytics**, revise la información de resumen en una de las hojas y haga clic en una para obtener información detallada sobre la página de búsqueda de registros.

En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migración desde la solución Networking Analytics antigua
En enero de 2017, la forma de enviar los registros de Azure Application Gateways y grupos de seguridad de red de Azure a un área de trabajo de Log Analytics puede cambiar. Estos cambios proporcionan las siguientes ventajas:
+ Los registros se escriben directamente en Azure Monitor sin la necesidad de usar una cuenta de almacenamiento
+ Menor latencia desde el momento cuando se generan los registros hasta que están disponibles en Azure Monitor
+ Menos pasos de configuración
+ Un formato común para todos los tipos de diagnósticos de Azure

Para usar las soluciones actualizadas:

1. [Configure los diagnósticos para que se envían directamente a Azure Monitor desde Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configure los diagnósticos para que se envían directamente a Azure Monitor de grupos de seguridad de red de Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Habilitar la *Azure Application Gateway Analytics* y el *Azure Network Security Group Analytics* solución mediante el proceso descrito en [soluciones de agregar Azure Monitor desde la Galería de soluciones](solutions.md)
3. Actualice cualquier consulta guardada, panel o alerta para usar el nuevo tipo de datos.
   + El tipo es AzureDiagnostics. Puede usar ResourceType para filtrar registros de redes de Azure.

     | En lugar de: | Uso: |
     | --- | --- |
     | NetworkApplicationgateways &#124; donde OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; donde ResourceType="APPLICATIONGATEWAYS" y OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; donde OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; donde ResourceType=="APPLICATIONGATEWAYS" y OperationName=ApplicationGatewayPerformance |
     | NetworkSecuritygroups | AzureDiagnostics &#124; donde ResourceType=="NETWORKSECURITYGROUPS" |

   + Para todos los campos que tengan un sufijo \_s, \_d o \_g en el nombre, cambie el primer carácter a minúsculas.
   + Para todos los campos que tengan un sufijo \_o en el nombre, los datos se dividen en campos individuales según los nombres de campo anidados.
4. Quite la solución *Azure Networking Analytics (en desuso)*.
   + Si usa PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Los datos recopilados antes del cambio no aparecen en la nueva solución. Puede seguir consultando estos datos con el tipo y los nombres de campo anteriores.

## <a name="troubleshooting"></a>solución de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Pasos siguientes
* Use [registro las consultas en Azure Monitor](../log-query/log-query-overview.md) ver datos de diagnósticos de Azure de detallados.
