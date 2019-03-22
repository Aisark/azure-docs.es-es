---
title: Establecimiento de alertas en Azure Application Insights | Microsoft Docs
description: Reciba notificaciones acerca de tiempos de respuesta lentos, excepciones y otros cambios de rendimiento o uso de la aplicación web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: c1165fcebf8382d30b1be86f102da78ef0a4ac9a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244373"
---
# <a name="set-alerts-in-application-insights"></a>Definición de alertas en Application Insights
[Azure Application Insights][start] puede avisarle sobre los cambios en las métricas de rendimiento o de uso de la aplicación web. 

Application Insights supervisa la aplicación activa en una [amplia variedad de plataformas][platforms] para ayudarlo a diagnosticar problemas de rendimiento y entender los patrones de uso.

Hay varios tipos de alertas:

* [**Alertas de métricas** ](../../azure-monitor/platform/alerts-metric-overview.md) le avisan cuando una métrica cruza un valor de umbral durante un cierto período - como tiempos de respuesta, recuentos de excepciones, uso de CPU o vistas de página.
* [**Alertas de registro** ](../../azure-monitor/platform/alerts-unified-log.md) se usa para describir aquellas alertas que la señal de alerta se basa en una consulta de Kusto personalizada.
* [**Pruebas web**][availability] le informan de que el sitio no está disponible en Internet o responde lentamente. [Más información][availability].
* [**Diagnósticos proactivos**](../../azure-monitor/app/proactive-diagnostics.md) se configuran automáticamente para que le notifiquen acerca de patrones de rendimiento no habituales.

## <a name="set-a-metric-alert"></a>Establecimiento de una alerta de métrica
Abra la ficha reglas de alerta y, a continuación, utilice el botón Agregar.

![En la ficha reglas de alerta, elija Agregar alerta. Establezca la aplicación como el recurso que se va a medir, proporcione un nombre para la alerta y elija una métrica.](./media/alerts/01-set-metric.png)

* Establezca el recurso antes de las demás propiedades. **Elija el recurso "(components)"** si desea establecer alertas sobre métricas de rendimiento o de uso.
* El nombre que asigne a la alerta debe ser único dentro del grupo de recursos (no solo en la aplicación).
* Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.
* Si activa la casilla "Enviar correo electrónico a propietarios, colaboradores y lectores", las alertas se envían por correo electrónico a todos los usuarios con acceso a este grupo de recursos. Para expandir dicho conjunto de usuarios, agréguelos al [grupo de recursos o suscripción](../../azure-monitor/app/resources-roles-access-control.md) (no al recurso).
* Si especifica "Correos electrónicos adicionales", las alertas se envían a esos usuarios o grupos (independientemente de que haya activado la casilla anterior). 
* Establezca una [dirección de webhook](../../azure-monitor/platform/alerts-webhooks.md) si ha configurado una aplicación web para responder a las alertas. Se llamará a esta dirección cuando la alerta se active y cuando se haya resuelto. (Pero tenga en cuenta que, en la actualidad, los parámetros de consulta no se pasan como propiedades de webhook).
* Puede deshabilitar o habilitar la alerta: consulte los botones en la parte superior.

*No puedo ver el botón Agregar alerta*

* ¿Está usando una cuenta de organización? Puede establecer alertas si tiene acceso de propietario o colaborador a este recurso de aplicación. Eche un vistazo a la pestaña de Control de acceso. [Más información sobre el control de acceso][roles].

> [!NOTE]
> En la hoja de alertas, verá que ya hay una alerta configurada: [Proactive Diagnostics](../../azure-monitor/app/proactive-failure-diagnostics.md) La alerta automática supervisa una métrica determinada: el índice de errores de las solicitudes. A menos que decida deshabilitar esta opción, no es necesario establecer su propio índice de errores de las solicitudes.
> 
> 

## <a name="see-your-alerts"></a>Visualización de alertas
Recibirá un correo electrónico cuando un alerta cambia el estado entre inactivo y activo. 

El estado actual de cada alerta se muestra en la ficha reglas de alerta.

Hay un resumen de la actividad reciente en la lista desplegable de alertas:

![Lista desplegable de alertas](./media/alerts/010-alert-drop.png)

El historial de cambios de estado está en el registro de actividad:

![En la pestaña información general, haga clic en configuración, registros de auditoría](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Funcionamiento de las alertas
* Una alerta tiene tres estados: nunca activada, activada y resuelta. "Activada" significa que la condición especificada tenía el valor true cuando se evaluó por última vez.
* Se genera una notificación cuando una alerta cambia de estado. (Si la condición de alerta ya tenía el valor true cuando creó la alerta, es posible que no reciba una notificación hasta que la condición cambie al valor false).
* Si ha activado la casilla de correos electrónicos o si ha proporcionado direcciones de correo electrónico, cada notificación generará un correo. También puede consultar la lista desplegable de notificaciones.
* Una alerta se evalúa cada vez que llega una métrica, pero no en caso contrario.
* La evaluación agrega la métrica durante el período anterior y, luego, la compara con el umbral para determinar el nuevo estado.
* El período que elija especifica el intervalo en el que se agregan métricas. No afecta a la frecuencia con la que se evalúa la alerta: depende de la frecuencia de llegada de métricas.
* Si no llega ningún dato para una métrica concreta durante un tiempo, este intervalo tiene efectos diferentes en la evaluación de la alerta y en los gráficos del Explorador de métricas. En el Explorador de métricas, si no se ve ningún dato durante más tiempo que el intervalo de muestreo del gráfico, el gráfico mostrará un valor de 0. Pero una alerta basada en la misma métrica no se volverá a evaluar, y el estado de la alerta permanecerá sin cambios. 
  
    Cuando finalmente llegan datos, el gráfico se desplazará a un valor distinto de cero. La alerta se evaluará en función de los datos disponibles para el período especificado. Si el nuevo punto de datos es el único disponible en el período, el agregado se basará en ese punto.
* Una alerta puede parpadear con frecuencia entre los estados de alerta y correcto, incluso si se establece un período largo. Esto puede suceder si el valor de métrica se sitúa alrededor del umbral. No hay ninguna histéresis en el umbral: la transición a alerta se produce en el mismo valor que la transición a correcto.

## <a name="what-are-good-alerts-to-set"></a>¿Qué alertas es conveniente establecer?
Depende de la aplicación. Para empezar, es mejor no establecer demasiadas métricas. Observe durante un tiempo sus gráficos de métrica mientras se ejecuta la aplicación para hacerse una idea de cómo se comporta normalmente. Este procedimiento le ayudará a encontrar maneras de mejorar su rendimiento. A continuación, configure alertas para que le avisen cuando las métricas salgan de la zona normal. 

Las alertas más populares son:

* Las [métricas del explorador][client], especialmente los **tiempos de carga de página del explorador**, son buenas para aplicaciones web. Si la página tiene muchos scripts, debe buscar **excepciones del explorador**. Para obtener estas métricas y alertas, tiene que configurar [la supervisión de páginas web][client].
* **Tiempo de respuesta del servidor** para las aplicaciones web del lado servidor. Además de configurar alertas, eche un vistazo a esta métrica para ver si varía desproporcionadamente con tasas de solicitud altas: la variación puede indicar que la aplicación se está quedando sin recursos. 
* **Excepciones de servidor** : para verlas, deberá realizar alguna [configuración adicional](../../azure-monitor/app/asp-net-exceptions.md).

No olvide que los [diagnósticos proactivos de frecuencia de errores](../../azure-monitor/app/proactive-failure-diagnostics.md) permiten supervisar automáticamente la velocidad a la que la aplicación responde a solicitudes con códigos de error.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Cómo establecer una alerta de excepción con búsqueda de registros personalizado

En esta sección, veremos cómo establecer una alerta de excepción basado en consultas. En este ejemplo, supongamos que queremos una alerta cuando la tasa de error es superior al 10% en las últimas 24 horas.

1. Vaya al recurso de Application Insights en Azure portal.
2. En el lado izquierdo, en configurar haga clic en **alerta**.

    ![Configuración de la izquierda, en haga clic en alerta](./media/alerts/1appinsightalert.png)

3. En la parte superior de la pestaña alerta, seleccione **nueva regla de alerta**.

     ![En la parte superior de la pestaña alerta, haga clic en nueva regla de alerta](./media/alerts/2createalert.png)

4. El recurso debe ser seleccionada automáticamente. Para establecer una condición, haga clic en **Agregar condición**.

    ![Haga clic en Agregar condición](./media/alerts/3addcondition.png)

5. En la pestaña configurar de la lógica de señal seleccione **búsqueda de registros personalizada**

    ![Haga clic en la búsqueda de registros personalizado](./media/alerts/4customlogsearch.png)

6. En la pestaña búsqueda de registro personalizado, escriba la consulta en el cuadro "Búsqueda". En este ejemplo, usaremos la siguiente consulta de Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Escriba la consulta en el cuadro consulta de búsqueda](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > También puede aplicar estos pasos para otros tipos de alertas basadas en consultas. Puede aprender más sobre el lenguaje de consulta Kusto de este [Kusto Introducción doc](https://docs.microsoft.com/azure/kusto/concepts/) o esto [hoja de referencia rápida SQL a Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. En "Alert logic," Elija si se basa en el número de resultados o unidades métricas. A continuación, seleccione la condición (mayor que, igual a, menor que) y un umbral. Mientras que va a cambiar estos valores, es posible que observe los cambios de frase de vista previa de condición. En este ejemplo usamos "igual a".

    ![En la lógica de alerta elegir entre las opciones proporcionadas para según y la condición y, a continuación, escriba un umbral](./media/alerts/6alertlogic.png)

8. En "Evaluado según", establezca el período y la frecuencia. El período de aquí debe coincidir con el valor que se coloca de período en la consulta anterior. A continuación, haga clic en **realiza**.

    ![Establecer el período y la frecuencia en la parte inferior y, a continuación, haga clic en listo](./media/alerts/7evaluate.png)

9. Ahora vemos la condición que hemos creado con el costo mensual estimado. A continuación en ["Grupos de acciones"](../platform/action-groups.md) puede crear un grupo nuevo o seleccione uno existente. Si lo desea, puede personalizar las acciones.

    ![Haga clic en el, seleccione o cree botones en el grupo de acciones](./media/alerts/8actiongroup.png)

10. Por último, agregue los detalles de alerta (nombre de la regla, la descripción, la gravedad de alerta). Cuando haya terminado, haga clic en **crear regla de alertas** en la parte inferior.

    ![En detalle de alertas, escriba el nombre de regla de alerta, escribir una descripción y elegir un nivel de gravedad ](./media/alerts/9alertdetails.png)

## <a name="who-receives-the-classic-alert-notifications"></a>¿Quién recibe las notificaciones de alerta (clásicas)?

Esta sección solo se aplica a las alertas clásicas y le ayudará a optimizar las notificaciones de alerta para asegurarse de que solo reciban las notificaciones los destinatarios que elija. Para más información sobre la diferencia entre [alertas clásicas](../platform/alerts-classic.overview.md) y las alertas de la nueva experiencia, consulte el [artículo de información general de alertas](../platform/alerts-overview.md). Para controlar la notificación de alerta en la nueva experiencia de alertas, utilice [grupos de acciones](../platform/action-groups.md).

* Se recomienda el uso de destinatarios específicos para las notificaciones de alerta clásicas.

* Para alertas basadas en alguna métrica de Application Insights (incluidas las métricas de disponibilidad), la opción de casilla de verificación **masiva o grupo**, si está habilitada, se envía a los usuarios con roles de propietario, colaborador o lector de la suscripción. De hecho, _todos_ los usuarios con acceso a la suscripción del recurso de Application Insights están dentro del ámbito y recibirán notificaciones.

> [!NOTE]
> Si actualmente usa la opción de casilla de verificación **masiva o grupo** y la deshabilita, no podrá revertir el cambio.

Use la nueva experiencia de alertas o las alertas prácticamente en tiempo real si tiene que enviar notificaciones a los usuarios según sus roles. Con los [grupos de acciones](../platform/action-groups.md), puede configurar el envío de notificaciones por correo electrónico a usuarios con cualquiera de los roles de propietario, colaborador o lector (no combinados conjuntamente como única opción).

## <a name="automation"></a>Automation
* [Uso de PowerShell para automatizar la configuración de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Uso de Webhook para automatizar la respuesta a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Vea también
* [Pruebas web de disponibilidad](../../azure-monitor/app/monitor-web-app-availability.md)
* [Use PowerShell to set alerts in Application Insights (Uso de PowerShell para definir alertas en Application Insights)](../../azure-monitor/app/powershell-alerts.md)
* [Proactive diagnostics](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

