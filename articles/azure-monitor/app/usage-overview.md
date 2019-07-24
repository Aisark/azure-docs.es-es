---
title: Análisis de uso con Azure Application Insights | Microsoft Docs
description: Entienda a los usuarios y lo qué hacen con la aplicación.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: ba29688958ee11aa9906a820f7a3d2bf41223743
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798178"
---
# <a name="usage-analysis-with-application-insights"></a>Análisis de uso con Application Insights

¿Qué características de la aplicación web o móvil son más populares? ¿Los usuarios logran sus objetivos con la aplicación? ¿Salen de ella en momentos concretos y vuelven más tarde?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) le ayudará a obtener información eficaz sobre el uso de la aplicación por parte de los usuarios. Cada vez que actualice la aplicación, puede evaluar también si funciona bien para los usuarios. Con este conocimiento, puede tomar decisiones basadas en datos sobre los ciclos de desarrollo siguientes.

## <a name="send-telemetry-from-your-app"></a>Envío de telemetría desde la aplicación

La mejor experiencia se obtiene mediante la instalación de Application Insights en el código de servidor de aplicaciones y en las páginas web. Los componentes de cliente y servidor de la aplicación devuelven telemetría a Azure Portal para su análisis.

1. **Código de servidor**: instale el módulo adecuado para [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) u [otra](../../azure-monitor/app/platforms.md) aplicación.

    * *¿No desea instalar código del servidor? Simplemente [cree un recurso de Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Código de página web**: Agregue el siguiente script en la página web antes de ``</head>`` de cierre. Reemplace la clave de instrumentación por el valor apropiado para el recurso de Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Para obtener configuraciones más avanzadas para supervisar sitios web, consulte la [referencia a JavaScript SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Código de aplicación móvil**: utilice el SDK de App Center para recopilar eventos de la aplicación y después enviar copias de estos eventos a Application Insights para el análisis; para ello, [siga esta guía](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obtener telemetría**: ejecute su proyecto en modo de depuración durante unos minutos y luego busque resultados en la hoja de información general en Application Insights.

    Publique su aplicación para supervisar el rendimiento de su aplicación y descubra lo que hacen sus usuarios con ella.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclusión del identificador de usuario y de sesión en la telemetría
Para realizar un seguimiento de los usuarios a lo largo del tiempo, Application Insights necesita una manera de identificarlos. La herramienta de eventos es la única herramienta de uso que no requiere un identificador de usuario o de sesión.

Comience enviando identificadores de usuario y de sesión con [este proceso](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Exploración de estadísticas y datos demográficos de uso
Descubra cuándo los usuarios utilizan la aplicación, en qué páginas que están más interesados, en qué ubicación se encuentran dichos usuarios, y los sistemas operativos y exploradores que emplean. 

Los informes Usuarios y sesiones filtran los datos por páginas o eventos personalizados, y los segmentan por propiedades tales como la ubicación, el entorno y la página. También puede agregar sus propios filtros.

![Usuarios](./media/usage-overview/users.png)  

La información de la derecha señala patrones de interés en el conjunto de datos.  

* El informe **Usuarios** indica el número de usuarios únicos que tienen acceso a las páginas dentro de los periodos seleccionados. Para las aplicaciones web, los usuarios se cuentan con cookies. Si alguien accede a su sitio con distintos exploradores o máquinas cliente, o borra las cookies, se contabilizarán más de una vez.
* El informe **Sesiones** indica el número de sesiones de usuario que acceden al sitio. Una sesión es un periodo de actividad por parte de un usuario, que finaliza con un periodo de inactividad de más de media hora.

[Más información sobre las herramientas Usuarios, Sesiones y Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retención : ¿cuántos usuarios regresan?

Retención lo ayudará a comprender la frecuencia con la que los usuarios vuelven a usar su aplicación, en función de las cohortes de usuarios que realizan alguna acción empresarial durante un intervalo de tiempo determinado. 

- Qué características específicas provocan que los usuarios vuelvan más veces que otras 
- Formular hipótesis basadas en datos de usuarios reales 
- Determinar si la retención es un problema del producto 

![Retención](./media/usage-overview/retention.png) 

Los controles de retención de la parte superior permiten definir eventos específicos y el intervalo de tiempo para calcular la retención. El gráfico situado en la parte central proporciona una representación visual del porcentaje total de retención por el intervalo de tiempo especificado. El gráfico de la parte inferior representa la retención individual en un periodo determinado. Este nivel de detalle permite entender lo que hacen los usuarios y qué podría afectar al regreso de los usuarios con una granularidad más detallada.  

[Más información de la herramienta Retención](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negocio personalizados

Para obtener una idea clara de lo que los usuarios hacen con la aplicación, es útil insertar líneas de código para registrar los eventos personalizados. Estos eventos pueden realizar un seguimiento desde acciones del usuario detalladas como hacer clic en botones específicos hasta eventos de negocio más importantes como realizar una compra o ganar una partida. 

Aunque, en algunos casos, las vistas de página pueden representar eventos útiles, en general, no es así. Un usuario puede abrir una página de un producto sin necesidad de adquirirlo. 

Con los eventos específicos del negocio, puede realizar un gráfico del progreso de los usuarios en su sitio. Puede averiguar sus preferencias para diferentes opciones y en qué partes salen o tienen dificultades. Con este conocimiento, puedan tomar decisiones fundamentadas en lo que respecta a las prioridades del trabajo pendiente en materia de desarrollo.

Los eventos se pueden registrar del lado del cliente de la aplicación:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

O del lado del servidor:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Puede adjuntar los valores de propiedad a estos eventos, para que pueda filtrar o dividir los eventos al examinarlos en el portal. Además, se adjunta un conjunto estándar de propiedades a cada evento, como el identificador de usuario anónimo, lo que permite realizar un seguimiento de la secuencia de actividades de un usuario individual.

Obtenga más información sobre los [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) y las [propiedades](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Eventos de segmentación y desglose

En las herramientas Usuarios, Sesiones y Eventos, puede segmentar y desglosar los eventos personalizados por usuario, nombre del evento y propiedades.
![Usuarios](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Diseño de la telemetría con la aplicación

Al diseñar cada característica de la aplicación, tenga en cuenta cómo va a medir su éxito con los usuarios. Decida qué eventos empresariales necesita registrar y codifique las llamadas de seguimiento de esos eventos en la aplicación desde el principio.

## <a name="a--b-testing"></a>Prueba A | B
Si no conoce qué variante de una característica tendrá más éxito, publique ambas para que estén accesibles a los diferentes usuarios. Mida el éxito de cada una y, a continuación, cambie a una versión unificada.

Para realizar esta técnica, adjunte valores de propiedad distintas a toda la telemetría que se envía con cada versión de la aplicación. Puede hacerlo al definir las propiedades en el TelemetryContext activo. Estas propiedades predeterminadas se agregan a cada mensaje de telemetría que envía la aplicación: no solo los mensajes personalizados, sino también la telemetría estándar.

En el portal de Application Insights, podrá filtrar y dividir los datos en los valores de propiedad, con el fin de comparar las distintas versiones.

Para ello, [configure un inicializador de telemetría](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer):

**Aplicaciones ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

En el inicializador de la aplicación web, como Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplicaciones de ASP.NET Core**

> [!NOTE]
> La adición del inicializador mediante `ApplicationInsights.config` o `TelemetryConfiguration.Active` no es válida para las aplicaciones de ASP.NET Core. 

Para aplicaciones de [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), la adición de un nuevo elemento `TelemetryInitializer` se realiza agregándolo al contenedor de inserción de dependencias, como se muestra a continuación. Esto se hace en el método `ConfigureServices` de la clase `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos los nuevos clientes de telemetría agregan automáticamente el valor de propiedad especificado. La telemetría individual puede invalidar los valores predeterminados.

## <a name="next-steps"></a>Pasos siguientes
   - [Usuarios, sesiones, eventos](usage-segmentation.md)
   - [Embudos](usage-funnels.md)
   - [Retención](usage-retention.md)
   - [Flujos de usuario](usage-flows.md)
   - [Libros](../../azure-monitor/app/usage-workbooks.md)
   - [Adición de contexto de usuario](usage-send-user-context.md)
