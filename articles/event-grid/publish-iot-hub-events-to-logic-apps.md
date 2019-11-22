---
title: 'Tutorial: Uso de eventos de IoT Hub para desencadenar Azure Logic Apps'
description: 'Tutorial: Con el servicio de enrutamiento de eventos de Azure Event Grid, cree procesos automatizados para llevar a cabo acciones de Azure Logic Apps basadas en eventos de IoT Hub.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106780"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Tutorial: Envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps

Azure Event Grid permite reaccionar a los eventos en IoT Hub mediante el desencadenamiento de acciones en las aplicaciones empresariales de bajada.

En este artículo se ofrece orientación mediante una configuración de ejemplo que usa IoT Hub y Event Grid. Al final, tendrá una aplicación en Azure Logic Apps configurada para enviar una notificación por correo electrónico cada vez que se agregue un dispositivo a la instancia de IoT Hub. 

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de correo electrónico de cualquier proveedor de correo electrónico que sea compatible con Azure Logic Apps, como Office 365 Outlook, Outlook.com o Gmail. Esta cuenta de correo electrónico se usa para enviar las notificaciones de eventos. Para obtener una lista completa de los conectores compatibles de Logic Apps, consulte la [información general sobre los conectores](https://docs.microsoft.com/connectors/).
* Una cuenta de Azure activa. En caso de no tener ninguna, puede [crear una gratis](https://azure.microsoft.com/pricing/free-trial/).
* Una instancia de IoT Hub en Azure. Si aún no ha creado ninguna, vea un tutorial en [Introducción a Azure IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Creación de una aplicación lógica

En primer lugar, cree una aplicación lógica y agregue un desencadenador de la cuadrícula de eventos que supervise el grupo de recursos de la máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Creación de un recurso de aplicación lógica

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Integración** > **Logic App**.

   ![Creación de la aplicación lógica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Asigne un nombre a la aplicación lógica que sea único en su suscripción; a continuación, seleccione la misma suscripción, el grupo de recursos y la ubicación en que se encuentra la instancia de IoT Hub. 
3. Seleccione **Crear**.

4. Una vez creado el recurso, vaya a la aplicación lógica. 

5. El Diseñador de aplicaciones lógicas muestra las plantillas de patrones comunes para que pueda empezar con mayor rapidez. En el Diseñador de aplicación lógica que se encuentra en **Plantillas**, elija **Aplicación lógica en blanco** para que pueda compilar la aplicación lógica desde el principio.

### <a name="select-a-trigger"></a>Selección de un desencadenador

Un desencadenador es un evento específico que inicia la aplicación lógica. Para este tutorial, el desencadenador que activa el flujo de trabajo recibe una solicitud a través de HTTP.  

1. En la barra de búsqueda de conectores y desencadenadores, escriba **HTTP**.
2. Seleccione **Solicitud: Cuando se recibe una solicitud HTTP** como desencadenador. 

   ![Selección del desencadenador de solicitud HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Seleccione **Usar una carga de ejemplo para generar el esquema**. 

   ![Selección del desencadenador de solicitud HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Pegue el siguiente código JSON de ejemplo en el cuadro de texto y después seleccione **Listo**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

5. Puede recibir una notificación emergente que dice: **Recuerde incluir un encabezado Content-Type establecido en application/json en la solicitud.** Puede ignorar esta sugerencia sin ningún problema y pasar a la sección siguiente. 

### <a name="create-an-action"></a>Creación de una acción

Las acciones son los pasos que se producen después de que el desencadenador inicia el flujo de trabajo de la aplicación lógica. En este tutorial, la acción consiste en enviar una notificación por correo electrónico desde su proveedor de correo electrónico. 

1. Seleccione **Nuevo paso**. Se abrirá una ventana para **elegir una acción**.

2. Busque **Correo electrónico**.

3. En función de su proveedor de correo electrónico, busque y seleccione el conector correspondiente. Este tutorial usa **Office 365 Outlook**. Los pasos para otros proveedores de correo electrónico son similares. 

   ![Selección del conector del proveedor de correo electrónico](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Seleccione la acción **Enviar un correo electrónico**. 

5. Si se le pide, inicie sesión en la cuenta de correo electrónico. 

6. Cree la plantilla de correo electrónico. 
   * **Para**: escriba la dirección de correo electrónico en la que desea recibir los correos electrónicos de notificación. Para este tutorial, use una cuenta de correo electrónico a la que pueda acceder para las pruebas. 
   * **Asunto** y **Cuerpo**: escriba el texto del correo electrónico. Seleccione las propiedades JSON de la herramienta del selector para incluir contenido dinámico basado en los datos de los eventos.  

   La plantilla de correo electrónico puede tener un aspecto similar al de este ejemplo:

   ![Rellenar la información del correo electrónico](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Guarde la aplicación lógica. 

### <a name="copy-the-http-url"></a>Copia de la dirección URL HTTP

Antes de cerrar el Diseñador de Logic Apps, copie la dirección URL en la que se realizan las escuchas de un desencadenador de las aplicaciones lógicas. Use esta dirección URL para configurar Event Grid. 

1. Haga clic en el cuadro de configuración del desencadenador **Cuando se recibe una solicitud HTTP** para expandirlo. 
2. Seleccione el botón de copia que se encuentra junto al valor **Dirección URL de HTTP POST** para copiarlo. 

   ![Copia de la dirección URL de HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Guarde esta dirección URL para poder hacer referencia a ella en la siguiente sección. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar la suscripción de eventos de IoT Hub

En esta sección, va a configurar IoT Hube para publicar eventos cuando se produzcan. 

1. En Azure Portal, vaya hasta el centro de IoT. 
2. Seleccione **Eventos**.

   ![Abrir los detalles de Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Seleccione **Suscripción de eventos**. 

   ![Crear una suscripción de eventos](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Cree la suscripción de eventos con los siguientes valores: 
   * **Tipo de evento**: desactive Suscribirse a todos los tipos de evento y seleccione **Dispositivo creado** en el menú.
   * **Detalles de punto de conexión**: En Tipo de punto de conexión, seleccione **Webhook**, haga clic en Seleccionar punto de conexión y pegue la dirección URL que copió de la aplicación lógica y confirme la selección.

     ![seleccionar dirección URL de punto de conexión](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Detalles de suscripciones de eventos**: proporcione un nombre descriptivo y seleccione **Esquema de Event Grid**.

   Una vez realizadas las acciones anteriores, el formulario debe tener un aspecto similar al del ejemplo siguiente: 

    ![Formulario de ejemplo de suscripción de eventos](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Puede guardar la suscripción de eventos aquí y recibir notificaciones en todos los dispositivos creados en su instancia de IoT Hub. En este tutorial, sin embargo, se van a usar los campos opcionales para filtrar por dispositivos específicos. Seleccione **Características adicionales** en la parte superior del formulario. 

6. Cree los siguientes filtros:

   * **El asunto comienza por**: escriba `devices/Building1_` para filtrar por los eventos de dispositivos del edificio 1.
   * **El asunto termina con**: escriba `_Temperature` para filtrar por los eventos de dispositivo relacionados con la temperatura.

5. Seleccione **Crear** para guardar la suscripción de eventos.

## <a name="create-a-new-device"></a>Creación de un dispositivo

Pruebe la aplicación lógica mediante la creación de un dispositivo para desencadenar un correo electrónico de notificación de eventos. 

1. En IoT Hub, seleccione **Dispositivos IoT**. 
2. Seleccione **Agregar**.
3. En **Id. de dispositivo**, escriba `Building1_Floor1_Room1_Temperature`.
4. Seleccione **Guardar**. 
5. Puede agregar varios dispositivos con distintos identificadores de dispositivo para probar los filtros de suscripción de eventos. Pruebe estos ejemplos: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Una vez que haya agregado unos cuantos dispositivos a su instancia de IoT Hub, consulte su correo electrónico para ver cuáles desencadenaron la aplicación lógica. 

## <a name="use-the-azure-cli"></a>Uso de la CLI de Azure

En lugar de usar Azure Portal, puede llevar a cabo los pasos de IoT Hub mediante la CLI de Azure. Para obtener más información, vea las páginas de la CLI de Azure para [crear una suscripción de eventos](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) y [crear un dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Limpieza de recursos

En este tutorial se usaron recursos que generan gastos en su suscripción de Azure. Cuando haya terminado de probar el tutorial y los resultados de las pruebas, deshabilite o elimine los recursos que no desea conservar. 

Si no desea perder el trabajo realizado en la aplicación lógica, deshabilítela en lugar de eliminarla. 

1. Vaya a la aplicación lógica.
2. En la hoja **Introducción**, seleccione **Eliminar** o **Deshabilitar**. 

Cada suscripción solo puede tener una instancia de IoT Hub gratuita. Si creó una instancia de IoT Hub gratis para este tutorial, no es necesario eliminarla para evitar cargos.

1. Vaya a su instancia de IoT Hub. 
2. En la página **Introducción**, seleccione **Eliminar**. 

Aunque conserve la instancia de IoT Hub, puede que desee eliminar la suscripción de eventos que creó. 

1. En la instancia de IoT Hub, seleccione **Event Grid**.
2. Seleccione la suscripción de eventos que desea eliminar. 
3. Seleccione **Eliminar**. 

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre [reacción a eventos de IoT Hub mediante Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md).
* [Aprenda a ordenar eventos conectados y desconectados de dispositivos](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Obtenga información sobre qué más puede hacer con [Event Grid](overview.md).


