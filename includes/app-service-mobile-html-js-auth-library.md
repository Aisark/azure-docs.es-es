---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "67186826"
---
### <a name="how-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Autenticación con un proveedor (flujo de servidor)
Para que Mobile Apps administre el proceso de autenticación en su aplicación, debe registrar esta última en el proveedor de identidades. A continuación, en Azure App Service, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor.
Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Una vez que haya registrado el proveedor de identidades, simplemente llame al método `.login()` con el valor del proveedor. Por ejemplo, para iniciar sesión con Facebook, use el siguiente código:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Los valores válidos para el proveedor son "aad", "facebook", "google", "microsoftaccount" y "twitter".

> [!NOTE]
> Autenticación de Google no funciona actualmente a través del flujo de servidor.  Para realizar la autenticación con Google, debe usar un [método de flujo de cliente](#client-auth).

En este caso, Azure App Service administra el flujo de autenticación de OAuth 2.0.  Muestra la página de inicio de sesión del proveedor seleccionado y genera un token de autenticación de App Service después de iniciar sesión correctamente con el proveedor de identidades. La función login, cuando se completa, devuelve un objeto JSON que expone el identificador de usuario y el token de autenticación de App Service en los campos userId y authenticationToken, respectivamente. El token puede almacenarse en caché y volver a usarse hasta que expire.

### <a name="how-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Autenticación con un proveedor (flujo de cliente)

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y proporcionar el token devuelto a App Service para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

#### <a name="social-authentication-basic-example"></a>Ejemplo básico de autenticación social

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
En este ejemplo se asume que el token proporcionado por el SDK del proveedor correspondiente se almacena en la variable token.

### <a name="how-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Obtención de información sobre el usuario autenticado

Se puede recuperar la información de autenticación desde el punto de conexión `/.auth/me` mediante una llamada HTTP con cualquier biblioteca de AJAX.  Asegúrese de establecer el encabezado `X-ZUMO-AUTH` en el token de autenticación.  El token de autenticación se almacena en `client.currentUser.mobileServiceAuthenticationToken`.  Por ejemplo, para usar la API de captura:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

La captura está disponible como [un paquete npm](https://www.npmjs.com/package/whatwg-fetch) o para descarga desde el explorador desde [CDNJS](https://cdnjs.com/libraries/fetch). También podría utilizar jQuery u otra API de AJAX para capturar la información.  Los datos se recibieron como un objeto JSON.
