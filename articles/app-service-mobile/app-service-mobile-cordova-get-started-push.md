---
title: Adición de notificaciones push a una aplicación de Apache Cordova con la característica Mobile Apps de Azure App Service | Microsoft Docs
description: Obtenga información acerca de cómo usar Mobile Apps para enviar notificaciones push a su aplicación de Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114358"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Agregar notificaciones push a su aplicación de Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general

En este tutorial, añadirá notificaciones push al proyecto de [inicio rápido de Apache Cordova][5] para que cada vez que se envíe una notificación push al dispositivo, se inserte un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesita el paquete de extensión de notificaciones push. Para más información, consulte [Trabajar con el SDK del servidor back-end de .NET para Mobile Apps][1].

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se da por hecho que tiene una aplicación de Apache Cordova que se desarrolló con Visual Studio 2015. Debe usarse un dispositivo Android, Windows o iOS, o bien Google Android Emulator.

Para completar este tutorial, necesita:

* Un equipo con [Visual Studio Community 2015][2] o posterior
* [Visual Studio Tools para Apache Cordova][4]
* Una [cuenta de Azure activa][3]
* Un proyecto de [inicio rápido de Apache Cordova][5] completado
* (Android) Una [cuenta de Google][6] con una dirección de correo electrónico verificada
* (iOS) La [pertenencia a un programa para desarrolladores de Apple][7] y un dispositivo iOS (el simulador de iOS no admite notificaciones push)
* (Windows) Una [cuenta de desarrollador para Microsoft Store][8] y un dispositivo con Windows 10

## <a name="configure-hub"></a>Configurar un Centro de notificaciones

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Vea un vídeo que muestra los pasos de esta sección][9].

## <a name="update-the-server-project"></a>Actualización del proyecto de servidor

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificación de la aplicación de Cordova

Para asegurarse de que el proyecto de aplicación de Apache Cordova está listo para controlar las notificaciones push, instale el complemento de notificaciones push de Cordova además de cualquier servicio de inserción push específico de la plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Actualización de la versión de Cordova en el proyecto.

Si el proyecto usa una versión anterior a Apache Cordova 6.1.1, actualice el proyecto de cliente. Para actualizar el proyecto, siga estos pasos:

* Haga clic con el botón derecho en `config.xml` para abrir el diseñador de configuración.
* Seleccione la pestaña **Plataformas**.
* Elija **6.1.1** en el cuadro de texto **Cordova CLI**. 
* Para actualizar el proyecto, seleccione **Compilar**y, a continuación, **Compilar solución**.

#### <a name="install-the-push-plugin"></a>Instalación del complemento de inserción

Las aplicaciones de Apache Cordova no controlan el dispositivo ni las funcionalidades de red de forma nativa.  Estas funcionalidades las proporcionan los complementos que se publican en [npm][10] o en GitHub. El complemento `phonegap-plugin-push` controla las notificaciones push de la red.

Puede instalar el complemento de notificaciones push de una de estas formas:

**Desde el símbolo del sistema:**

Ejecute el siguiente comando:

    cordova plugin add phonegap-plugin-push

**Desde Visual Studio:**

1. En el Explorador de soluciones, abra el archivo `config.xml`. A continuación, seleccione **Complementos** > **Personalizado**. A continuación, seleccione **Git** como origen de instalación.

2. Escriba `https://github.com/phonegap/phonegap-plugin-push` como origen.

    ![Abra el archivo config.xml en el Explorador de soluciones.][img1]

3. Seleccione la flecha que hay junto al origen de la instalación.

4. En **SENDER_ID**, si ya tiene un identificador numérico del proyecto para el proyecto de la consola para desarrolladores de Google, puede agregarlo aquí. En caso contrario, escriba un valor de marcador de posición, como 777777. Si su objetivo es Android, puede actualizar este valor en el archivo config.xml más adelante.

    >[!NOTE]
    >A partir de la versión 2.0.0, google-services.json debe instalarse en el directorio raíz del proyecto para configurar el identificador del remitente. Para más información, consulte la [documentación de instalación](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md).

5. Seleccione **Agregar**.

Ahora ya está instalado el complemento de inserción.

#### <a name="install-the-device-plugin"></a>Instalación del complemento del dispositivo

Siga el mismo procedimiento que usó para instalar el complemento de inserción. Añada el complemento de dispositivo de la lista de complementos principales (haga clic en **Complementos** > **Principales** para encontrarlo). Este complemento es necesario para obtener el nombre de la plataforma.

#### <a name="register-your-device-when-the-application-starts"></a>Registro del dispositivo al inicio de la aplicación 

Inicialmente, se incluirá el código mínimo para Android. Más tarde, puede modificar la aplicación para que se ejecute en iOS o en Windows 10.

1. Añada una llamada a **registerForPushNotifications** durante la devolución de llamada para el proceso de inicio de sesión. Como alternativa, puede añadirla en la parte inferior del método **onDeviceReady**:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Este ejemplo muestra la llamada a **registerForPushNotifications** después de que la autenticación se realiza correctamente. Puede llamar a `registerForPushNotifications()` tantas veces como sea necesario.

2. Agregue el nuevo método **registerForPushNotifications** como se indica a continuación:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) En el código anterior, reemplace `Your_Project_ID` por el identificador numérico del proyecto de la aplicación en la [consola para desarrolladores de Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configuración y ejecución de la aplicación en Android

Complete esta sección para habilitar las notificaciones push en Android.

#### <a name="enable-gcm"></a>Habilitar la mensajería en la nube Firebase

Dado que, en principio, el objetivo es la plataforma Android de Google, es preciso habilitar el Servicio de mensajería en la nube de Firebase.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configuración del back-end de aplicación móvil para enviar solicitudes de notificaciones push mediante FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configuración de una aplicación Cordova para Android

En la aplicación de Cordova, abra el archivo **config.xml**. A continuación, reemplace `Your_Project_ID` por el identificador numérico del proyecto de la aplicación en la [consola para desarrolladores de Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Abra **index.js**. A continuación, actualice el código para usar el identificador numérico del proyecto.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Configuración de un dispositivo Android para la depuración USB

Antes de implementar su aplicación en su dispositivo Android, debe habilitar la depuración USB. Realice los pasos siguientes en su teléfono Android:

1. Vaya a **Ajustes** > **Acerca del teléfono**. A continuación, pulse **Número de compilación** hasta que se habilite el modo de programador (unas siete veces).
2. En **Ajustes** > **Opciones de desarrollo**, habilite **Depuración USB**. A continuación, conecte su teléfono Android al equipo de desarrollo mediante un cable USB.

Cuando lo probamos, usamos un dispositivo Google Nexus 5X con Android 6.0 (Marshmallow). Sin embargo, las técnicas son comunes a cualquier versión moderna de Android.

#### <a name="install-google-play-services"></a>Instalación de Google Play Services

El complemento de inserción se basa en Google Play Services de Android para las notificaciones push.

1. En Visual Studio, seleccione **Herramientas** > **Android** > **Android SDK Manager**. A continuación, expanda la carpeta **Extras**. Active las casillas apropiadas para asegurarse de que se instala cada uno de los siguientes SDK:

   * Android 2.3 o superior
   * Revisión de Google Repository 27 o superior
   * Google Play Services 9.0.2 o superior

2. Seleccione **Instalar paquetes**. A continuación, espere hasta que la instalación se complete.

Las bibliotecas requeridas actuales se enumeran en la [documentación de instalación de phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Prueba de las notificaciones push de la aplicación en Android

Ahora puede probar las notificaciones push mediante la ejecución de la aplicación y la inserción de elementos en la tabla TodoItem. Puede hacerlo desde el mismo dispositivo o desde otro dispositivo, siempre que use el mismo back-end. Pruebe la aplicación Cordova en la plataforma Android de una de las siguientes maneras:

* *En un dispositivo físico:*  conecte el dispositivo Android al equipo de desarrollo con un cable USB.  En lugar de **Emulador de Android de Google**, seleccione **Dispositivo**. Visual Studio implementa la aplicación en el dispositivo y luego la ejecuta. Luego podrá interactuar con la aplicación en el dispositivo.

  Las aplicaciones de pantalla compartida, como [Mobizen][20] pueden ayudarle a desarrollar una aplicación Android. Mobizen proyecta la pantalla de Android en un explorador web de su equipo.

* *En un emulador de Android:* se requieren pasos de configuración adicionales cuando se utiliza un emulador.

    Asegúrese de que va a realizar la implementación en un dispositivo virtual que tenga las API de Google establecidas como destino, como se muestra en el administrador de dispositivo virtual Android (AVD).

    ![Administrador de dispositivos virtuales de Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Si desea usar un emulador de x86 más rápido, [instale el controlador de HAXM][11] y, a continuación, configure el emulador para usarlo.

    Agregue una cuenta de Google al dispositivo Android seleccionando **Aplicaciones** > **Configuración** > **Agregar cuenta**. A continuación, siga las indicaciones.

    ![Adición de una cuenta de Google en el dispositivo Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Ejecute la aplicación todolist como antes e inserte un nuevo elemento todo. Esta vez, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificaciones para ver el texto completo de la notificación.

    ![Ver la notificación](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Optional) Configuración y ejecución en iOS

En esta sección se explica cómo ejecutar el proyecto Cordova en dispositivos de iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalación y ejecución del agente de compilación remoto de iOS en un servicio Mac o en la nube

Para poder ejecutar una aplicación de Cordova en iOS mediante Visual Studio, siga los pasos de la [guía de instalación de iOS][12] para instalar y ejecutar el agente de compilación remoto.

Asegúrese de que puede compilar la aplicación para iOS. Los pasos de la guía de instalación son necesarios para compilar para iOS desde Visual Studio. Si no tiene un equipo Mac, puede compilar para iOS mediante el agente de compilación remoto en un servicio como MacInCloud. Para más información, consulte el artículo sobre la [ejecución de una aplicación iOS en la nube][21].

> [!NOTE]
> Se requiere Xcode 7 o posterior para usar el complemento de inserción de iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Búsqueda del identificador que se va a usar como id. de la aplicación

Antes de registrar la aplicación para las notificaciones push, abra config.xml en su aplicación de Cordova, busque el valor del atributo `id` en el elemento de widget y cópielo para su uso posterior. En el siguiente XML, el identificador es `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Posteriormente, utilice este identificador al crear un id. de aplicación en el portal para desarrolladores de Apple. Si crea un identificador de aplicación diferente en el portal para desarrolladores, debe realizar algunos pasos adicionales más adelante en este tutorial. El identificador del elemento widget debe coincidir con el identificador de aplicación del portal para desarrolladores.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registro de la aplicación para notificaciones de inserción en el portal para desarrolladores de Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Ver un vídeo donde se muestren pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configuración de Azure para enviar notificaciones push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Comprobación de que el id. de aplicación coincide con la aplicación de Cordova

Si el identificador de la aplicación que creó en la cuenta para desarrolladores de Apple ya coincide con el identificador del elemento widget del archivo config.xml, puede omitir este paso. Sin embargo, si los identificadores no coinciden, siga estos pasos:

1. Elimine la carpeta platforms del proyecto.
2. Elimine la carpeta plugins del proyecto.
3. Elimine la carpeta node_modules del proyecto.
4. Actualice el atributo id del elemento widget en el archivo config.xml para que use el identificador de la aplicación que creó en la cuenta para desarrolladores de Apple.
5. Vuelva a compilar el proyecto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Prueba de las notificaciones push en su aplicación de iOS

1. En Visual Studio, asegúrese de que **iOS** está seleccionado como destino de implementación. A continuación, seleccione **Dispositivo** para ejecutar las notificaciones push en el dispositivo iOS conectado.

    Puede ejecutar las notificaciones push en un dispositivo iOS conectado a su equipo con iTunes. El simulador de iOS no admite notificaciones push.

2. Seleccione el botón **Ejecutar** o presione **F5** en Visual Studio para compilar el proyecto e iniciar la aplicación en un dispositivo iOS. Luego, seleccione **Aceptar** para aceptar las notificaciones push.

   > [!NOTE]
   > La aplicación solicita confirmación para las notificaciones push durante la primera ejecución.

3. En la aplicación, escriba una tarea y luego seleccione el icono de signo de suma **(+)** .
4. Compruebe que se recibe una notificación. A continuación, seleccione **Aceptar** para descartar la notificación.

## <a name="optional-configure-and-run-on-windows"></a>(Optional) Configuración y ejecución en Windows

En esta sección se explica cómo ejecutar el proyecto de la aplicación de Apache Cordova en dispositivos con Windows 10 (el complemento de notificaciones push de PhoneGap es compatible con Windows 10). Puede omitir esta sección si no está trabajando con dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registro de la aplicación de Windows para notificaciones push con WNS

Para utilizar las opciones de Tienda en Visual Studio, seleccione un destino de Windows en la lista Plataformas de solución, como **Windows-x64** o **Windows-x86** (evite **Windows-AnyCPU** para las notificaciones push).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Visualización de un vídeo donde se muestren pasos similares][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configuración del Centro de notificaciones para WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configuración de una aplicación de Cordova para que admita notificaciones push de Windows

Haga clic con el botón derecho en **config.xml** para abrir el diseñador de configuración. A continuación, seleccione **Diseñador de vistas**. Luego, seleccione la pestaña **Windows** y, a continuación, seleccione **Windows 10** en **Versión de Windows de destino**.

Para admitir las notificaciones push en sus compilaciones (depuración) predeterminadas, abra el archivo **build.json**. Copie la configuración de la "versión" en la configuración de depuración.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Tras la actualización, el archivo **build.json** debe contener el código siguiente:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Compile la aplicación y compruebe que no hay errores. Ahora debe registrar la aplicación cliente para las notificaciones desde el back-end de Mobile Apps. Repita esta sección para cada proyecto de Windows de la solución.

#### <a name="test-push-notifications-in-your-windows-app"></a>Prueba de las notificaciones push en su aplicación de Windows

En Visual Studio, asegúrese de que hay una plataforma de Windows seleccionada como destino de implementación, como **Windows-x64** o **Windows-x86**. Para ejecutar la aplicación en un equipo con Windows 10 que hospede Visual Studio, elija **Equipo local**.

1. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación.

2. En la aplicación, escriba un nombre para un nuevo todoitem y haga clic en el icono del signo más **(+)** para añadirlo.

Compruebe que se recibe una notificación cuando se agrega el artículo.

## <a name="next-steps"></a>Pasos siguientes

* Lea acerca de [Notification Hubs][17] para obtener información sobre las notificaciones push.
* Si aún no lo ha hecho, siga el tutorial y [añada autenticación][14] a su aplicación de Apache Cordova.

Aprenda a usar los siguientes SDK:

* [SDK de Apache Cordova][15]
* [SDK de servidor ASP.NET][1]
* [SDK de servidor Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
