---
title: Ejemplos de integración de Azure Service Bus en Event Grid | Microsoft Docs
description: Este artículo proporciona ejemplos de integración de la mensajería de Service Bus y Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: f31e014cf242675577bedd29a3a79332ede32bf5
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304235"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Respuesta a eventos de Azure Service Bus recibidos a través de Azure Event Grid con Azure Functions y Azure Logic Apps
En este tutorial, aprenderá a responder a eventos de Azure Service Bus que se reciben a través de Azure Event Grid con Azure Functions y Azure Logic Apps. Realizará los siguientes pasos:
 
- Cree una función de Azure de prueba para depurar y ver el flujo inicial de eventos desde Event Grid.
- Cree una función de Azure para recibir y procesar los mensajes de Azure Service Bus basados en eventos de Event Grid.
- Crear una aplicación lógica para responder a eventos de Event Grid

Después de crear los artefactos de Service Bus, Event Grid, Azure Functions y Logic Apps, realizará las siguientes acciones: 

1. Enviar mensajes a un tema de Service Bus 
2. Comprobar que las suscripciones al tema recibieron esos mensajes
3. Comprobar que la función o la aplicación lógica de suscripción al evento ha recibido el evento 

## <a name="create-a-service-bus-namespace"></a>Creación de un espacio de nombres de Service Bus
Siga las instrucciones de este tutorial: [Inicio rápido: Use Azure Portal para crear un tema de Service Bus y suscripciones al tema ](service-bus-quickstart-topics-subscriptions-portal.md) para realizar las siguientes tareas:

- Cree un espacio de nombres de Service Bus **Premium**. 
- Obtenga la cadena de conexión. 
- Cree un tema de Service Bus.
- Cree dos suscripciones al tema. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparación de una aplicación de ejemplo para enviar mensajes
Puede usar cualquier método para enviar un mensaje al tema de Service Bus. El ejemplo de código al final de este procedimiento presupone que usa Visual Studio 2017.

1. Clone [el repositorio azure-service-bus de GitHub](https://github.com/Azure/azure-service-bus/).
2. En Visual Studio,acuda a la carpeta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* y abra el archivo *SBEventGridIntegration.sln*.
3. Vaya al proyecto **MessageSender** y, a continuación, seleccione **Program.cs**.
4. Rellene el nombre del tema de Service Bus y la cadena de conexión que obtuvo en el paso anterior:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Compile y ejecute el programa para enviar mensajes de prueba al tema de Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Configuración de una función de prueba en Azure 
Antes de examinar todo el escenario, configure por lo menos una pequeña función de prueba que puede usar para depurar y ver los eventos que fluyen. Siga las instrucciones del artículo [Creación de su primera función en Azure Portal](../azure-functions/functions-create-first-azure-function.md) para realizar las siguientes tareas: 

1. Cree una aplicación de función.
2. Cree una función desencadenada mediante HTTP. 

Luego, siga estos pasos: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. Expanda **Functions** en la vista de árbol y seleccione la función. Reemplace el código de la función por el código siguiente: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Seleccione **Guardar y ejecutar**.

    ![Salida de la aplicación de función](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Seleccione **Get function URL** (Obtener la dirección URL de la función) y anote la dirección URL. 

    ![Obtención de dirección URL de la función](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Configure la función para usar la versión **V1**: 
    1. Seleccione la aplicación de función en la vista de árbol y seleccione **Function app settings** (Configuración de Function App). 

        ![Configuración de Function App]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. En **Runtime version** (Versión en tiempo de ejecución), seleccione **~1**. 
2. Expanda **Functions** en la vista de árbol y seleccione la función. Reemplace el código de la función por el código siguiente: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Seleccione **Guardar y ejecutar**.

    ![Salida de la aplicación de función](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Seleccione **Get function URL** (Obtener la dirección URL de la función) y anote la dirección URL. 

    ![Obtención de dirección URL de la función](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Conexión de la función y el espacio de nombres mediante Event Grid
En esta sección va a asociar la función y el espacio de nombres de Service Bus mediante Azure Portal. 

Para crear una suscripción de Azure Event Grid, siga estos pasos:

1. En Azure Portal, vaya a su espacio de nombres y, luego, en el panel izquierdo, seleccione **Eventos**. Se abre la ventana del espacio de nombres, con dos suscripciones de Event Grid que se muestran en el panel derecho. 
    
    ![Service Bus: página de eventos](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Seleccione **+Suscripción de eventos** en la barra de herramientas. 
3. En la página **Crear suscripción de eventos**, realice los siguientes pasos:
    1. Escriba el **nombre** de la suscripción. 
    2. En **Tipo de punto de conexión**, seleccione **Webhook**. 

        ![Service Bus: suscripción a Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Elija **Seleccionar un punto de conexión**, pegue la dirección URL de la función y, luego, seleccione **Confirmar selección**. 

        ![Función: selección del punto de conexión](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Cambie a la pestaña **Filtros**, escriba el nombre de la **primera suscripción** al tema de Service Bus que creó anteriormente y, luego, seleccione el botón **Crear**. 

        ![Filtro de suscripción a eventos](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Confirme que la suscripción a eventos aparece en la lista.

    ![Suscripción a eventos en la lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Envío de mensaje al tema de Service Bus
1. Ejecute la aplicación de C# para .NET, que envía mensajes al tema de Service Bus. 

    ![Salida de la aplicación de consola](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. En la página de la aplicación de función de Azure, expanda **Functions**, expanda su **función** y seleccione **Supervisión**. 

    ![Función de supervisión](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Recepción de mensajes mediante Azure Functions
En la sección anterior pudo observar un escenario de prueba y depuración sencillo, y se aseguró de que los eventos fluían. 

En esta sección aprenderá cómo recibir y procesar los mensajes una vez que reciba un evento.

### <a name="publish-a-function-from-visual-studio"></a>Prueba de una función desde Visual Studio
1. En la misma solución de Visual Studio (**SBEventGridIntegration**) que ha abierto, seleccione **ReceiveMessagesOnEvent.cs** en el proyecto **SBEventGridIntegration**. 
2. Especifique la cadena de conexión de Service Bus en el código siguiente:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Descargue el **perfil de publicación** de la función:
    1. Seleccione la aplicación de función. 
    2. Seleccione la pestaña **Información general** si no está ya seleccionada. 
    3. Seleccione **Obtener perfil de publicación** en la barra de herramientas. 

        ![Obtención del perfil de publicación de la función](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Guarde el archivo en la carpeta del proyecto. 
4. En Visual Studio, haga clic con el botón derecho en **SBEventGridIntegration** y seleccione **Publicar**. 
5. Seleccione **Iniciar** en la página **Publicar**. 
6. En la página **Elegir un destino de publicación**, realice los pasos siguientes y seleccione **Importar perfil**. 

    ![Visual Studio: botón Importar perfil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Seleccione el **archivo de perfil de publicación** que descargó anteriormente. 
8. En la página **Publicar**, seleccione **Publicar**. 

    ![Visual Studio: Publicar](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Confirme que ve la nueva función de Azure **ReceiveMessagesOnEvent**. Si es necesario, actualice la página. 

    ![Confirmación de que la función se ha creado](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Obtenga la dirección URL a la nueva función y anótela. 

### <a name="event-grid-subscription"></a>Suscripción a Event Grid

1. Elimine la suscripción a Event Grid existente:
    1. En la página **Espacio de nombres de Service Bus**, seleccione **Eventos** en el menú izquierdo. 
    2. Seleccione la suscripción a eventos existente. 
    3. En la página **Suscripción de eventos**, seleccione **Eliminar**.
2. Siga las instrucciones de la sección [Conexión de la función y el espacio de nombres mediante Event Grid](#connect-the-function-and-namespace-via-event-grid) para crear una suscripción de Event Grid con la nueva dirección URL de función.
3. Siga las instrucciones de la sección [Envío de mensaje al tema de Service Bus](#send-messages-to-the-service-bus-topic) para enviar mensajes al tema y supervisar la función. 

## <a name="receive-messages-by-using-logic-apps"></a>Recepción de mensajes mediante Logic Apps
Conecte una aplicación lógica con Azure Service Bus y Azure Event Grid mediante estos pasos:

1. En Azure Portal, cree una aplicación lógica.
    1. Seleccione **+Crear un recurso**, **Integración** y, luego, **Aplicación lógica**. 
    2. En la página **Aplicación lógica: Crear**, escriba un **nombre** para la aplicación lógica.
    3. Selección la **suscripción**de Azure. 
    4. En **Grupo de recursos**, seleccione **Usar existente** y seleccione el grupo de recursos que usó para otros recursos (por ejemplo, función de Azure, espacio de nombres de Service Bus) que creó anteriormente. 
    5. En **Ubicación**, seleccione la ubicación de la aplicación lógica. 
    6. Seleccione **Crear** para crear la aplicación lógica. 
2. En la página **Diseñador de aplicaciones lógicas**, seleccione **Aplicación lógica en blanco** en **Plantillas**. 
3. En el diseñador, realice los pasos siguientes:
    1. Busque **Event Grid**. 
    2. Seleccione **When a resource event occurs (preview) - Azure Event Grid** (Cuando se produce un evento de recurso [versión preliminar]: Azure Event Grid). 

        ![Diseñador de aplicaciones lógicas: selección del desencadenador de Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Seleccione **Sign in** (Iniciar sesión), escriba sus credenciales de Azure y seleccione **Allow Access** (Permitir acceso). 
5. En la página **When a resource event occurs** (Cuando se produce un evento de recurso), realice los siguientes pasos:
    1. Seleccione su suscripción a Azure. 
    2. En **Resource Type** (Tipo de recurso), seleccione **Microsoft.ServiceBus.Namespaces**. 
    3. En **Resource Name** (Nombre del recurso), seleccione el espacio de nombres de Service Bus. 
    4. Seleccione **Add new parameter** (Agregar nuevo parámetro) y **Suffix Filter** (Filtro de sufijo). 
    5. En **Suffix Filter** (Filtro de sufijo), escriba el nombre de la segunda suscripción al tema de Service Bus. 
        ![Diseñador de Logic Apps: configuración de eventos](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. En el diseñador, seleccione **+New Step** (+Nuevo paso) y siga los siguientes pasos:
    1. Busque **Service Bus**.
    2. Seleccione **Service Bus** en la lista. 
    3. Seleccione **Get messages** (Obtener mensajes) en la lista **Actions** (Acciones). 
    4. Seleccione **Get messages from a topic subscription (peek-lock)** (Obtener mensajes de una suscripción de tema [bloque de inspección]). 

        ![Diseñador de aplicaciones lógicas: obtención de la acción de mensajes](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Escriba un **nombre para la conexión**. Por ejemplo:  **Obtenga mensajes de la suscripción al tema** y seleccione el espacio de nombres de Service Bus. 

        ![Diseñador de aplicaciones lógicas: selección del espacio de nombres de Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Seleccione **RootManageSharedAccessKey**.

        ![Diseñador de aplicaciones lógicas: selección de la clave de acceso compartido](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Seleccione **Crear**. 
    8. Seleccione su tema y suscripción. 
    
        ![Diseñador de aplicaciones lógicas: selección del tema y la suscripción de Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Seleccione **+New step** (+Nuevo paso) y realice los siguientes pasos: 
    1. Seleccione **Service Bus**.
    2. Seleccione **Complete the message in a topic subscription** (Completar el mensaje en una suscripción de tema) en la lista de acciones. 
    3. Seleccione el **tema** de Service Bus.
    4. Seleccione la segunda **suscripción** al tema.
    5. En **Lock token of the message** (	Token de bloqueo del mensaje), seleccione **Lock Token** (Token de bloqueo) en **Dynamic content** (Contenido dinámico). 

        ![Diseñador de aplicaciones lógicas: selección del tema y la suscripción de Service Bus](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Seleccione **Save** (Guardar) en la barra de herramientas del diseñador de aplicaciones lógicas para guardar la aplicación lógica. 
9. Siga las instrucciones de la sección [Envío de mensaje al tema de Service Bus](#send-messages-to-the-service-bus-topic) para enviar mensajes al tema. 
10. Cambie a la página **Información general** de la aplicación lógica. Verá que la aplicación lógica se ejecuta en el **historial de ejecuciones** de los mensajes enviados.

    ![Diseñador de aplicaciones lógicas: ejecuciones de aplicación lógica](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Más información acerca de [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Más información acerca de la [característica Logic Apps de Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* Aprenda más sobre [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
