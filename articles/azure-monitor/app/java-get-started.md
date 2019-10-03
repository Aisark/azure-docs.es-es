---
title: Análisis de aplicaciones web de Java con Azure Application Insights | Microsoft Docs
description: 'Supervisión del rendimiento de aplicaciones web de Java con Application Insights. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: a6e8187a085d637ad3abc650daf15d92b96755a3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338108"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Introducción a Application Insights en un proyecto web de Java

[Application Insights](https://azure.microsoft.com/services/application-insights/) es un servicio de análisis extensible para desarrolladores web que ayuda a comprender el rendimiento y el uso de la aplicación activa. Úsela para [instrumentar automáticamente solicitudes, realizar un seguimiento de dependencias y recopilar contadores de rendimiento](auto-collect-dependencies.md#java), diagnosticar problemas de rendimiento y excepciones y [escribir código][api] para realizar un seguimiento de lo que hacen los usuarios con la aplicación. 

![Captura de pantalla de información general con datos de ejemplo](./media/java-get-started/overview-graphs.png)

Application Insights es compatible con aplicaciones Java que se ejecutan en Linux, Unix o Windows.

Necesita:

* Java 7 o posterior
* Una suscripción a [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obtención de una clave de instrumentación de Application Insights
1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com).
2. Cree un recurso en Application Insights. Establezca el tipo de aplicación a una aplicación web de Java.

3. Busque la clave de instrumentación del nuevo recurso. Pronto tendrá que pegarla en el proyecto de código.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Incorporación del SDK de Application Insights para Java al proyecto
*Elija la forma adecuada para su proyecto.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Si usa Maven... <a name="maven-setup" />
Si su proyecto ya se ha configurado para usar Maven para la compilación, combine el siguiente código en el archivo pom.xml.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Si usa Gradle... <a name="gradle-setup" />
Si su proyecto ya se ha configurado para usar Grade para la compilación, combine el siguiente código al archivo build.gradle.

A continuación, actualice las dependencias del proyecto, para obtener los archivos binarios descargados.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Por el contrario, si va a administrar manualmente las dependencias...
Descargue el [versión más reciente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), copie los archivos necesarios en el proyecto y reemplace todos los de las versiones anteriores.

### <a name="questions"></a>Preguntas...
* *¿Cuál es la relación entre los componentes `-web-auto`, `-web` y `-core`?*
  * `applicationinsights-web-auto` proporciona métricas que realizan el seguimiento de los recuentos de solicitudes de servlet HTTP y los tiempos de respuesta, mediante el registro automático del filtro de servlet de Application Insights en tiempo de ejecución.
  * `applicationinsights-web` también proporciona métricas que realizan el seguimiento de los recuentos de solicitudes de servlet HTTP y los tiempos de respuesta, pero requiere el registro manual del filtro de servlet de Application Insights en la aplicación.
  * `applicationinsights-core` solo proporciona la API básica, por ejemplo, si la aplicación no se basa en servlet.
  
* *¿Cómo se debe actualizar el SDK a la versión más reciente?*
  * Si usa Gradle o Maven...
    * Actualice el archivo de compilación para especificar la última versión.
  * Si va a administrar las dependencias manualmente...
    * Descargue el [SDK más reciente de Application Insights para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) y sustituya los antiguos. Los cambios se describen en las [notas de la versión de SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Adición del archivo ApplicationInsights.xml
Agregue ApplicationInsights.xml a la carpeta de recursos del proyecto o asegúrese de que se agrega a la ruta de acceso de la clase de implementación del proyecto. Copie en ella el siguiente XML.

Sustituya la clave de instrumentación que obtuvo en el portal de Azure.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Si lo desea, el archivo de configuración puede residir en cualquier ubicación a la que pueda acceder su aplicación.  La propiedad del sistema `-Dapplicationinsights.configurationDirectory` especifica el directorio que contiene ApplicationInsights.xml. Por ejemplo, un archivo de configuración ubicado en `E:\myconfigs\appinsights\ApplicationInsights.xml` se configuraría con la propiedad `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* La clave de instrumentación se envía junto con todos los elementos de telemetría e indica a Application Insights que se muestre en el recurso.
* El componente de la solicitud HTTP es opcional. Envía automáticamente telemetría sobre las solicitudes y tiempos de respuesta en el portal.
* La correlación de eventos es una incorporación al componente de la solicitud HTTP. Asigna un identificador a cada solicitud recibida por el servidor y agrega este identificador como propiedad a todos los elementos de telemetría como la propiedad 'Operation.Id'. Le permite correlacionar la telemetría asociada a cada solicitud estableciendo un filtro en la [búsqueda de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternativas para establecer la clave de instrumentación
SDK de Application Insights busca la clave en este orden:

1. Propiedad del sistema: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Variable de entorno: APPINSIGHTS_INSTRUMENTATIONKEY
3. Archivo de configuración: ApplicationInsights.xml

También se puede [configurar en el código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Adición del agente

[Instale el agente de Java](java-agent.md) para capturar llamadas HTTP salientes, consultas de JDBC y registros de aplicaciones y mejorar la nomenclatura de las operaciones.

## <a name="5-run-your-application"></a>5. Ejecución de la aplicación
Ejecútela en modo de depuración en el equipo de desarrollo o bien publíquela en el servidor.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Visualización de la telemetría en Application Insights
Vuelva al recurso de Application Insights en el [Portal de Microsoft Azure](https://portal.azure.com).

Los datos de las solicitudes HTTP aparecen en la hoja de información general. (Si todavía no está ahí, espere unos segundos y, a continuación, haga clic en Actualizar).

![Captura de pantalla de información general con datos de ejemplo](./media/java-get-started/overview-graphs.png)

[Más información acerca de las métricas.][metrics]

Haga clic en cualquier gráfico para ver métricas agregadas más detalladas.

![Panel de errores de Application Insights con gráficos](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Datos de instancia
Haga clic en un tipo de solicitud específico para ver las instancias individuales.

![Profundización en una vista de ejemplo concreta](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Análisis: Lenguaje de consulta eficaz
A medida que acumula más datos, puede ejecutar consultas tanto para agregar datos como para buscar instancias individuales.  [Analytics](../../azure-monitor/app/analytics.md) es una eficaz herramienta tanto para conocer el rendimiento y el uso, como para el diagnóstico.

![Ejemplo de Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalación de la aplicación en el servidor
Ahora puede publicar la aplicación en el servidor, dejar que la utilicen los usuarios y ver la telemetría en el portal.

* Asegúrese de que el firewall permite que la aplicación envíe datos de telemetría a estos puertos:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Si el tráfico saliente debe enrutarse a través de un firewall, defina las propiedades de sistema `http.proxyHost` y `http.proxyPort`.

* En los servidores de Windows, instale:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Este componente habilita los contadores de rendimiento.)

## <a name="azure-app-service-config-spring-boot"></a>Configuración de Azure App Service (Spring Boot)

Las aplicaciones de Spring Boot que se ejecutan en Windows requieren configuración adicional para ejecutarse en Azure App Services. Modifique **web.config** y agregue lo siguiente:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Excepciones y errores de solicitud
El filtro web de Application Insights recopila automáticamente las excepciones no controladas y las solicitudes con error.

Para recopilar datos de otras excepciones, puede [insertar llamadas a trackException() en el código][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Supervisión de llamadas a métodos y dependencias externas
[Instale el agente de Java](java-agent.md) para registrar los métodos internos especificados y las llamadas realizadas a través de JDBC, con datos de tiempo.

También sirve para la nomenclatura automática de las operaciones.

## <a name="w3c-distributed-tracing"></a>Seguimiento distribuido de W3C

El SDK de Java de Application Insights ahora admite el [seguimiento distribuido de W3C](https://w3c.github.io/trace-context/).

La configuración del SDK de entrada se explica más en detalle en nuestro artículo sobre [correlación](correlation.md#telemetry-correlation-in-the-java-sdk).

La configuración del SDK de salida se define en el archivo [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>contadores de rendimiento
Abra **Investigar** y **Métricas** para ver un intervalo de contadores de rendimiento.

![Captura de pantalla del panel de métricas con los bytes privados del proceso seleccionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar la recopilación de contadores de rendimiento
Para deshabilitar la recopilación del conjunto estándar de contadores de rendimiento, agregue el siguiente código bajo el nodo raíz del archivo ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Recopilar contadores de rendimiento adicionales
Puede especificar contadores de rendimiento adicionales que se van a recopilar.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expuestos por la máquina virtual de Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` : el nombre mostrado en el portal de Application Insights.
* `objectName` : el nombre del objeto JMX.
* `attribute` : el atributo del nombre del objeto JMX que se va a capturar
* `type` (opcional): el tipo de atributo del objeto JMX:
  * Valor predeterminado: un tipo simple como int o long.
  * `composite`: los datos del contador de rendimiento tienen el formato 'Attribute.Data'
  * `tabular`: los datos del contador de rendimiento tienen el formato de una fila de tabla

#### <a name="windows-performance-counters"></a>Contadores de rendimiento de Windows
Cada [contador de rendimiento de Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) es un miembro de una categoría (de la misma manera que un campo es un miembro de una clase). Las categorías puede ser globales, o pueden tener instancias con nombre o numeradas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName: el nombre mostrado en el portal de Application Insights.
* categoryName: la categoría de contador de rendimiento (objeto de rendimiento) con la que está asociada este contador de rendimiento.
* counterName: el nombre del contador de rendimiento.
* instanceName: el nombre de la instancia de categoría del contador de rendimiento o una cadena vacía (""), si la categoría contiene una sola instancia. Si categoryName es Proceso, y el contador de rendimiento que desea recopilar está en el proceso de JVM actual en que se ejecuta la aplicación, especifique `"__SELF__"`.

### <a name="unix-performance-counters"></a>Contadores de rendimiento de Unix
* [Instale collectd con el complemento de Application Insights](java-collectd.md) para obtener una amplia variedad de datos de red y del sistema.

## <a name="get-user-and-session-data"></a>Obtención de datos de usuario y sesión
Bien, va a enviar telemetría desde el servidor web. Ahora, para obtener la visión completa de 360 grados de la aplicación, puede agregar mayor supervisión:

* [Agregue telemetría a las páginas web][usage] para supervisar las vistas de páginas y las métricas de usuario.
* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.

## <a name="send-your-own-telemetry"></a>Envío de su propia telemetría
Ahora que ha instalado el SDK, puede utilizar la API para enviar su propia telemetría.

* [Realice el seguimiento de eventos y métricas personalizados][api] para saber qué hacen los usuarios con su aplicación.
* [Busque eventos y registros][diagnostic] para ayudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Pruebas web de disponibilidad
Application Insights puede probar su sitio web a intervalos regulares para comprobar que está activo y que responde correctamente.

[Obtenga más información sobre cómo configurar las pruebas web de disponibilidad][availability].

## <a name="questions-problems"></a>¿Tiene preguntas? ¿Tiene problemas?
[Solución de problemas de Java](java-troubleshoot.md)

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de llamadas a dependencias](java-agent.md)
* [Supervisión de contadores de rendimiento de Unix](java-collectd.md)
* Incorporación de la [supervisión a las páginas web](javascript.md) para controlar los tiempos de carga, las llamadas de AJAX y la excepciones del explorador.
* Escritura de una [telemetría personalizada](../../azure-monitor/app/api-custom-events-metrics.md) para hacer un seguimiento del uso en el explorador o el servidor.
* Uso de [Analytics](../../azure-monitor/app/analytics.md) para realizar consultas eficaces sobre los datos de telemetría de la aplicación
* Para más información, visite [Azure para desarrolladores de Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
