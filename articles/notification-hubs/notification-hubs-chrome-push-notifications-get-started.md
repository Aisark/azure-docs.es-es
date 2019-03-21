---
title: Envío de notificaciones push a aplicaciones Chrome con Azure Notification Hubs | Microsoft Docs
description: Aprenda a usar los Centros de notificaciones de Azure para enviar notificaciones push a una aplicación de Chrome.
services: notification-hubs
keywords: notificaciones push móviles,notificaciones push,notificación push,notificaciones push android
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 03374f63345bd6c9e4f2b603443a1448493e1cdc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894621"
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Tutorial: Notificaciones push a aplicaciones Chrome con Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial le guía a través de la creación de un Centro de notificaciones y del envío notificaciones de inserción a una aplicación de Google Chrome de ejemplo mediante [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). La aplicación de Chrome se ejecuta en el contexto de un explorador Google Chrome y se registra en el Centro de notificaciones.

> [!NOTE]
> Las notificaciones push de las aplicaciones de Chrome no son notificaciones en el explorador genéricas, son específicas del modelo de extensibilidad del explorador (consulte [Chrome Apps Overview] (Información general sobre las aplicaciones de Chrome) para más información). Además del explorador de escritorio, las aplicaciones de Chrome se ejecutan en dispositivos móviles (iOS y Android) mediante Apache Cordova. Para más información, consulte cómo ejecutar [Aplicaciones de Chrome en dispositivos móviles].

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * [Habilitación del servicio de mensajería en la nube de Google](#register)
> * [Configuración de su Centro de notificaciones](#configure-hub)
> * [Conexión de la aplicación Chrome con el centro de notificaciones](#connect-app)
> * [Envío de notificaciones push a la aplicación de Chrome](#send)

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

1. Vaya al sitio web de [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) e inicie sesión con las credenciales de su cuenta de Google
2. Seleccione **Create Project** (Crear proyecto) en la barra de herramientas.

    ![Botón Create Project (Crear proyecto)](media/notification-hubs-chrome-get-started/create-project-button.png)
3. Proporcione un **nombre de proyecto** adecuado y, luego, haga clic en el botón **Crear**.
4. Seleccione el icono de notificación (campana) en la barra de herramientas y seleccione el mensaje **Create Project** (Crear proyecto).

    ![Notificación de creación de proyecto](media/notification-hubs-chrome-get-started/project-creation-notification.png)
5. Tome nota del número del proyecto en **Project Number**, en la página **Project** (Proyecto) del proyecto que acaba de crear. Úselo como valor de **GCM Sender ID** (Identificador de remitente de GCM) en la aplicación de Chrome para registrarse en GCM.

    ![Consola de Google Cloud: número de proyecto](media/notification-hubs-chrome-get-started/gcm-project-number.png)
6. En el panel, seleccione **Go to APIs overview** (Ir a información general de API).

    ![Botón Go to APIs overview (Ir a información general de API)](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
7. En la página API & Services (API y servicios), seleccione **Enable APIs and Services** (Habilitar API y servicios).

    ![Enable APIs and services (Habilitar API y servicios)](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
8. Buscar en la lista la palabra clave **cloud messaging**. Seleccione **Google Cloud Messaging** en la lista filtrada.

    ![API de Google Cloud Messaging](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
9. En la página **Google Cloud Messaging**, seleccione **Enable** (Habilitar).

    ![Habilitar GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
10. En la página **API & Services** (API y servicios), cambie a la pestaña **Credentials** (Credenciales). Seleccione **Create credentials** (Crear credenciales) y, después, **API key** (Clave de API).

    ![Botón Create API key (Crear clave de API)](media/notification-hubs-chrome-get-started/create-api-key-button.png)
11. En el cuadro de diálogo **API key created** (Clave de API creada), seleccione el botón de copia para copiar la clave en el Portapapeles. Guárdela en algún lugar. Este valor se usa para configurar el Centro de notificaciones, en la sección siguiente, para que pueda enviar notificaciones push a GCM.

    ![Página de API](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Seleccione la clave de API en la lista **API keys** (Claves API). En la página API key (Clave API), seleccione **IP addresses (web servers, cron jobs, etc.)** [Direcciones IP (servidores web, trabajos de cron, etc.)], escriba la dirección IP **0.0.0.0/0** y haga clic en Save (Guardar).

    ![Habilitar direcciones IP](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Creación de un Centro de notificaciones

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Seleccione **Google (GCM)** en la categoría **NOTIFICATION SETTINGS** (CONFIGURACIÓN DE NOTIFICACIONES), escriba el **clave de API** del proyecto GCM y haga clic en **Save** (Guardar).

&emsp;&emsp;&emsp;&emsp;![Centros de notificaciones de Azure: Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Conexión de la aplicación Chrome con el centro de notificaciones

El centro de notificaciones está configurado para trabajar con GCM y el usuario dispone de las cadenas de conexión necesarias para registrar su aplicación para que reciba y envíe notificaciones push.

### <a name="create-a-new-chrome-app"></a>Creación de una nueva aplicación Chrome

El siguiente ejemplo se basa en el [ejemplo de GCM de la aplicación de Chrome] y usa la forma recomendada para crear una aplicación de Chrome. En esta sección, se indican los pasos que son específicos de Azure Notification Hubs.

> [!NOTE]
> Se recomienda que descargue el origen de esta aplicación Chrome del [ejemplo del centro de notificaciones de la aplicación Chrome].

La aplicación Chrome se crea con JavaScript y puede usar su editor de texto preferido para su creación. La siguiente imagen muestra el aspecto de la aplicación de Chrome:

![Aplicación de Google Chrome][15]

1. Cree una carpeta y asígnele el nombre `ChromePushApp`. El nombre es arbitrario; si el nombre es diferente, asegúrese de sustituir la ruta de acceso en los segmentos de código necesarios.
2. Descargue la [biblioteca crypto-js] en la carpeta que creó en el segundo paso. Esta carpeta de biblioteca contiene dos subcarpetas: `components` y `rollups`.
3. Cree un archivo `manifest.json` . Todas las aplicaciones de Chrome están respaldadas por un archivo de manifiesto que contiene los metadatos de la aplicación y, lo más importante, todos los permisos concedidos a la aplicación cuando el usuario la instala.

    ```json
    {
        "name": "NH-GCM Notifications",
        "description": "Chrome platform app.",
        "manifest_version": 2,
        "version": "0.1",
        "app": {
        "background": {
            "scripts": ["background.js"]
        }
        },
        "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
        "icons": { "128": "gcm_128.png" }
    }
    ```

    Observe el elemento `permissions`, que especifica que esta aplicación de Chrome puede recibir notificaciones push de GCM. La aplicación de ejemplo también usa un archivo de icono, `gcm_128.png`, que encontrará en el código fuente reutilizado del ejemplo original de GCM. Puede sustituirlo por cualquier imagen que cumpla los [criterios de icono](https://developer.chrome.com/apps/manifest/icons).
4. Cree un archivo llamado `background.js` con el código siguiente:

    ```javascript
    // Returns a new notification ID used in the notification.
    function getNotificationId() {
        var id = Math.floor(Math.random() * 9007199254740992) + 1;
        return id.toString();
    }

    function messageReceived(message) {
        // A message is an object with a data property that
        // consists of key-value pairs.

        // Concatenate all key-value pairs to form a display string.
        var messageString = "";
        for (var key in message.data) {
        if (messageString != "")
            messageString += ", "
        messageString += key + ":" + message.data[key];
        }
        console.log("Message received: " + messageString);

        // Pop up a notification to show the GCM message.
        chrome.notifications.create(getNotificationId(), {
        title: 'GCM Message',
        iconUrl: 'gcm_128.png',
        type: 'basic',
        message: messageString
        }, function() {});
    }

    var registerWindowCreated = false;

    function firstTimeRegistration() {
        chrome.storage.local.get("registered", function(result) {

        registerWindowCreated = true;
        chrome.app.window.create(
            "register.html",
            {  width: 520,
                height: 500,
                frame: 'chrome'
            },
            function(appWin) {}
        );
        });
    }

    // Set up a listener for GCM message event.
    chrome.gcm.onMessage.addListener(messageReceived);

    // Set up listeners to trigger the first-time registration.
    chrome.runtime.onInstalled.addListener(firstTimeRegistration);
    chrome.runtime.onStartup.addListener(firstTimeRegistration);
    ```

    Este archivo muestra el código HTML de la ventana de la aplicación Chrome (`register.html`) y también define el controlador `messageReceived` que se encarga de la notificación push entrante.
5. Cree un archivo llamado `register.html`, que define la interfaz de usuario de la aplicación de Chrome.

   > [!NOTE]
   > En este ejemplo se usa **CryptoJS v3.1.2**. Si descargó otra versión de la biblioteca, asegúrese de reemplazar correctamente la versión en la ruta de acceso `src` .

    ```html
    <html>
        <head>
            <title>GCM Registration</title>
            <script src="register.js"></script>
            <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
            <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
        <body>
            Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
            <button id="registerWithGCM">Register with GCM</button>
            <br/>
            <br/>
            <br/>

            Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
            Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

            <br/>
            <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
            <br/>
            <br/>

            <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
        </body>
    </html>
    ```
6. Cree un archivo llamado `register.js` con el código de este paso. Este archivo especifica el script subyacente a `register.html`. Las aplicaciones Chrome no permiten la ejecución insertada, por lo que es necesario crear un script de copia de seguridad independiente para la interfaz de usuario.

    ```javascript
    var registrationId = "";
    var hubName        = "", connectionString = "";
    var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

    window.onload = function() {
        document.getElementById("registerWithGCM").onclick = registerWithGCM;  
        document.getElementById("registerWithNH").onclick = registerWithNH;
        updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
    }

    function updateLog(status) {
        currentStatus = document.getElementById("console").innerHTML;
        if (currentStatus != "") {
        currentStatus = currentStatus + "\n\n";
        }

        document.getElementById("console").innerHTML = currentStatus  + status;
    }

    function registerWithGCM() {
        var senderId = document.getElementById("senderId").value.trim();
        chrome.gcm.register([senderId], registerCallback);

        // Prevent register button from being clicked again before the registration finishes.
        document.getElementById("registerWithGCM").disabled = true;
    }

    function registerCallback(regId) {
        registrationId = regId;
        document.getElementById("registerWithGCM").disabled = false;

        if (chrome.runtime.lastError) {
        // When the registration fails, handle the error and retry the
        // registration later.
        updateLog("Registration failed: " + chrome.runtime.lastError.message);
        return;
        }

        updateLog("Registration with GCM succeeded.");
        document.getElementById("registerWithNH").disabled = false;

        // Mark that the first-time registration is done.
        chrome.storage.local.set({registered: true});
    }

    function registerWithNH() {
        hubName = document.getElementById("hubName").value.trim();
        connectionString = document.getElementById("connectionString").value.trim();
        splitConnectionString();
        generateSaSToken();
        sendNHRegistrationRequest();
    }

    // From https://msdn.microsoft.com/library/dn495627.aspx
    function splitConnectionString()
    {
        var parts = connectionString.split(';');
        if (parts.length != 3)
        throw "Error parsing connection string";

        parts.forEach(function(part) {
        if (part.indexOf('Endpoint') == 0) {
        endpoint = 'https' + part.substring(11);
        } else if (part.indexOf('SharedAccessKeyName') == 0) {
        sasKeyName = part.substring(20);
        } else if (part.indexOf('SharedAccessKey') == 0) {
        sasKeyValue = part.substring(16);
        }
        });

        originalUri = endpoint + hubName;
    }

    function generateSaSToken()
    {
        targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
        var expiresInMins = 10; // 10 minute expiration

        // Set expiration in seconds.
        var expireOnDate = new Date();
        expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
        var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
        .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
        .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
        .getUTCSeconds()) / 1000;
        var tosign = targetUri + '\n' + expires;

        // Using CryptoJS.
        var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
        var base64signature = signature.toString(CryptoJS.enc.Base64);
        var base64UriEncoded = encodeURIComponent(base64signature);

        // Construct authorization string.
        sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
    }

    function sendNHRegistrationRequest()
    {
        var registrationPayload =
        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
        "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
            "<content type=\"application/xml\">" +
                "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                    "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                "</GcmRegistrationDescription>" +
            "</content>" +
        "</entry>";

        // Update the payload with the registration ID obtained earlier.
        registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

        var url = originalUri + "/registrations/?api-version=2014-09";
        var client = new XMLHttpRequest();

        client.onload = function () {
        if (client.readyState == 4) {
            if (client.status == 200) {
            updateLog("Notification Hub Registration successful!");
            updateLog(client.responseText);
            } else {
            updateLog("Notification Hub Registration did not succeed!");
            updateLog("HTTP Status: " + client.status + " : " + client.statusText);
            updateLog("HTTP Response: " + "\n" + client.responseText);
            }
        }
        };

        client.onerror = function () {
            updateLog("ERROR - Notification Hub Registration did not succeed!");
        }

        client.open("POST", url, true);
        client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
        client.setRequestHeader("Authorization", sasToken);
        client.setRequestHeader("x-ms-version", "2014-09");

        try {
            client.send(registrationPayload);
        }
        catch(err) {
            updateLog(err.message);
        }
    }
    ```

    El script tiene los siguientes parámetros clave:

   * `window.onload` define los eventos de clic de botón de los dos botones de la interfaz de usuario. El primer controlador de evento de clic de botón se registra con GCM y el otro usa el identificador de registro que se devolvió después del registro con GCM para registrarse con Azure Notification Hubs.
   * `updateLog` es la función que permite la información del registro del código.
   * `registerWithGCM` es el primer controlador de clic de botón que hace que la llamada de `chrome.gcm.register` a GCM registre la instancia actual de la aplicación Chrome.
   * `registerCallback` es la función de devolución de llamada a la que se invoca cuando se devuelve la llamada de registro de GCM.
   * `registerWithNH` es el segundo controlador de clic de botón, que se registra en Notification Hubs. Obtiene los valores de `hubName` y `connectionString` (que el usuario ha especificado) y elabora la llamada a la API de REST de registro en los Centros de notificaciones.
   * `splitConnectionString` y `generateSaSToken` son asistentes que representan la implementación de JavaScript del proceso de creación de un token SaS, que debe usarse en todas las llamadas de la API REST. Para más información, vea [Conceptos comunes](https://msdn.microsoft.com/library/dn495627.aspx).
   * `sendNHRegistrationRequest` es la función que realiza una llamada HTTP REST a Azure Notification Hubs.
   * `registrationPayload` define la carga de XML del registro. Para obtener más información, consulte [Crear registro]. Actualice el identificador de registro en ella con el valor recibido de GCM.
   * `client` es una instancia de `XMLHttpRequest` que la aplicación usa para realizar la solicitud HTTP POST. Actualice el encabezado `Authorization` con `sasToken`. La finalización correcta de esta llamada registra esta instancia de la aplicación Chrome en Azure Notification Hubs.

     La estructura general de las carpetas de este proyecto debe ser similar a la siguiente: ![Aplicación de Google Chrome: estructura de carpetas][21]

### <a name="set-up-and-test-your-chrome-app"></a>Configuración y prueba de la aplicación Chrome

1. Abra el explorador Chrome. Abra las **extensiones de Chrome** y habilite el **modo de desarrollador**.

    ![Google Chrome: habilitar modo de desarrollador][16]
2. Haga clic en **Cargar extensión descomprimida** y vaya a la carpeta donde creó los archivos. Opcionalmente, también puede usar la **herramienta para desarrolladores de aplicaciones y extensiones Chrome**. Esta herramienta es una aplicación Chrome en sí misma (se instala desde el almacén web de Chrome) y proporciona las capacidades de depuración avanzadas para el desarrollo de aplicaciones Chrome.

    ![Google Chrome: cargar extensión descomprimida][17]
3. Si la aplicación Chrome se crea sin errores, verá que aparece.

    ![Google Chrome: pantalla de la aplicación de Chrome][18]
4. Escriba el **número de proyecto** que obtuvo anteriormente de la **consola en la nube de Google**, como el identificador del remitente, y haga clic en **egistrar con GCM**. Debe ver el mensaje **Registration with GCM succeeded**

    ![Google Chrome: personalización de la aplicación de Chrome][19]
5. Escriba su **nombre del centro de notificaciones** y el valor de **DefaultListenSharedAccessSignature** obtenido anteriormente del Portal y haga clic en **Registrarse en el Centro de notificaciones de Azure**. Debe ver el mensaje **El registro en el centro de notificaciones se realizó correctamente!** (El registro en Notification Hubs se realizó correctamente) y los detalles de la respuesta de registro, que contienen el identificador de registro de Azure Notification Hubs.

    ![Google Chrome: especificar detalles del centro de notificaciones][20]  

## <a name="send"></a>Envío de notificaciones a la aplicación Chrome

Para las pruebas, enviamos notificaciones push de Chrome mediante la aplicación de consola de .NET.

> [!NOTE]
> Puede enviar notificaciones push con Notification Hubs desde cualquier back-end a través de la [interfaz REST](https://msdn.microsoft.com/library/windowsazure/dn223264.aspx). Consulte el [portal de documentación](https://azure.microsoft.com/documentation/services/notification-hubs/) para obtener más ejemplos multiplataforma.

1. En Visual Studio, en el menú **Archivole**, seleccione **Nuevo** y, luego, **Proyecto**. En **Visual C#**, haga clic en **Windows** y **Aplicación de consola** y, luego, haga clic en **Aceptar**.  Este paso crea un nuevo proyecto de aplicación de consola.
2. En el menú **Tools** (Herramientas), haga clic en **Administrador de paquetes NuGet** y luego en **Consola del Administrador de paquetes**. Verá la Consola del Administrador de paquetes en Visual Studio.
3. En la ventana de la consola, ejecute el siguiente comando:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

   Se agrega automáticamente al proyecto una referencia al SDK de Azure Service Bus con el [paquete NuGet WindowsAzure.ServiceBus](https://nuget.org/packages/WindowsAzure.ServiceBus/).
4. Abra `Program.cs` y agregue la siguiente instrucción `using`:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```
5. En la clase `Program`, agregue el método siguiente:

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
        await hub.SendGcmNativeNotificationAsync(message);
    }
    ```

    Asegúrese de reemplazar el marcador de posición `<hub name>` por el nombre del Centro de notificaciones que aparece en el [portal](https://portal.azure.com) de la página Centros de notificaciones. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada `DefaultFullSharedAccessSignature` que obtuvo en la sección de configuración del centro de notificaciones.

    > [!NOTE]
    > Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de conexión de acceso **Listen** no concede permisos para enviar notificaciones push.

6. Agregue las siguientes llamadas en el método `Main` :

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

7. Asegúrese de que se está ejecutando Chrome y ejecute la aplicación de consola.
8. Verá el siguiente cuadro emergente de notificación en el escritorio.

    ![Google Chrome: notificación][13]
9. También puede ver todas las notificaciones mediante la ventana de notificaciones de Chrome en la barra de tareas (en Windows) cuando Chrome se está ejecutando.

    ![Google Chrome: lista de notificaciones](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> No es necesario tener abierta o en ejecución la aplicación Chrome en el explorador (aunque el propio explorador Chrome se debe estar ejecutando). También obtendrá una vista consolidada de todas las notificaciones en la ventana de notificaciones de Chrome.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial ha difundido notificaciones de difusión a todos los clientes registrados con el back-end. Para aprender a enviar notificaciones push a dispositivos específicos, pase al siguiente tutorial:

> [!div class="nextstepaction"]
>[Envío de notificaciones push a dispositivos específicos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[ejemplo del centro de notificaciones de la aplicación Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Chrome Apps Overview]: https://developer.chrome.com/apps/about_apps
[ejemplo de GCM de la aplicación de Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplicaciones de Chrome en dispositivos móviles]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Crear registro]: https://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca crypto-js]: https://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
