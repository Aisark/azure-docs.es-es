---
title: Diagnóstico de problemas de rendimiento mediante Azure Application Insights | Microsoft Docs
description: Tutorial para buscar y diagnosticar problemas de rendimiento en un aplicación mediante Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f906ab5db35ce8b239eceac9cdc8244f230f5a77
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596029"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Búsqueda y diagnóstico de problemas de rendimiento con Azure Application Insights

Azure Application Insights recopila datos de telemetría de cualquier aplicación para ayudarle a analizar su funcionamiento y rendimiento.  Esta información se puede usar para identificar los problemas que se pueden producir o las mejoras en la aplicación que más afectarían a los usuarios.  En este tutorial se recorre todo el proceso de análisis del rendimiento tanto de los componentes de servidor de una aplicación como de la perspectiva del cliente.  Aprenderá a:

> [!div class="checklist"]
> * Identificar el rendimiento de las operaciones del servidor
> * Analizar las operaciones del servidor para determinar la causa principal de un bajo rendimiento
> * Identificar las operaciones del cliente más lentas
> * Analizar los detalles de las vistas de página mediante el lenguaje de consulta


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
    - ASP.NET y desarrollo web
    - Desarrollo de Azure
- Implemente una aplicación de .NET en Azure y [habilite el SDK de Application Insights](../../azure-monitor/app/asp-net.md).
- [Habilite el generador de perfiles de Application Insights](../../azure-monitor/app/profiler.md#installation) de la aplicación.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificación de operaciones lentas del servidor
Application Insights recopila datos de rendimiento de las distintas operaciones en la aplicación. Mediante la identificación de las operaciones que más tardan en completarse, es posible diagnosticar problemas potenciales o enfocar mejor el desarrollo en curso para mejorar el rendimiento general de la aplicación.

1. Seleccione **Application Insights** y, a continuación, seleccione la suscripción.  
1. Para abrir el panel **Rendimiento**, seleccione **Rendimiento** en el menú **Investigar** o haga clic en el grafo **Tiempo de respuesta del servidor**.

    ![Rendimiento](media/tutorial-performance/performance.png)

2. El panel **Rendimiento** muestra el número de operaciones de la aplicación y la duración media de cada una de ellas.  Esta información se puede usar para identificar las operaciones que más afectan a los usuarios. En este ejemplo, **GET Customers/Details** y **GET Home/Index** son probables candidatos a los que investigar, ya que su duración es relativamente alta y se las llama en numerosas ocasiones.  Otras operaciones pueden tener una duración superior, pero rara vez se les llama, por lo que el efecto de su mejora sería mínimo.  

    ![Panel Rendimiento](media/tutorial-performance/performance-blade.png)

3. Actualmente, el grafo muestra la duración media de las operaciones a lo largo del tiempo. Puede cambiar al percentil 95 para detectar los problemas de rendimiento. Para agregar las operaciones que le interesen, ánclelas al grafo.  Esto muestra que hay algunos valores máximos que merece la pena investigar.  Y para aislarlos aún más, puede reducir la ventana de tiempo del grafo.

    ![Anclar operaciones](media/tutorial-performance/pin-operations.png)

4.  El panel de rendimiento de la derecha muestra la distribución de duraciones de distintas solicitudes para la operación seleccionada.  Reduzca la ventana para empezar alrededor del percentil 95. La tarjeta de información "3 dependencias principales" puede indicarle de un vistazo que las dependencias externas probablemente estén coadyuvando a que se produzcan las transacciones lentas.  Haga clic en el botón con el número de ejemplos para ver una lista de los ejemplos. A continuación, puede seleccionar cualquier ejemplo para ver los detalles de transacción.

    ![Distribución de las duraciones](media/tutorial-performance/duration-distribution.png)

5.  Puede ver a simple vista que la llamada a la tabla de Azure Fabrikamaccount es la que más contribuye a la duración total de la transacción. También puede ver que una excepción ha provocado un error. Puede hacer clic en cualquier elemento de la lista para ver sus detalles en el lado derecho. [Más información acerca de la experiencia de diagnósticos de transacción](../../azure-monitor/app/transaction-diagnostics.md)

    ![Detalles de la operación](media/tutorial-performance/operation-details.png)
    

6.  **Profiler** ayuda a profundizar en los diagnósticos de nivel de código al mostrar el código real que se ejecutó para la operación y el tiempo necesario para cada paso. Es posible que no se pueda realizar un seguimiento de algunas operaciones, ya que el generador de perfiles se ejecuta periódicamente.  Con el tiempo, más operaciones deben tener seguimientos.  Para iniciar el generador de perfiles de la operación, haga clic en **Seguimientos de Profiler**.
5.  El seguimiento muestra los eventos individuales de cada operación, por lo que puede diagnosticar la causa principal por la duración de la operación global.  Haga clic en uno de los ejemplos superiores, que son los que tienen una duración mayor.
6.  Haga clic en **Mostrar ruta de acceso activa** para resaltar la ruta de acceso específica de los eventos que más contribuyen a la duración total de la operación.  En este ejemplo, puede ver que la llamada más lenta procede del método *FabrikamFiberAzureStorage.GetStorageTableData*. La parte que tiene la mayor parte del tiempo es el método *CloudTable.CreateIfNotExist*. Si esta línea de código se ejecuta cada vez que se llama a la función, se consumirán recursos de CPU y llamadas de red innecesarios. La mejor manera de corregir el código es poner esta línea en algún método de inicio que solo se ejecute una vez. 

    ![Detalles de Profiler](media/tutorial-performance/profiler-details.png)

7.  La sugerencia que se muestra en **Consejo de rendimiento**, en la parte superior de la pantalla, es compatible con la evaluación de que la duración excesiva se debe a la espera.  Haga clic en el vínculo de **espera** para obtener documentación acerca de cómo interpretar los diferentes tipos de eventos.

    ![Consejo de rendimiento](media/tutorial-performance/performance-tip.png)

8.  Para un análisis más profundo, puede hacer clic en **Descargar seguimiento .etl** para descargar el seguimiento en Visual Studio.

## <a name="use-analytics-data-for-server"></a>Uso de datos de análisis en el servidor
Application Insights Analytics proporciona un lenguaje de consulta completo que le permite analizar todos los datos recopilados por Application Insights.  Con ello se puede realizar un análisis profundo de los datos de solicitud y rendimiento.

1. Vuelva al panel de detalles de la operación y haga clic en el botón Análisis.

    ![Botón Análisis](media/tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics se abre con una consulta en cada una de las vistas del panel.  Dichas consultas se pueden ejecutar tal cual, o bien se pueden modificar para ajustarlas a los requisitos.  La primera consulta muestra la duración de esta operación a lo largo del tiempo.

    ![Análisis](media/tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identificación de operaciones lentas del cliente
Además de identificar los procesos del servidor que se deben optimizar, Application Insights puede analizar la perspectiva de los exploradores cliente.  Esto puede ayudarle a identificar posibles mejoras en los componentes de cliente, e incluso identificar problemas con distintos exploradores o ubicaciones diferentes.

1. Seleccione **Explorador** en **Investigar** para abrir el resumen del explorador.  Esto proporciona un resumen visual de los distintos datos de telemetría de la aplicación desde la perspectiva del explorador.

    ![Resumen de explorador](media/tutorial-performance/browser-summary.png)

2.  Desplácese hacia abajo hasta **Cuáles son mis páginas más lentas?** .  Así se muestra una lista de las páginas de la aplicación que más han tardado en cargar los clientes.  Esta información se puede usar para dar mayor prioridad a las páginas que más afectan al usuario.
3.  Haga clic en una de las páginas para abrir el panel **Vista de página**.  En el ejemplo, la página **/FabrikamProd** muestra una duración media excesiva.  El panel **Vista de página** proporciona detalles acerca de esta página, entre los que se incluye un desglose de los distintos intervalos de duración.

    ![Vista de página](media/tutorial-performance/page-view.png)

4.  Haga clic en la duración máxima para comprobar los detalles de estas solicitudes.  Luego, haga clic en la solicitud individual para ver los detalles del cliente que solicita la página incluido, lo que incluye el tipo de explorador y su ubicación.  Esta información puede ayudarle a determinar si ciertos tipos de clientes tienen problemas de rendimiento.

    ![Detalles de la solicitud](media/tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Uso de datos de análisis en el cliente
Al igual que los datos recopilados relativos al rendimiento del servidor, Application Insights hace que todos los datos de cliente estén disponibles para un análisis profundo mediante Analytics.

1. Vuelva al resumen del explorador y haga clic en el icono de Analytics.

    ![Icono de Analytics](media/tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics se abre con una consulta en cada una de las vistas del panel. La primera consulta muestra la duración de distintas vistas de la página a lo largo del tiempo.

    ![Análisis](media/tutorial-performance/client-analytics.png)

3.  Smart Diagnostics es una característica de Application Insights Analytics que identifica patrones únicos en los datos.  Al hacer clic en el punto de Smart Diagnostics en el gráfico de líneas, la misma consulta se ejecuta sin los registros que causaron las anomalías.  Los detalles de dichos registros se muestran en la sección de comentarios de consulta para que pueda identificar las propiedades de las vistas de página que provocan que la duración sea excesiva.

    ![Smart Diagnostics](media/tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a identificar las excepciones en tiempo de ejecución, pase al siguiente tutorial, donde aprenderá a crear alertas en respuesta a errores.

> [!div class="nextstepaction"]
> [Alerta por el estado de una aplicación](../../azure-monitor/learn/tutorial-alert.md)
