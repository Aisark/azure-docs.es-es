---
title: Uso del complemento de Apache Cordova para Azure Mobile Apps
description: Uso del complemento de Apache Cordova para Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25bff77da6c2bd89916fe40ebb153ff9631e62eb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388803"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Uso de una biblioteca de cliente de Apache Cordova para Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center admite servicios integrados de un extremo a otro fundamentales para el desarrollo de aplicaciones móviles. Los desarrolladores pueden usar los servicios de **compilación**, **prueba** y **distribución** para configurar la canalización de integración y entrega continuas. Una vez que se ha implementado la aplicación, los desarrolladores pueden supervisar el estado y el uso de su aplicación con los servicios de **análisis** y **diagnóstico**, e interactuar con los usuarios que utilizan el servicio de **Push** (inserción). Además, los desarrolladores pueden aprovechar **Auth** para autenticar a los usuarios y el servicio de **datos** para almacenar y sincronizar los datos de la aplicación en la nube.
>
> Si está pensando en integrar servicios en la nube en su aplicación para dispositivos móviles, regístrese en [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoy mismo.

## <a name="overview"></a>Información general
En esta guía se muestran algunos escenarios comunes del uso del último [complemento de Apache Cordova para Azure Mobile Apps]. Si no está familiarizado con Azure Mobile Apps, realice primero el tutorial [Guía de inicio rápido de Azure Mobile Apps] para crear un back-end, crear una tabla y descargar un proyecto de Apache Cordova previamente compilado. En esta guía nos centramos en el complemento de Apache Cordova del lado cliente.

## <a name="supported-platforms"></a>Plataformas compatibles
Este SDK es compatible con la versión 6.0.0 y posterior de Apache Cordova en dispositivos iOS, Android y Windows.  Las plataformas compatibles con las siguientes:

* Niveles de API de Android del 19 al 24 (de KitKat a Nougat)
* Versiones 8.0 y posteriores de iOS.
* Windows Phone 8.1
* Plataforma universal de Windows

## <a name="Setup"></a>Configuración y requisitos previos
En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que ha agregado el complemento de Apache Cordova al código.  Si no lo ha hecho, puede agregar el complemento Apache Cordova al proyecto en la línea de comandos:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para más información sobre la creación de [su primera aplicación de Apache Cordova], consulte su documentación.

## <a name="ionic"></a>Configuración de una aplicación de Ionic v2

Para configurar correctamente un proyecto de Ionic v2, primero cree una aplicación básica y agregue el complemento de Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Agregue las siguientes líneas a `app.component.ts` para crear el objeto de cliente:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Ahora puede compilar y ejecutar el proyecto en el explorador:

```
ionic platform add browser
ionic run browser
```

El complemento de Cordova de Azure Mobile Apps es compatible con Ionic v1 y v2.  Solo las aplicaciones de Ionic v2 requieren la declaración adicional para el objeto `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Instrucciones: Autenticación de usuarios
Azure App Service admite la autenticación y autorización de usuarios de la aplicación que usan diversos proveedores de identidades externos: Facebook, Google, cuenta Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación] .

Al utilizar la autenticación en una aplicación de Apache Cordova, los siguientes complementos de Cordova deben estar disponibles:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Se admiten dos flujos de autenticación: un flujo de servidor y un flujo de cliente.  El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Instrucciones: Cómo configurar su servicio de aplicaciones móviles para URL de redireccionamiento externas
Varios tipos de aplicaciones de Apache Cordova utilizan una función de bucle invertido para controlar los flujos de la interfaz de usuario de OAuth.  Los flujos de interfaz de usuario de OAuth conllevan problemas, ya que el servicio de autenticación solo sabe cómo utilizar el servicio de manera predeterminada.  Ejemplos de flujos de interfaz de usuario de OAuth problemáticos:

* El emulador Ripple
* La característica Live Reload con Ionic
* Ejecución del back-end móvil localmente
* Ejecución del back-end móvil en una instancia de Azure App Service diferente a la que proporciona la autenticación.

Siga estas instrucciones para agregar los ajustes locales a la configuración:

1. Inicie sesión en el [Azure Portal]
2. Seleccione **Todos los recursos** o **App Services** y haga clic en el nombre de la aplicación móvil.
3. Haga clic en **Herramientas**
4. Haga clic en la opción **Explorador de recursos** del menú OBSERVAR y, después, en **Ir**.  Se abrirá una nueva ventana o pestaña.
5. Expanda los nodos **config** y **authsettings** de su sitio en el panel de navegación izquierdo.
6. Haga clic en **Editar**
7. Busque el elemento "allowedExternalRedirectUrls".  Se puede establecer en Null o una matriz de valores.  Cambie el valor al siguiente:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sustituya las URL por las de su servicio.  Algunos ejemplos son "`http://localhost:3000`" (para el servicio de ejemplo de Node.js) o "`http://localhost:4400`" (para el servicio de Ripple).  Sin embargo, estas URL no son más que ejemplos. Es decir, su situación puede ser distinta, incluso si debe configurar los servicios mencionados en los ejemplos.
8. Haga clic en el botón **Lectura/escritura** situado en la esquina superior derecha de la pantalla.
9. Haga clic en el botón verde **PUT** .

La configuración se guardará en este momento.  No cierre la ventana del explorador hasta que la configuración haya terminado de guardarse.
Agregue también estas URL de bucle invertido a la configuración de CORS:

1. Inicie sesión en el [Azure Portal]
2. Seleccione **Todos los recursos** o **App Services** y haga clic en el nombre de la aplicación móvil.
3. Se abrirá automáticamente la hoja Configuración.  En caso contrario, haga clic en **Toda la configuración**.
4. Haga clic en la opción **CORS** del menú API.
5. Escriba la dirección URL que quiera agregar en el cuadro proporcionado y presione Intro.
6. Escriba las direcciones URL adicionales que necesite.
7. Haga clic en **Guardar** para guardar la configuración.

Los nuevos ajustes tardarán aproximadamente entre 10 y 15 segundos en surtir efecto.

## <a name="register-for-push"></a>Instrucciones: Registro de notificaciones push
Instale [phonegap-plugin-push] para administrar las notificaciones push.  Este complemento se puede agregar fácilmente mediante el comando `cordova plugin add` en la línea de comandos o por medio del instalador de complementos Git dentro de Visual Studio.  El siguiente código de la aplicación de Apache Cordova registrará el dispositivo para notificaciones push:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use el SDK de Notification Hubs para enviar notificaciones push desde el servidor.  No envíe nunca notificaciones push directamente desde la aplicación, ya que podría usarse para desencadenar un ataque de denegación de servicio en Notification Hubs o el PNS.  El PNS puede prohibir el tráfico como resultado de estos ataques.

## <a name="more-information"></a>Más información

Puede encontrar información detallada sobre las API en nuestra [documentación de la API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Guía de inicio rápido de Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[complemento de Apache Cordova para Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[su primera aplicación de Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
