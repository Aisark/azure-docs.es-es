---
title: Supervisión de mensajes B2B con Azure Monitor
description: Configuración del registro de diagnóstico para los mensajes AS2, X12 y EDIFACT en Azure Logic Apps mediante Azure Monitor
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: cd96376f764ec3075b916bf2207ec6ee3dd3fcbd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791925"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Configuración el registro de diagnóstico para los mensajes B2B en Azure Logic Apps mediante Azure Monitor

Después de establecer la comunicación B2B entre entidades en la cuenta de integración, los asociados pueden intercambiar mensajes entre sí. Para comprobar que esta comunicación funciona según lo esperado, puede supervisar los mensajes AS2, X12 y EDIFACT y configurar el registro de diagnóstico de la cuenta de integración mediante los [registros de Azure Monitor](../log-analytics/log-analytics-overview.md). Este servicio supervisa los entornos locales y en la nube, lo que le permite conservar la disponibilidad y el rendimiento, y recopila detalles de entorno de tiempo de ejecución y eventos para lograr una depuración más completa. También puede usar estos datos con otros servicios, como Azure Storage y Azure Event Hubs.

> [!NOTE]
> Esta página puede que incluya aún referencias a Microsoft Operations Management Suite (OMS), que se [retirará en enero de 2019](../azure-monitor/platform/oms-portal-transition.md), pero reemplace esos pasos por Azure Log Analytics siempre que sea posible. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una aplicación lógica configurada con registro de diagnósticos. Obtenga información sobre [cómo crear una aplicación lógica](quickstart-create-first-logic-app-workflow.md) y [cómo configurar el registro de esa aplicación lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Una vez que cumpla con los requisitos anteriores, también necesitará un área de trabajo de Log Analytics que usará para supervisar y realizar el seguimiento de la comunicación B2B mediante los registros de Azure Monitor. Si no tiene un área de trabajo de Log Analytics, aprenda a [crear un área de trabajo de Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Una cuenta de integración vinculada a la aplicación lógica. Aprenda a [crear una cuenta de integración con un vínculo a la aplicación lógica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Activación del registro de diagnóstico

Puede activar el registro directamente desde la cuenta de integración o [mediante el servicio de Azure Monitor](#azure-monitor-service). Azure Monitor ofrece supervisión básica con datos de nivel de infraestructura. Más información sobre [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Activación del registro desde la cuenta de integración

1. En [Azure Portal](https://portal.azure.com), busque y seleccione la cuenta de integración. En **Supervisión**, seleccione **Configuración de diagnóstico**.

   ![Buscar y seleccionar la cuenta de integración, seleccionar "Registros de diagnóstico"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Busque y seleccione su cuenta de integración. En las listas de filtros, seleccione los valores que se aplican a su cuenta de integración.
Cuando haya terminado, elija **Agregar configuración de diagnóstico**.

   | Propiedad | Valor | DESCRIPCIÓN | 
   |----------|-------|-------------|
   | **Suscripción** | <*Azure-subscription-name*> | La suscripción de Azure asociada a la cuenta de integración | 
   | **Grupos de recursos** | <*nombre del grupo de recursos de Azure*> | El grupo de recursos de Azure de la cuenta de integración | 
   | **Tipo de recurso** | **Cuentas de integración** | El tipo del recurso de Azure para el que desea activar el registro | 
   | **Recurso** | <*integration-account-name*> | El nombre del recurso de Azure para el que desea activar el registro | 
   ||||  

   Por ejemplo:

   ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Proporcione un nombre para la nueva configuración de diagnóstico y seleccione el área de trabajo de Log Analytics y los datos que desee registrar.

   1. Seleccione **Enviar a Log Analytics**. 

   1. En **Log Analytics**, seleccione **Configurar**. 

   1. En **Áreas de trabajo de OMS**, seleccione el área de trabajo de Log Analytics que desea usar para el registro. 

      > [!NOTE]
      > Las áreas de trabajo de OMS ahora se conocen como áreas de trabajo de Log Analytics. 

   1. En **Registro**, seleccione la categoría **IntegrationAccountTrackingEvents** y elija **Guardar**.

   Por ejemplo: 

   ![Configuración de los registros de Azure Monitor para que pueda enviar datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Ahora [configure el seguimiento de los mensajes B2B en los registros de Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Activación del registro mediante Azure Monitor

1. En [Azure Portal](https://portal.azure.com), en el menú principal de Azure, seleccione **Monitor**. En **Configuración**, seleccione **Configuración de diagnóstico**. 

   ![Seleccionar "Monitor" > "Configuración de diagnóstico" > su cuenta de integración](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Busque y seleccione su cuenta de integración. En las listas de filtros, seleccione los valores que se aplican a su cuenta de integración.
Cuando haya terminado, elija **Agregar configuración de diagnóstico**.

   | Propiedad | Valor | DESCRIPCIÓN | 
   |----------|-------|-------------|
   | **Suscripción** | <*Azure-subscription-name*> | La suscripción de Azure asociada a la cuenta de integración | 
   | **Grupos de recursos** | <*nombre del grupo de recursos de Azure*> | El grupo de recursos de Azure de la cuenta de integración | 
   | **Tipo de recurso** | **Cuentas de integración** | El tipo del recurso de Azure para el que desea activar el registro | 
   | **Recurso** | <*integration-account-name*> | El nombre del recurso de Azure para el que desea activar el registro | 
   ||||  

   Por ejemplo:

   ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Proporcione un nombre para la nueva configuración de diagnóstico y seleccione el área de trabajo de Log Analytics y los datos que desee registrar.

   1. Seleccione **Enviar a Log Analytics**. 

   1. En **Log Analytics**, seleccione **Configurar**. 

   1. En **Áreas de trabajo de OMS**, seleccione el área de trabajo de Log Analytics que desea usar para el registro. 

      > [!NOTE]
      > Las áreas de trabajo de OMS ahora se conocen como áreas de trabajo de Log Analytics. 

   1. En **Registro**, seleccione la categoría **IntegrationAccountTrackingEvents** y elija **Guardar**.

   Por ejemplo: 

   ![Configuración de los registros de Azure Monitor para que pueda enviar datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Ahora [configure el seguimiento de los mensajes B2B en los registros de Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Uso de datos de diagnóstico con otros servicios

Además de con registros de Azure Monitor, puede usar los datos de diagnóstico de la aplicación lógica con otros servicios de Azure, por ejemplo: 

* [Archivar registros de Diagnósticos de Azure en Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Transmitir registros de Diagnósticos de Azure a Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Luego puede obtener supervisión en tiempo real mediante la telemetría y los análisis de otros servicios, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) y [Power BI](../azure-monitor/platform/powerbi.md). Por ejemplo:

* [Transmitir datos de Event Hubs a Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizar datos que se están transmitiendo con Stream Analytics y crear un panel de análisis en tiempo real en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Según las opciones que quiera configurar, primero asegúrese de [crear una cuenta de Azure Storage](../storage/common/storage-create-storage-account.md) o [crear un centro de eventos de Azure](../event-hubs/event-hubs-create.md). Después, puede seleccionar los destinos a los que desea enviar los datos de diagnóstico.
Los períodos de retención solo se aplican cuando se usa una cuenta de almacenamiento.

![Envío de los datos a una cuenta de Azure Storage o a un centro de eventos](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Esquemas de seguimiento compatibles

Azure admite los siguientes tipos de esquema de seguimiento, de los que todos, salvo el tipo personalizado, tienen esquemas fijos.

* [Esquema de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de seguimiento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquema de seguimiento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Pasos siguientes

* [Seguimiento de mensajes B2B en los registros de Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Seguimiento de mensajes B2B en los registros de Azure Monitor")
* [Más información acerca de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

