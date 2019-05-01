---
title: Configuración de alertas de supervisión en Azure Stream Analytics
description: En este artículo se describe cómo usar Azure Portal para configurar la supervisión y las alertas para los trabajos de Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 52db8217cc1e1f84d25ab896be9b42db3bf6bd81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771980"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuración de alertas en Azure Stream Analytics

Es importante supervisar el trabajo de Azure Stream Analytics para asegurarse de que se está ejecutando continuamente sin problemas. En este artículo se describe cómo configurar alertas para escenarios comunes que se deben supervisar. 

Las reglas se pueden configurar en las métricas en el portal y también [mediante programación](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre datos de registros de operaciones.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configuración de alertas en Azure Portal

El ejemplo siguiente muestra cómo configurar alertas para cuando el trabajo entra en un estado de error. Esta alerta se recomienda para todos los trabajos.

1. En Azure Portal, abra el trabajo de Stream Analytics para el que desea crear una alerta.

2. En la página **Trabajo**, vaya a la sección **Supervisión**.  

3. Seleccione **Métricas** y, a continuación, haga clic en **Nueva regla de alertas**.

   ![Configuración de alertas de Stream Analytics en Azure Portal](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. El nombre del trabajo de Stream Analytics debe aparecer automáticamente en **RECURSO**. Haga clic en **Agregar condición** y seleccione **All Administrative operations** (Todas las operaciones administrativas) en **Configurar lógica de señal**.

   ![Selección del nombre de la señal para la alerta de Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. En **Configurar lógica de señal**, cambie **Nivel de evento** a **Todo** y cambie **Estado** a **Con error**. Deje **Evento iniciado por** en blanco y haga clic en **Listo**.

   ![Configuración de señal lógica para alerta de Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Seleccione un grupo de acciones existente o cree uno nuevo. En este ejemplo, un nuevo grupo de acciones llamado **TIDashboardGroupActions** se ha creado con la acción **Emails** que envía un correo electrónico a los usuarios con el rol de **propietario** de Azure Resource Manager.

   ![Configuración de una alerta para un trabajo Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. En **RECURSO**, **CONDICIÓN** y **GRUPOS DE ACCIONES** debería haber una entrada. Tenga en cuenta que para que se activen las alertas, es preciso que se den las condiciones definidas. Por ejemplo, puede medir el valor promedio de una métrica en los últimos 15 minutos, cada 5 minutos.

   ![Creación de una regla de alerta de Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Agregue un **nombre de regla de alerta**, una **descripción**y su **grupo de recursos** en **DETALLES DE LA ALERTA** y haga clic en **Crear regla de alertas** para crear la regla para el trabajo de Stream Analytics.

   ![Creación de una regla de alerta de Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Escenarios que supervisar

Se recomiendan las siguientes alertas para supervisar el rendimiento del trabajo de Stream Analytics. Estas métricas deben evaluarse cada minuto durante los últimos 5 minutos.

|Métrica|Condición|Agregación de tiempo|Umbral|Acciones correctivas|
|-|-|-|-|-|
|Porcentaje de uso de SU|Mayor que|Máxima|80|Existen diversos factores que incrementan el porcentaje de uso de unidades de streaming. Puede escalar con paralelización de consultas o aumentar el número de unidades de streaming. Para más información, consulte [Aprovechamiento de la paralelización de consultas en Azure Stream Analytics](stream-analytics-parallelization.md).|
|Errores de tiempo de ejecución|Mayor que|Total|0|Examine los registros de diagnóstico o actividad y realice los cambios correspondientes en las entradas, la consulta o las salidas.|
|Retraso de la marca de agua|Mayor que|Máxima|Cuando el valor medio de esta métrica en los últimos 15 minutos es mayor que la tolerancia de llegada tardía (en segundos). Si no ha modificado la tolerancia de llegada tardía, el valor predeterminado se establece en 5 segundos.|Pruebe a aumentar el número de SU o a paralelizar su consulta. Para más información sobre las SU, consulte [Descripción y ajuste de las unidades de streaming](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Para más información sobre la paralelización de su consulta, consulte [Aprovechamiento de la paralelización de consultas en Azure Stream Analytics](stream-analytics-parallelization.md).|
|Errores de deserialización de entrada|Mayor que|Total|0|Examine los registros de diagnóstico o actividad y realice los cambios correspondientes en la entrada. Para más información sobre los registros de diagnóstico, consulte [Solución de problemas de Azure Stream Analytics mediante registros de diagnóstico](stream-analytics-job-diagnostic-logs.md).|

## <a name="get-help"></a>Obtención de ayuda

Para más información sobre la configuración de alertas en Azure Portal, consulte el artículo sobre la [recepción de notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-get-started.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

