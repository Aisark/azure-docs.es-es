---
title: 'Detección inteligente: anomalías de error en Application Insights | Microsoft Docs'
description: Alerta de cambios no habituales en la frecuencia de solicitudes con errores a la aplicación web y proporciona análisis del diagnóstico. No necesita ninguna configuración.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 46944603fdf45a2a7a14641086959bf61b3f773e
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465880"
---
# <a name="smart-detection---failure-anomalies"></a>Detección inteligente: anomalías de error
[Application Insights](../../azure-monitor/app/app-insights-overview.md) le notifica automáticamente casi en tiempo real si la aplicación web sufre un aumento anómalo en la frecuencia de solicitudes erróneas. Asimismo, detecta un aumento inusual de la tasa de solicitudes HTTP o llamadas de dependencia notificadas como errores. En el caso de las solicitudes, las solicitudes con error suelen ser aquellas con códigos de respuesta de 400 o superiores. Para ayudarle a evaluar las prioridades y a diagnosticar el problema, en la notificación se proporciona un análisis de las características de los errores y la telemetría relacionada. También hay vínculos en el portal de Application Insights para obtener un diagnóstico más amplio. La característica no necesita ninguna instalación o configuración, ya que usa algoritmos de aprendizaje automático para predecir la tasa normal de errores.

Esta característica funciona para cualquier aplicación web, hospedada en la nube o en sus propios servidores, que genera telemetría de dependencia o de solicitud, por ejemplo, si tiene un rol de trabajo que llama a [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) o a [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Después de configurar [Application Insights para su proyecto](../../azure-monitor/app/app-insights-overview.md) y siempre que la aplicación genere una cantidad mínima determinada de datos de telemetría, la detección inteligente de anomalías de errores tarda 24 horas en aprender el comportamiento normal de la aplicación antes de que se active y se puedan enviar alertas.

Esta es una alerta de ejemplo.

![Alerta de ejemplo de detección inteligente que muestra el análisis en torno al error](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> De forma predeterminada, obtendrá un correo de formato más corto que este ejemplo. Pero puede [cambiar a este formato detallado](#configure-alerts).
>
>

Observe que le indica:

* La tasa de errores en comparación con el comportamiento normal de la aplicación.
* Cuántos usuarios se ven afectados (para darle una idea de la gravedad).
* Un patrón característico relacionado con los errores. En este ejemplo encontrará un código de respuesta, un nombre de solicitud (operación) y una versión de aplicación concretos. Esto le indica inmediatamente por dónde empezar a buscar en el código. Otras posibilidades pueden ser un sistema operativo cliente o explorador específicos.
* Las excepciones, seguimientos de registros y errores de dependencias (bases de datos u otros componentes externos) que parecen estar asociados con los errores caracterizados.
* Los vínculos directos a búsquedas significativas en los datos de telemetría en Application Insights.

## <a name="failure-anomalies-v2"></a>Anomalías en los errores v2
Ya está disponible una versión nueva de la regla de alertas Anomalías en los errores. Esta versión nueva se ejecuta en la nueva plataforma de alertas de Azure y presenta una variedad de mejoras respecto de la versión existente.

### <a name="whats-new-in-this-version"></a>Novedades de esta versión
- Detección de problemas más rápida
- Un conjunto más completo de acciones: la regla de alertas está creada con un [grupo de acciones](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) asociado denominado "Detección inteligente de Application Insights" que contiene acciones de correo electrónico y webhook y se puede extender para desencadenar acciones adicionales cuando se activa la alerta.
- Notificaciones más centradas: las notificaciones por correo electrónico que se envían desde esta regla de alertas ahora se envían de manera predeterminada a los usuarios asociados con los roles Lector de supervisión y Colaborador de supervisión de la suscripción. Encontrará más información sobre esto [aquí](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- Configuración más sencilla a través de las plantillas de Resource Manager, consulte un ejemplo [aquí](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Compatibilidad con el esquema de alertas comunes: las notificaciones enviadas desde esta regla de alertas siguen el [esquema de alertas comunes](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Plantilla unificada de correo electrónico: las notificaciones por correo electrónico desde esta regla de alertas tienen un aspecto coherente con el de otros tipos de alertas. Con este cambio, ya no está disponible la opción de recibir alertas de Anomalías en los errores con información de diagnóstico detallada.

### <a name="how-do-i-get-the-new-version"></a>¿Cómo puedo obtener la versión nueva?
- Los recursos de Application Insights recién creados ahora están aprovisionados con la versión nueva de la regla de alertas Anomalías en los errores.
- Los recursos existentes de Application Insights con la versión clásica de la regla de alertas Anomalías en los errores recibirán la versión nueva una vez que la suscripción de hospedaje se migre a la plataforma de alertas nueva como parte del [proceso de retiro de las alertas clásicas](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> La versión nueva de la regla de alertas Anomalías en los errores sigue siendo gratuita. Además, las acciones de correo electrónico y webhook desencadenadas por el grupo de acción "Detección inteligente de Application Insights" asociado también son gratis.
> 
> 

## <a name="benefits-of-smart-detection"></a>Ventajas de la detección inteligente
Las [alertas de métricas](../../azure-monitor/app/alerts.md) normales le comunican que puede haber un problema. Pero la detección inteligente inicia el trabajo de diagnóstico y realiza muchos de los análisis que, de otra forma, tendría que hacer usted mismo. Los resultados se le presentan claramente organizados, lo que le ayuda a llegar rápidamente a la raíz del problema.

## <a name="how-it-works"></a>Cómo funciona
La detección inteligente supervisa la telemetría recibida de su aplicación y, en particular, las tasas de errores. Esta regla cuenta el número de solicitudes para el que la propiedad `Successful request` es falsa y el número de llamadas de dependencia para el que la propiedad `Successful call` es falsa. De manera predeterminada, para las solicitudes, `Successful request == (resultCode < 400)` (a no ser que haya escrito código personalizado para [filtrar](../../azure-monitor/app/api-filtering-sampling.md#filtering) o generar sus propias llamadas de [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)). 

El rendimiento de una aplicación tiene un patrón típico de comportamiento. Algunas solicitudes o llamadas de dependencia son más propensas a errores que otras; y la tasa de error general puede aumentar a medida que aumenta la carga. La detección inteligente usa aprendizaje automático para encontrar estas anomalías.

A medida que Application Insights recibe telemetría de su aplicación web, la detección inteligente compara el comportamiento actual con los patrones vistos a lo largo de los últimos días. Si se observa un incremento anómalo de la tasa de errores en comparación con el rendimiento previo, se desencadena un análisis.

Cuando se desencadena un análisis, el servicio realiza un análisis del clúster en la solicitud errónea, para tratar de identificar un patrón de valores que caracterice los errores. En el ejemplo anterior, el análisis ha detectado que la mayoría de los errores giran en torno a un código de resultado, nombre de solicitud, host de URL de servidor e instancia de rol específicos. Por el contrario, el análisis ha descubierto que la propiedad del sistema operativo de cliente se distribuye en varios valores y, por lo tanto, no se muestra.

Si se instrumenta el servicio con estas llamadas de telemetría, el analizador busca una excepción y un error de dependencia que estén asociados a solicitudes del clúster que identificó, junto con un ejemplo de cualquier registro de seguimiento asociado a estas solicitudes.

El análisis resultante se le envía como una alerta, a no ser que configurara lo contrario.

Al igual que sucede con las [alertas que establece manualmente](../../azure-monitor/app/alerts.md), puede inspeccionar el estado de la alerta y configurarla en la hoja Alertas del recurso de Application Insights. Pero, a diferencia de otras alertas, no es necesario instalar ni configurar la detección inteligente. Si lo desea, puede deshabilitarla o cambiar sus direcciones de correo electrónico de destino.

### <a name="alert-logic-details"></a>Detalles de la lógica de alerta

Las alertas se desencadenan por nuestro algoritmo de aprendizaje automático propietario, por lo que no se pueden compartir los detalles exactos de la implementación. Dicho esto, entendemos que a veces se necesita saber más acerca de cómo funciona la lógica subyacente. Los principales factores que se evalúan para determinar si se debe desencadenar una alerta son: 

* Análisis del porcentaje de errores de las solicitudes o dependencias en una ventana de tiempo adaptable de 20 minutos.
* Una comparación del porcentaje de errores de los últimos 20 minutos con la tasa de los últimos 40 minutos y los últimos siete días, y la búsqueda de desviaciones significativas que superen X veces esa desviación estándar.
* Al utilizar un límite adaptativo para el porcentaje mínimo de errores, que varía en función del volumen de solicitudes o dependencias de la aplicación.

## <a name="configure-alerts"></a>Configurar alertas
Puede deshabilitar la detección inteligente, cambiar los destinatarios de correo electrónico, crear un proyecto de webhook u optar por recibir mensajes de alerta más detallados.

Abra la página Alertas. Se incluyen anomalías de errores junto con alertas que se han establecido manualmente, y puede ver si están actualmente en el estado de alerta.

![En la página de información general, haga clic en el icono Alertas. O bien, en cualquier página de métricas, haga clic en el botón Alertas.](./media/proactive-failure-diagnostics/021.png)

Haga clic en la alerta para configurarla.

![Configuración](./media/proactive-failure-diagnostics/032.png)

Observe que puede deshabilitar la detección inteligente, pero no puede eliminarla (ni crear otra).

#### <a name="detailed-alerts"></a>Alertas detalladas
Si selecciona "Obtener diagnósticos detallados", el correo electrónico contendrá más información de diagnóstico. A veces podrá diagnosticar el problema simplemente con los datos del correo electrónico.

Existe un pequeño riesgo de que la alerta más detallada contenga información confidencial, ya que incluye mensajes de excepciones y seguimientos. Sin embargo, esto solo podría ocurrir si el código permitiera información confidencial en esos mensajes.

## <a name="triaging-and-diagnosing-an-alert"></a>Clasificación y diagnóstico de una alerta
Una alerta indica que se detectó un aumento anómalo de la tasa de solicitudes con errores. Es probable que haya algún problema con la aplicación o con su entorno.

Puede determinar la urgencia del problema a partir del porcentaje de solicitudes y del número de usuarios afectados. En el ejemplo anterior, la tasa de error del 22.5 % se compara con una tasa normal del 1 %, lo que indica que hay algo que no va bien. Por otro lado, solo 11 usuarios se vieron afectados. Si se tratara de su aplicación, podría evaluar la importancia que tiene el problema.

En muchos casos, podrá diagnosticar el problema rápidamente a partir del nombre de la solicitud, las excepciones, los errores de dependencias y otros datos de seguimiento proporcionados.

Hay algunas otras pistas. Por ejemplo, la tasa de error de dependencia en este ejemplo es la misma que la tasa de excepción (89,3 %). Esto sugiere que la excepción se produce directamente desde el error de dependencia, lo que le da una idea clara de dónde puede empezar a buscar en el código.

Pero si necesita investigar más, los vínculos de cada sección le llevarán directamente a una [página de búsqueda](../../azure-monitor/app/diagnostic-search.md) filtrada por las solicitudes, la excepción, la dependencia o el seguimiento correspondientes. O puede abrir el [Portal de Azure](https://portal.azure.com), navegar hasta el recurso de Application Insights de su aplicación y abrir la hoja Errores.

En este ejemplo, si hace clic en el vínculo "Ver los detalles de los errores de dependencia", se abre la hoja de búsqueda de Application Insights. Muestra la instrucción SQL que incluye un ejemplo de la causa raíz: Los valores NULL se proporcionaron en los campos obligatorios y no superó la validación durante la operación de guardado.

![Búsqueda de diagnóstico](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Revisar las alertas recientes

Haga clic en **Detección inteligente** para ir a la alerta más reciente:

![Resumen de alertas](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>¿Cuál es la diferencia ...
La detección inteligente de anomalías de errores complementa otras características similares pero distintas de Application Insights.

* Las [alertas de métricas](../../azure-monitor/app/alerts.md) las configura el usuario y pueden supervisar una amplia variedad de métricas, como el uso de la CPU, la velocidad máxima de las solicitudes, los tiempos de carga de las páginas, etc. Puede usarlas para avisarle, por ejemplo, de si necesita agregar más recursos. Por el contrario, la detección inteligente de anomalías de errores abarca un pequeño conjunto de métricas críticas (actualmente solo la tasa de solicitudes con errores), diseñadas para notificarle casi en tiempo real el momento en el que la tasa de solicitudes con errores de la aplicación web aumenta significativamente en comparación con el comportamiento normal de esta.

    La detección inteligente ajusta automáticamente su umbral en respuesta a condiciones existentes.

    La detección inteligente inicia el trabajo de diagnóstico automáticamente.
* La [detección inteligente de anomalías de rendimiento](proactive-performance-diagnostics.md) también usa la inteligencia automática para detectar patrones inusuales en las métricas y no requiere ninguna configuración por parte del usuario. Pero a diferencia de la detección inteligente de anomalías de errores, el objetivo de la detección inteligente de anomalías de rendimiento es encontrar segmentos del colector de uso que pudieran haberse servidor incorrectamente, por ejemplo, por páginas específicas de un tipo específico de explorador. El análisis se realiza diariamente y, si se encuentra algún resultado, probablemente sea mucho menos urgente que una alerta. Por el contrario, el análisis de anomalías de errores se realiza continuamente sobre la telemetría entrante, y se le notificará en unos minutos si las tasas de errores del servidor son mayores de lo esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Si recibe una alerta de detección inteligente
*¿Por qué he recibido esta alerta?*

* Hemos detectado un aumento anómalo en la tasa de solicitudes con errores en comparación con la línea de base normal del período anterior. Después de analizar los errores y la telemetría asociada, creemos que hay un problema que debe examinar.

*¿La notificación significa que tengo definitivamente un problema?*

* Intentamos alertarle sobre las interrupciones o la degradación de la aplicación, aunque solo usted puede entender totalmente la semántica y el impacto en la aplicación o los usuarios.

*¿Entonces están mirando mis datos?*

* No. El servicio es completamente automático. Solo obtendrá las notificaciones. Los datos son [privados](../../azure-monitor/app/data-retention-privacy.md).

*¿Es necesario suscribirse a esta alerta?*

* No. Cada aplicación que envía telemetría de solicitud tiene la regla de alerta de detección inteligente.

*¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*

* Sí, en Reglas de alerta, haga clic en la regla de detección inteligente para configurarla. Puede deshabilitar la alerta o cambiar a los destinatarios de la misma.

*Perdí el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*

* En los registros de actividad. En Azure, abra el recurso de Application Insights correspondiente a su aplicación y luego seleccione los registros de actividad.

*Algunas de las alertas se refieren a problemas conocidos y no deseo recibirlas.*

* Tenemos pendiente la incorporación de la supresión de alertas.

## <a name="next-steps"></a>Pasos siguientes
Estas herramientas de diagnóstico lo ayudarán a inspeccionar los datos de telemetría de su aplicación:

* [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md)
* [Explorador de búsqueda](../../azure-monitor/app/diagnostic-search.md)
* [Analytics: Lenguaje de consulta eficaz](../../azure-monitor/log-query/get-started-portal.md)

Las detecciones inteligentes son completamente automáticas. Pero ¿quizás le gustaría configurar algunas alertas más?

* [Alertas de métricas configuradas manualmente](../../azure-monitor/app/alerts.md)
* [Pruebas web de disponibilidad](../../azure-monitor/app/monitor-web-app-availability.md)
