---
title: Solución de problemas de Application Insights en un proyecto web de Java
description: 'Guía de solución de problemas: supervisión de aplicaciones activas Java con Application Insights.'
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2018
ms.author: mbullwin
ms.openlocfilehash: eaade5f9ec9db7e8d224305147dafc264916d9c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995593"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Solución de problemas y preguntas y respuestas sobre Application Insights para Java
Preguntas o problemas relacionados con [Azure Application Insights en Java][java]. a continuación se incluyen algunas sugerencias.

## <a name="build-errors"></a>Errores de compilación
**En Eclipse o Intellij IDEA, al agregar el SDK de Application Insights a través de Maven o Gradle, obtengo errores de compilación o de validación de la suma de comprobación.**

* Si el elemento `<version>` de dependencia usa un patrón con caracteres comodín (por ejemplo, (Maven) `<version>[2.0,)</version>` o (Gradle) `version:'2.0.+'`), pruebe a especificar una versión específica en su lugar, como `2.0.1`. Consulte la [notas de la versión](https://github.com/Microsoft/ApplicationInsights-Java/releases) para la versión más reciente.

## <a name="no-data"></a>No aparecen datos
**He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no aparecen datos en el portal.**

* Espere un minuto y haga clic en Actualizar, Los gráficos se actualizan automáticamente de forma periódica, pero puede actualizarlos manualmente. El intervalo de actualización depende del intervalo de tiempo del gráfico.
* Compruebe que tiene una clave de instrumentación definida en el archivo ApplicationInsights.xml (en la carpeta de recursos del proyecto) o configurada como variable de entorno.
* Compruebe que no haya ningún nodo `<DisableTelemetry>true</DisableTelemetry>` en el archivo xml.
* En el firewall, es posible que tenga que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com. Consulte la [lista completa de excepciones del firewall](../../azure-monitor/app/ip-addresses.md)
* En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
* Active el inicio de sesión en la ventana de la consola del IDE. Para ello, agregue un elemento `<SDKLogger />` bajo el nodo raíz en el archivo ApplicationInsights.xml (en la carpeta de recursos del proyecto) y compruebe si hay entradas precedidas por la indicación AI: INFO/WARN/ERROR para cualquier registro sospechoso.
* Asegúrese de que se ha cargado correctamente el archivo ApplicationInsights.xml apropiado por parte del SDK de Java, examinando para ello los mensajes de salida de la consola para ver si hay una instrucción que haga referencia a que "el archivo de configuración se ha encontrado correctamente".
* Si no se encuentra el archivo de configuración, compruebe los mensajes de salida para ver dónde se busca el archivo de configuración, y asegúrese de que ApplicationInsights.xml se encuentra en una de las ubicaciones de búsqueda. Como regla general, puede colocar el archivo de configuración cerca de los JAR de SDK de Application Insights. Por ejemplo: en Tomcat, esto significaría la carpeta WEB-INF/classes. Durante el desarrollo, puede colocar el archivo ApplicationInsights.xml en la carpeta de recursos del proyecto web.
* Consulte también la [página de problemas de GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para ver los problemas conocidos relacionados con el SDK.
* Asegúrese de usar la misma versión de los appender de inicio de sesión, agente, web y núcleo de Application Insights para evitar tener problemas de conflictos de versiones.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Solía ver datos, pero ya no sucede esto.
* Compruebe el [blog de estado](https://blogs.msdn.com/b/applicationinsights-status/).
* ¿Ha alcanzado su cuota mensual de puntos de datos? Abra Configuración/Cuotas y Precios para averiguarlo. Si es así, puede actualizar el plan o pagar para obtener capacidad adicional. Consulte el [esquema de precios](https://azure.microsoft.com/pricing/details/application-insights/).
* ¿Ha actualizado el SDK recientemente? Asegúrese de que en el directorio del proyecto solo haya archivos JAR de SDK únicos. No debería haber dos versiones diferentes de SDK.
* ¿Busca en el recurso correcto de AI? El valor de iKey de la aplicación debe coincidir con el recurso en el que espera la telemetría. Deben ser iguales.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>No veo todos los datos que esperaba
* Abra la página Uso y costos estimados y compruebe si el [muestreo](../../azure-monitor/app/sampling.md) está en funcionamiento. (La transmisión al 100 % significa que el muestreo no está en funcionamiento). El servicio Application Insights se puede configurar para que acepte únicamente una fracción de la telemetría procedente de la aplicación. Esto le ayuda a mantenerse en su cuota mensual de telemetría.
* ¿Tiene activado el muestreo del SDK? En caso afirmativo, los datos se muestrearían a la velocidad especificada para todos los tipos aplicables.
* ¿Ejecuta una versión anterior del SDK de Java? A partir de la versión 2.0.1, hemos introducido un mecanismo de tolerancia a errores para controlar los errores intermitentes de la red y el back-end, así como la persistencia de los datos en las unidades locales.
* ¿Tiene limitaciones debido al exceso de telemetría? Si activa el registro INFO, verá el mensaje de registro "App is throttled" (La aplicación está limitada). El límite actual es de 32 000 elementos de telemetría/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>El agente de Java no puede capturar datos de dependencia
* ¿Ha configurado el agente de Java siguiendo el [agente de configuración de Java](java-agent.md)?
* Asegúrese de que el archivo JAR del agente de Java y el archivo AI-Agent.xml se coloquen en la misma carpeta.
* Asegúrese de que la dependencia que intenta recopilar automáticamente admita la recopilación automática. Actualmente solo se admiten colecciones de dependencias de MySQL, MsSQL, Oracle DB y Azure Cache for Redis.
* ¿Usa JDK 1.7 o 1.8? Actualmente no se admite la colección de dependencias en JDK 9.

## <a name="no-usage-data"></a>No aparecen datos de uso
**Veo datos sobre solicitudes y tiempos de respuesta, pero no de vista de página, del explorador o de datos de usuarios.**

La aplicación se ha configurado correctamente para enviar datos de telemetría desde el servidor. El paso siguiente consiste en [configurar las páginas web para enviar datos de telemetría desde el explorador web][usage].

Como alternativa, si el cliente es una aplicación de [teléfono o de cualquier otro dispositivo][platforms], puede enviar datos de telemetría desde este.

Use la misma clave de instrumentación para configurar la telemetría tanto de cliente como de servidor. Los datos aparecerán en el mismo recurso de Application Insights y podrá correlacionar eventos de cliente y servidor.


## <a name="disabling-telemetry"></a>Deshabilitación de la telemetría
**¿Cómo puedo deshabilitar la recopilación de telemetría?**

En el código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**O**

Actualice ApplicationInsights.xml (en la carpeta de recursos del proyecto). Agregue lo siguiente bajo el nodo raíz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Si usa el método XML, debe reiniciar la aplicación al cambiar el valor.

## <a name="changing-the-target"></a>Cambio de destino
**¿Cómo puedo cambiar el recurso de Azure al que mi proyecto envía datos?**

* [Obtenga la clave de instrumentación del nuevo recurso.][java]
* Si ha agregado Application Insights al proyecto mediante el kit de herramientas de Azure para Eclipse, haga clic con el botón derecho en el proyecto web, seleccione **Azure**, **Configurar Application Insights** y cambie la clave.
* Si ha configurado la clave de instrumentación como variable de entorno, actualice el valor de dicha variable con el nuevo valor de iKey.
* De lo contrario, actualice la clave en ApplicationInsights.xml en la carpeta de recursos del proyecto.

## <a name="debug-data-from-the-sdk"></a>Depuración de datos del SDK

**¿Cómo puedo averiguar lo que está haciendo el SDK?**

Para más información sobre lo que sucede en la API, agregue `<SDKLogger/>` bajo el nodo raíz del archivo de configuración ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

También puede indicar al registrador que lo envíe a un archivo:

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Iniciador de Spring Boot

Para habilitar el registro de SDK con aplicaciones de Spring Boot con Spring Boot Starter de Application Insights, agregue lo siguiente a la `application.properties` archivo.:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java Agent

Para habilitar la actualización de registro del agente de JVM el [archivo AI-Agent.xml](java-agent.md).

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>Pantalla de inicio de Azure
**Estoy mirando el [portal de Azure](https://portal.azure.com). ¿El mapa indica algo sobre mi aplicación?**

No, este muestra el estado de los servidores de Azure en todo el mundo.

*¿Cómo puedo encontrar datos sobre mi aplicación desde el panel de inicio de Azure (pantalla principal)?*

Suponiendo que haya [configurado la aplicación para Application Insights][java], haga clic en Examinar, seleccione Application Insights y, después, elija el recurso de aplicación que haya creado para su aplicación. Para mayor brevedad en un futuro, puede anclar la aplicación al panel de inicio.

## <a name="intranet-servers"></a>Servidores de intranet
**¿Puedo supervisar un servidor de mi intranet?**

Sí, siempre que dicho servidor pueda enviar datos de telemetría al portal de Application Insights a través de una conexión pública a Internet.

En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.

## <a name="data-retention"></a>Retención de datos
**¿Cuánto tiempo se retienen los datos en el portal? ¿Es seguro?**

Consulte [Privacidad y retención de los datos][data].

## <a name="debug-logging"></a>Registro de depuración
Application Insights usa `org.apache.http`. Se reubica en los archivos JAR principales de Application Insights en el espacio de nombres `com.microsoft.applicationinsights.core.dependencies.http`. Esto permite a Application Insights administrar los escenarios donde existen diferentes versiones del mismo módulo `org.apache.http` en un código base.

>[!NOTE]
>Si habilita los registros a nivel de DEPURACIÓN para todos los espacios de nombres en la aplicación, se respetarán en todos los módulos de ejecución, incluido `org.apache.http`, cuyo nombre ha cambiado a `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights no podrá aplicar el filtrado a estas llamadas, porque la llamada de registro la realiza la biblioteca de Apache. Los registros a nivel de DEPURACIÓN producen un volumen considerable de datos de registro y no se recomiendan para las instancias de producción en vivo.


## <a name="next-steps"></a>Pasos siguientes
**He configurado Application Insights para mi aplicación de servidor Java. ¿Qué más puedo hacer?**

* [Supervisar la disponibilidad de sus páginas web][availability]
* [Supervisar el uso de páginas web][usage]
* [Realizar el seguimiento del uso y diagnosticar los problemas en las aplicaciones de su dispositivo][platforms]
* [Escribir código para realizar un seguimiento del uso de la aplicación][track]
* [Captura de registros de diagnóstico][javalogs]

## <a name="get-help"></a>Obtención de ayuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Notificación de un problema en GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

