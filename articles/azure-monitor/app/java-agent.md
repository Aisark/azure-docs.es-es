---
title: Supervisión de rendimiento de aplicaciones web de Java en Azure Application Insights | Microsoft Docs
description: Supervisión extendida del rendimiento y el uso de su sitio web de Java con Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: af157204ad1e1b28639ae2d8f192b3122afa8147
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299239"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Supervisión de dependencias, excepciones detectadas y tiempos de ejecución del método en aplicaciones web de Java


Si ha [instrumentado la aplicación web de Java con Application Insights][java], puede usar el agente de Java para obtener información más clara, sin tener que realizar cambios de código:

* **Dependencias:** datos sobre las llamadas realizadas por la aplicación a otros componentes, por ejemplo:
  * Se capturan las **llamadas HTTP salientes** realizadas a través de Apache HttpClient, OkHttp y `java.net.HttpURLConnection`.
  * Se capturan las **llamadas Redis** realizadas a través del cliente de Jedis.
  * **Consultas JDBC**: en el caso de MySQL y PostgreSQL, si la llamada tarda más de 10 segundos, el agente notifica el plan de consulta.

* **Registro de aplicaciones**: puede capturar y poner en correlación los registros de aplicaciones con solicitudes HTTP y otra telemetría.
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **Nomenclatura mejorada para las opciones**: (se usa para la agregación de solicitudes en el portal).
  * **Spring**: se basa en `@RequestMapping`.
  * **JAX-RS**: se basa en `@Path`. 

Para usar el agente de Java, debe instalarlo en el servidor. Las aplicaciones web deben instrumentarse con el [SDK de Application Insights para Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalación del agente de Application Insights para Java
1. [Descargue el agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)en la máquina que ejecuta el servidor de Java. Asegúrese de descargar la misma versión del agente de Java que los paquetes web y principales del SDK de Application Insights para Java.
2. Edite el script de inicio del servidor de aplicaciones y agregue el siguiente argumento de JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por ejemplo, en Tomcat en un equipo Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie el servidor de aplicaciones.

## <a name="configure-the-agent"></a>Configuración del agente
Cree un archivo denominado `AI-Agent.xml` y colóquelo en la misma carpeta que el archivo JAR del agente.

Establezca el contenido del archivo XML. Edite el ejemplo siguiente para incluir u omitir las características que desee.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Configuración adicional (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para Azure App Service, haga lo siguiente:

* Seleccione Configuración > Configuración de la aplicación.
* En Configuración de la aplicación, agregue un nuevo par clave-valor:

Clave: `JAVA_OPTS` Valor: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Para conocer la última versión del agente de Java, consulte [aquí](https://github.com/Microsoft/ApplicationInsights-Java/releases
) las versiones. 

El agente debe estar empaquetado como un recurso en el proyecto de forma que termine en el directorio D:/home/site/wwwroot/. Para confirmar que el agente está en el directorio correcto de App Service, vaya a **Herramientas de desarrollo** > **Herramientas avanzadas** > **Consola de depuración** y examine el contenido del directorio del sitio.    

* Guarde la configuración y reinicie la aplicación. (Estos pasos solo se aplican a las instancias de App Services que se ejecutan en Windows).

> [!NOTE]
> AI-Agent.xml y el archivo jar del agente deben estar en la misma carpeta. A menudo se colocan juntos en la carpeta `/resources` del proyecto.  

#### <a name="enable-w3c-distributed-tracing"></a>Habilitación del seguimiento distribuido de W3C

Agregue lo siguiente a AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> El modo de compatibilidad con versiones anteriores está habilitado de forma predeterminada, y el parámetro enableW3CBackCompat es opcional y debe usarse solo cuando quiera desactivarlo. 

Lo ideal sería cuando todos los servicios se han actualizado a la versión más reciente de los SDK compatibles con el protocolo W3C. Se recomienda encarecidamente pasar a la versión más reciente de los SDK con compatibilidad con W3C lo antes posible.

Asegúrese de que **las configuraciones (del agente) tanto [entrantes](correlation.md#w3c-distributed-tracing) como salientes** sean exactamente iguales.

## <a name="view-the-data"></a>Visualización de los datos
En el recurso de Application Insights, aparecen tiempos de ejecución agregados de métodos y dependencias remotos [en el icono Rendimiento][metrics].

Para buscar instancias individuales de informes de dependencia, excepción y método, abra [Buscar][diagnostic].

[Más información sobre diagnósticos de problemas de dependencia](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>¿Tiene preguntas? ¿Tiene problemas?
* ¿No hay datos? [Establezca excepciones del firewall](../../azure-monitor/app/ip-addresses.md)
* [Solución de problemas de Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
