---
title: Exportar alertas y recomendaciones de Azure Security Center a SIEM | Microsoft Docs
description: En este artículo se explica cómo configurar la exportación continua de alertas y recomendaciones de seguridad a SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f994f4ec6d41fa0aab37e36d713eaefb22e85b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665084"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Exportar alertas y recomendaciones de seguridad (versión preliminar)

Azure Security Center genera alertas y recomendaciones de seguridad detalladas. Puede verlas en el portal o mediante herramientas de programación. También puede exportar esta información o enviarla a otras herramientas de supervisión de su entorno. 

En este artículo se describe el conjunto de herramientas (versión preliminar) que le permiten exportar alertas y recomendaciones de forma manual o continua.

Con estas herramientas puede realizar lo siguiente:

* Generar informes detallados como CSV
* Exportar elementos a las áreas de trabajo de Log Analytics
* Exportar elementos a Azure Event Hubs (para integraciones con SIEM de terceros)

## <a name="setting-up-a-continuous-export"></a>Configuración de una exportación continua

1. En la barra lateral de Security Center, haga clic en **Precios y configuración**.

1. Seleccione la suscripción específica para la que quiere configurar la exportación de datos.
    
1. En la barra lateral de la página de configuración de esa suscripción, seleccione **Exportación continua (versión preliminar)** .

    [![Opciones de exportación en Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox): aquí se ven las opciones de exportación. Hay una pestaña para cada destino de exportación disponible. 

1. Seleccione el tipo de datos que quiere exportar y elija los filtros que quiera de cada tipo (por ejemplo, exportar solo alertas de gravedad alta).

1. En el área "Destino de exportación", elija dónde quiere guardar los datos. Los datos se pueden guardar en el destino de una suscripción diferente (por ejemplo, en una instancia central del Centro de eventos o en un área de trabajo central de Log Analytics).

1. Haga clic en **Save**(Guardar).

## <a name="continuous-export-through-azure-event-hubs"></a>Exportación continua a través de Azure Event Hubs  

> [!NOTE]
> El método más efectivo en la mayoría de los casos para transmitir datos de supervisión a herramientas externas, es usar Azure Event Hubs. [En este artículo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) se proporciona una breve descripción de cómo puede transmitir datos de supervisión desde diferentes orígenes a un centro de eventos y a vínculos de instrucciones detalladas.

> [!NOTE]
> Si exportó anteriormente alertas de Security Center a un SIEM mediante el registro de actividad de Azure, el procedimiento que se indica a continuación reemplaza esa metodología.

### <a name="to-integrate-with-a-siem"></a>Integración con un SIEM 

Después de configurar la exportación continua de los datos de Security Center seleccionados a Azure Event Hubs, puede configurar el conector adecuado en el SIEM siguiendo las instrucciones que se indican a continuación.

Siga las instrucciones necesarias para el SIEM desde [esta página](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) y use el conector pertinente:

* **Splunk**: use el [complemento de Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar**: use [un origen de registro configurado manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight**: use [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Si usa **Azure Sentinel**, use el [conector de datos](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de las alertas nativas de Azure Security Center que se ofrecen ahí.

Asimismo, si quiere trasladar los datos exportados continuamente desde el Centro de eventos configurado a Azure Data Explorer, siga las instrucciones que se detallan en la [ingesta de datos del Centro de eventos en Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Exportación continua a las áreas de trabajo de Log Analytics

Para exportar al área de trabajo de Log Analytics, debe tener habilitadas las soluciones de Log Analytics de nivel gratuito o estándar de Security Center en el área de trabajo. Si usa Azure Portal, la solución de nivel gratuito de Security Center se habilita automáticamente al habilitar la exportación continua. Sin embargo, si va a configurar las opciones de exportación continua mediante programación, debe seleccionar manualmente el plan de tarifa gratuito o estándar para el área de trabajo necesaria desde **Precios y configuración**.  

Las alertas y recomendaciones de seguridad se almacenan en las tablas *SecurityAlert* y *SecurityRecommendations* respectivamente. El nombre de la solución de Log Analytics que contiene estas tablas cambiará dependiendo de si está en el nivel gratuito o estándar (consulte los [precios](security-center-pricing.md)): Security o SecurityCenterFree.

![Tabla *SecurityAlert* en Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visualización de las recomendaciones y alertas de seguridad exportadas en Azure Monitor

En algunos casos, puede elegir ver las alertas de seguridad exportadas o las recomendaciones en [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor proporciona un conjunto de alertas unificadas que consta de varias alertas de Azure, como el registro de diagnóstico, las alertas de métricas y las alertas personalizadas basadas en las consultas del área de trabajo de Log Analytics.

Para ver las alertas y recomendaciones de Security Center en Azure Monitor, configure una regla de alerta basada en consultas de Log Analytics (alerta de registro):

1. En la página **Alertas** de Azure Monitor, haga clic en **Nueva regla de alertas**.

    ![Página de alertas de Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. En la página de creación de reglas, configure la nueva regla (de la misma manera que configuraría una [regla de alertas de registro en Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log):

    * En **Recurso**, seleccione el área de trabajo de Log Analytics a la que exportó las recomendaciones y las alertas de seguridad.

    * En **Condición**, seleccione **Custom log search** (Búsqueda de registros personalizada). En la página que aparece, configure la consulta, el período de retrospectiva y el período de frecuencia. En la consulta de búsqueda, puede escribir *SecurityAlert* o *SecurityRecommendation* para consultar los tipos de datos que Security Center exporta continuamente al habilitar la característica de exportación continua a Log Analytics. 
    
    * Opcionalmente, en [Grupo de acciones](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups), configure el grupo de acciones que desea desencadenar. Los grupos de acciones pueden desencadenar el envío de correo electrónico, los vales de ITSM, los webhooks, etc.
    ![Regla de alerta de Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Ahora verá las nuevas alertas o recomendaciones de Azure Security Center (según la configuración) en las alertas de Azure Monitor, con el desencadenamiento automático de un grupo de acciones (si se proporciona).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportación manual de un solo uso de alertas de seguridad

Para descargar un informe de CSV para alertas o recomendaciones, abra la página de **alertas de seguridad** o de **recomendaciones** y haga clic en el botón para **descargar el informe de CSV (vista previa)** .

[![Descargar datos de alertas como un archivo CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Estos informes contienen alertas y recomendaciones para los recursos de las suscripciones seleccionadas actualmente en el filtro "Directorio y suscripción" de Azure Portal: ![Filtro para seleccionar el directorio y la suscripción](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a configurar exportaciones continuas de sus recomendaciones y alertas. También aprendió a descargar los datos de alertas como un archivo CSV. 

Para obtener material relacionado, consulte la documentación siguiente: 

- [Documentación de Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Documentación de Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentación sobre Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)