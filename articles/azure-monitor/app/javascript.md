---
title: Azure Application Insights para aplicaciones web de JavaScript | Microsoft Docs
description: Obtenga recuentos de sesiones y vistas de página, además de datos de cliente web, y realice el seguimiento de los patrones de uso. Detecte problemas de rendimiento y excepciones en páginas web de JavaScript.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 177d8e6e9d4393df785f2caf55bf6cbe895bc640
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667910"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas web

Obtenga información sobre el rendimiento y la utilización de su página web o aplicación. Si agrega [Application Insights](app-insights-overview.md) al script de la página, obtendrá los intervalos de carga de la página y de las llamadas AJAX, recuentos y detalles sobre las excepciones del explorador y los errores de AJAX, así como usuarios y recuentos de sesiones. Todos estos datos se pueden segmentar por página, sistema operativo del cliente y versión del explorador, geolocalización y otras dimensiones. Puede establecer alertas sobre recuentos de errores o sobre cargas de página lentas. Y mediante la inserción de llamadas de seguimiento en el código de JavaScript, puede controlar cómo se utilizan las distintas características de la aplicación de la página web.

Puede utilizar Application Insights con cualquier página web, solo tiene que agregar un pequeño fragmento de JavaScript. Si el servicio web es [Java](java-get-started.md) o [ASP.NET](asp-net.md), puede usar los SDK del lado servidor junto con el SDK de JavaScript del lado cliente para obtener una descripción completa del rendimiento de la aplicación.

## <a name="adding-the-javascript-sdk"></a>Adición del SDK de JavaScript

1. En primer lugar, necesita un recurso de Application Insights. Si aún no tiene un recurso y una clave de instrumentación, siga las [instrucciones para crear un nuevo recurso](create-new-resource.md).
2. Copie la clave de instrumentación del recurso al que desea que se envíe la telemetría de JavaScript.
3. Agregue el SDK de JavaScript de Application Insights a su página web o aplicación mediante una de las dos opciones siguientes:
    * [Configuración de npm](#npm-based-setup)
    * [Fragmento de código Javascript](#snippet-based-setup)

> [!IMPORTANT]
> Solo tiene que usar uno de los métodos siguientes para agregar el SDK de JavaScript de Application Insights a la aplicación. Si usa la configuración basada en npm, no use la configuración basada en el fragmento de código. Lo mismo ocurre a la inversa: si usa el método del fragmento de código, no utilice la configuración basada en npm. 

### <a name="npm-based-setup"></a>Configuración basada en npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>Configuración basada en fragmento de código

Si su aplicación no usa npm, puede instrumentar directamente las páginas web con Application Insights pegando este fragmento de código en la parte superior de cada una de las páginas. Preferiblemente, debe ser el primer script de la sección `<head>` para que pueda supervisar cualquier posible problema con todas las dependencias. Si usa una aplicación de servidor de Blazor, agregue el fragmento de código en la parte superior del archivo `_Host.cshtml` en la sección `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Envío de telemetría a Azure Portal

De forma predeterminada, el SDK de JavaScript de Application Insights recopila automáticamente un número de elementos de telemetría que son útiles para determinar el estado de la aplicación y la experiencia del usuario subyacente. Entre ellas se incluyen las siguientes:

- **Excepciones no detectadas** en la aplicación, incluida información sobre lo siguiente:
    - Seguimiento de la pila
    - Detalles de la excepción y mensaje que acompaña al error
    - Número de línea y columna del error
    - Dirección URL en la que se produjo el error
- **Solicitudes de dependencia de red** realizadas por las solicitudes **XHR** and **Fetch** (la recopilación de capturas está deshabilitada de forma predeterminada) de la aplicación, incluida información sobre lo siguiente:
    - Dirección URL del origen de dependencia
    - Comando y método usado para solicitar la dependencia
    - Duración de la solicitud
    - Código de resultado y estado correcto de la solicitud
    - Identificador (si existe) del usuario que realiza la solicitud
    - Contexto de correlación (si existe) en el que se realiza la solicitud
- **Información del usuario** (por ejemplo, ubicación, red, IP)
- **Información del dispositivo** (por ejemplo, explorador, sistema operativo, versión, idioma, resolución, modelo)
- **Información de la sesión**

### <a name="telemetry-initializers"></a>Inicializadores de telemetría
Los inicializadores de telemetría se usan para modificar el contenido de la telemetría recopilada antes de enviarla desde el explorador del usuario. También se pueden usar para impedir que se envíen determinados datos de telemetría, devolviendo `false`. Se pueden agregar varios inicializadores de telemetría a la instancia de Application Insights, que se ejecutarán en el orden en el que se han agregado.

El argumento de entrada en `addTelemetryInitializer` es una devolución de llamada que toma [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) como argumento y devuelve `boolean` o `void`. Si devuelve `false`, no se envía el elemento de telemetría, sino que pasa al siguiente inicializador de telemetría, si existe, o se envía al punto de conexión de recopilación de telemetría.

Ejemplo de uso de inicializadores de telemetría:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Configuración
La mayoría de los campos de configuración tienen un nombre que permite establecer false como valor predeterminado. Todos los campos son opcionales, excepto `instrumentationKey`.

| NOMBRE | Valor predeterminado | DESCRIPCIÓN |
|------|---------|-------------|
| instrumentationKey | null | **Obligatorio**<br>La clave de instrumentación que ha obtenido en Azure Portal. |
| accountId | null | Un identificador de cuenta opcional, si su aplicación agrupa a los usuarios en cuentas. Sin espacios, comas, puntos y comas, signos igual o barras verticales. |
| sessionRenewalMs | 1800000 | Se registra una sesión si el usuario está inactivo durante este período de tiempo en milisegundos. El valor predeterminado es 30 minutos. |
| sessionExpirationMs | 86400000 | Se registra una sesión si ha continuado durante este período de tiempo en milisegundos. El valor predeterminado es 24 horas. |
| maxBatchSizeInBytes | 10000 | Tamaño máximo del lote de telemetría. Si un lote supera este límite, se envía inmediatamente y se inicia un nuevo lote. |
| maxBatchInterval | 15000 | Cuánto tiempo deben recopilar datos de telemetría en lote antes de enviarlos (milisegundos). |
| disableExceptionTracking | false | Si es true, las excepciones no se recopilan automáticamente. El valor predeterminado es false. |
| disableTelemetry | false | Si es true, no se recopila ni se envía la telemetría. El valor predeterminado es false. |
| enableDebug | false | Si es true, los datos de depuración **internos** se devuelven como una excepción **en lugar de** registrarse, independientemente de la configuración de registro del SDK. El valor predeterminado es false. <br>***Nota:*** Si se habilita este valor, se descartará la telemetría siempre que se produzca un error interno. Esto puede ser útil para identificar rápidamente problemas con la configuración o el uso del SDK. Si no desea perder telemetría durante la depuración, considere la posibilidad de usar `consoleLoggingLevel` o `telemetryLoggingLevel` en lugar de `enableDebug`. |
| loggingLevelConsole | 0 | Registra errores **internos** de Application Insights en la consola. <br>0: desactivado <br>1: solo errores críticos <br>2\. todo (errores y advertencias) |
| loggingLevelTelemetry | 1 | Envía errores **internos** de Application Insights como telemetría. <br>0: desactivado <br>1: solo errores críticos <br>2\. todo (errores y advertencias) |
| diagnosticLogInterval | 10000 | (interno) Intervalo de sondeo (en milisegundos) para la cola de registro interno |
| samplingPercentage | 100 | Porcentaje de eventos que se enviarán. El valor predeterminado es 100, lo que significa que se envían todos los eventos. Establézcalo si desea conservar el límite de datos para aplicaciones a gran escala. |
| autoTrackPageVisitTime | false | Si es true, en una vista de página, se realiza un seguimiento del tiempo de visualización de la página instrumentada anterior, que se envía como telemetría, y se inicia un nuevo temporizador para la vista de página actual. El valor predeterminado es false. |
| disableAjaxTracking | false | Si es true, las llamadas Ajax no se recopilan automáticamente. El valor predeterminado es false. |
| disableFetchTracking | true | Si es true, las solicitudes Fetch no se recopilan automáticamente. El valor predeterminado es true. |
| overridePageViewDuration | false | Si es true, el comportamiento predeterminado de trackPageView se cambia para registrar el final del intervalo de duración de la vista de página cuando se llama a trackPageView. Si es false y no se proporciona ninguna duración personalizada a trackPageView, el rendimiento de la vista de página se calcula mediante la API de tiempos de navegación. El valor predeterminado es false. |
| maxAjaxCallsPerView | 500 | El valor predeterminado es 500: controla cuántas llamadas AJAX se supervisarán por vista de página. Establézcalo en -1 para supervisar todas las llamadas AJAX (ilimitadas) en la página. |
| disableDataLossAnalysis | true | Si es false, se comprobará al inicio en los búferes del remitente de telemetría interna si hay elementos que todavía no se hayan enviado. |
| disableCorrelationHeaders | false | Si es false, el SDK agregará dos encabezados ("Request-Id" y "Request-Context") a todas las solicitudes de dependencia para correlacionarlas con las solicitudes correspondientes en el lado servidor. El valor predeterminado es false. |
| correlationHeaderExcludedDomains |  | Deshabilite encabezados de correlación para dominios específicos. |
| correlationHeaderDomains |  | Habilite encabezados de correlación para dominios específicos. |
| disableFlushOnBeforeUnload | false | El valor predeterminado es false. Si es true, no se llamará al método Flush cuando se desencadene un evento onBeforeUnload. |
| enableSessionStorageBuffer | true | El valor predeterminado es true. Si es true, el búfer con toda la telemetría no enviada se guarda en el almacenamiento de la sesión. El búfer se restaura al cargar la página. |
| isCookieUseDisabled | false | El valor predeterminado es false. Si es true, el SDK no almacenará ni leerá ningún dato de las cookies.|
| cookieDomain | null | Dominio de cookies personalizado. Resulta útil si desea compartir cookies de Application Insights entre subdominios. |
| isRetryDisabled | false | El valor predeterminado es false. Si es false, se produce un reintento con los errores 206 (parcialmente correcto), 408 (tiempo de espera), 429 (demasiadas solicitudes), 500 (error interno del servidor), 503 (servicio no disponible) y 0 (sin conexión, solo si se detecta). |
| isStorageUseDisabled | false | Si es true, el SDK no almacenará ni leerá ningún dato del almacenamiento local o de la sesión. El valor predeterminado es false. |
| isBeaconApiDisabled | true | Si es false, el SDK enviará toda la telemetría mediante [Beacon API](https://www.w3.org/TR/beacon). |
| onunloadDisableBeacon | false | El valor predeterminado es false. Si la pestaña está cerrada, el SDK enviará la telemetría restante mediante la [API Beacon ](https://www.w3.org/TR/beacon). |
| sdkExtension | null | Establece el nombre de la extensión del SDK. Solo puede contener caracteres alfabéticos. El nombre de la extensión se agrega como prefijo a la etiqueta "ai.internal.sdkVersion" (por ejemplo, "ext_javascript:2.0.0"). El valor predeterminado es null. |
| isBrowserLinkTrackingEnabled | false | El valor predeterminado es false. Si es true, el SDK realizará un seguimiento de todas las solicitudes de [Vínculo con exploradores](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink). |
| appId | null | AppId se utiliza para la correlación entre las dependencias AJAX que se producen en el lado cliente con las solicitudes del lado servidor. Cuando Beacon API está habilitada, no se puede usar automáticamente, pero se puede establecer manualmente en la configuración. El valor predeterminado es null. |
| enableCorsCorrelation | false | Si es true, el SDK agregará dos encabezados ("Request-Id" y "Request-Context") a todas las solicitudes CORS para correlacionar dependencias AJAX salientes con las solicitudes correspondientes en el lado servidor. El valor predeterminado es false. |
| namePrefix | no definido | Un valor opcional que se usará como sufijo de nombre para el nombre de cookies y localStorage.
| enableAutoRouteTracking | false | Realice un seguimiento automático de los cambios de ruta en aplicaciones de página única (SPA). Si es true, cada cambio de ruta enviará una nueva vista de página a Application Insights. Los cambios de ruta hash (`example.com/foo#bar`) también se registran como nuevas vistas de página.
| enableRequestHeaderTracking | false | Si es true, se realiza un seguimiento de los encabezados de solicitud AJAX y Fetch. El valor predeterminado es false.
| enableResponseHeaderTracking | false | Si es true, se realiza un seguimiento de los encabezados de respuesta de solicitud AJAX y Fetch. El valor predeterminado es false.
| distributedTracingMode | `DistributedTracingModes.AI` | Establece del modo de seguimiento distribuido. Si se establece el modo AI_AND_W3C o el modo W3C, se generarán los encabezados de contexto de seguimiento W3C (traceparent/tracestate) y se incluirán en todas las solicitudes salientes. AI_AND_W3C se proporciona para la compatibilidad con versiones anteriores de cualquier servicio instrumentado de Application Insights heredado.

## <a name="single-page-applications"></a>Aplicaciones de página única

De forma predeterminada, este SDK **no** controlará el cambio de ruta basado en el estado que se produce en las aplicaciones de página única. Para habilitar el seguimiento automático de cambios de ruta para una aplicación de página única, puede agregar `enableAutoRouteTracking: true` a la configuración de instalación.

Actualmente, ofrecemos un [complemento React](#react-extensions) independiente que puede inicializarse con este SDK. También llevará a cabo el seguimiento de cambios de ruta, así como la recopilación de [otros datos de telemetría específicos de React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Extensiones de React

| Extensiones |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Exploración de datos del explorador o del lado cliente

Para ver datos del explorador o del lado cliente, vaya a **Métricas** y agregue métricas individuales que le interesen: 

![](./media/javascript/page-view-load-time.png)

También puede ver los datos desde el SDK de JavaScript mediante la experiencia de explorador en el portal.

Seleccione **Explorador** y, a continuación, elija **Errores** o **Rendimiento**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Rendimiento 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependencias

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análisis 

Para consultar la telemetría recopilada por el SDK de JavaScript, seleccione el botón **Ver en registros (Analytics)** . Al agregar una instrucción `where` de `client_Type == "Browser"`, solo verá los datos del SDK de JavaScript y se excluirán todos los datos de telemetría del lado servidor recopilados por otros SDK.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Compatibilidad con mapa de origen

La pila de llamadas compactada de los datos de telemetría de las excepciones se puede descompactar en Azure Portal. Todas las integraciones existentes en el panel Detalles de la excepción funcionarán con la pila de llamadas recién descompactada. La descompactación del mapa de origen mediante una operación de arrastrar y colocar se admite en todos los SDK de JS actuales y futuros (+Node.JS), por lo que no es necesario actualizar la versión del SDK. Para ver la pila de llamadas descompactada:
1. Seleccione un elemento de telemetría de excepciones en Azure Portal para ver sus detalles de transacción completa.
2. Identifique qué mapas de origen corresponden a esta pila de llamadas. El mapa de origen debe coincidir con el archivo de origen de un marco de pila, pero con el sufijo `.map`.
3. Arrastre y coloque los mapas de origen en la pila de llamadas en Azure Portal. ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Versión web básica de Application Insights

Para una experiencia ligera, puede instalar la versión básica de Application Insights.
```
npm i --save @microsoft/applicationinsights-web-basic
```
Esta versión incluye las características y funcionalidades mínimas, que puede ir ampliando como desee. Por ejemplo, no realiza ninguna recopilación automática (excepciones no detectadas, AJAX, etc.). En esta versión no se incluyen las API para enviar determinados tipos de telemetría, como `trackTrace`, `trackException`, etc., por lo que tendrá que proporcionar su propio contenedor. La única API disponible es `track`. Aquí tiene un [ejemplo](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html).

## <a name="examples"></a>Ejemplos

Para ver ejemplos ejecutables, consulte [Ejemplos del SDK de JavaScript de Application Insights](https://github.com/topics/applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Actualizar desde la versión anterior de Application Insights

Cambios importantes en la versión del SDK V2:
- Para permitir mejores firmas de API, se han actualizado algunas de las llamadas API, como trackPageView o trackException. No se admite la ejecución en IE8 o en versiones anteriores del explorador.
- El sobre de telemetría presenta cambios en el nombre y la estructura del campo debido a actualizaciones del esquema de datos.
- `context.operation` se ha trasladado a `context.telemetryTrace`. También se han cambiado algunos campos (`operation.id` --> `telemetryTrace.traceID`).
  - Si desea actualizar manualmente el identificador de la vista de página actual (por ejemplo, en las aplicaciones SPA), puede hacerlo con `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`.

Si usa el SDK de producción de Application Insights actual (1.0.20) y desea ver si el nuevo SDK funciona en tiempo de ejecución, actualice la dirección URL en función de su escenario de carga de SDK actual.

- Descarga mediante el escenario de CDN: actualice el fragmento de código que usa actualmente para que apunte a la dirección URL siguiente:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- Escenario de npm: llame a `downloadAndSetup` para descargar el script ApplicationInsights completo de la red CDN e inicialícelo con la clave de instrumentación:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Pruebe en el entorno interno para comprobar que la telemetría de supervisión funciona según lo esperado. Si todo funciona, actualice las firmas de API correctamente a la versión del SDK V2 y realice la implementación en los entornos de producción.

## <a name="sdk-performanceoverhead"></a>Rendimiento y sobrecarga del SDK

Gracias a su tamaño comprimido con gzip de 25 KB y a los aproximadamente 15 ms que tarda en inicializarse, Application Insights agrega una cantidad insignificante de tiempo de carga a su sitio web. Con el fragmento de código, los componentes mínimos de la biblioteca se cargan rápidamente. Mientras tanto, el script completo se descarga en segundo plano.

Mientras el script se descarga de la red CDN, todo el seguimiento de la página se pone en cola. Una vez que se completa la inicialización asincrónica del script descargado, se realiza un seguimiento de todos los eventos que se pusieron en cola. Como resultado, no perderá telemetría durante todo el ciclo de vida de la página. Este proceso de instalación proporciona a la página un sistema de análisis sin interrupciones, invisible para los usuarios.

> Resumen:
> - Tamaño de **25 KB** comprimido con gzip
> - Tiempo de inicialización total de **15 ms**
> - **Cero** datos de telemetría perdidos durante el ciclo de vida de la página

## <a name="browser-support"></a>Compatibilidad con exploradores

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Más reciente ✔ | Más reciente ✔ | 9+ ✔ | Más reciente ✔ | Más reciente ✔ |

## <a name="open-source-sdk"></a>SDK de código abierto

El SDK de JavaScript de Application Insights es de código abierto. Para ver el código fuente o para contribuir al proyecto, visite el [repositorio de GitHub oficial](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Pasos siguientes
* [Seguir el uso](usage-overview.md)
* [Eventos y métricas personalizados](api-custom-events-metrics.md)
* [Compilación - Métrica - Aprendizaje](usage-overview.md)

