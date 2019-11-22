---
title: Inicio rápido de la aplicación web de OIDC en Node.js para la plataforma de identidad de Microsoft | Azure
description: Aprenda a implementar la autenticación en una aplicación web de Node.js mediante OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac3344fe16d24ae116a5fde289421998f11f18
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182033"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>Inicio rápido: incorporación del inicio de sesión mediante OIDC a una aplicación web de Node.js

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

En esta guía de inicio rápido, aprenderá a configurar la autenticación de OpenID Connect en una aplicación web creada mediante Node.js con Express. El ejemplo está diseñado para ejecutarse en cualquier plataforma.

## <a name="prerequisites"></a>Requisitos previos

Para ejecutar este ejemplo, necesitará lo siguiente:

* Instalar Node.js desde http://nodejs.org/.

* Una [cuenta de Microsoft](https://www.outlook.com) o del [programa de desarrolladores de Office 365](/office/developer-program/office-365-developer-program).

## <a name="register-your-application"></a>Registrar su aplicación 
1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta personal, profesional o educativa de Microsoft.
1. Si su cuenta está presente en más de un inquilino de Azure AD:
    - Seleccione su perfil en el menú en la esquina superior derecha de la página y, a continuación, **Cambiar directorio**.
    - Cambie la sesión al inquilino de Azure AD en el que desea crear la aplicación.

1. Vaya a [Azure Active Directory > Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar la aplicación.

1. Seleccione **Nuevo registro**.

1. Cuando aparezca la página **Registrar una aplicación**, escriba la información de registro de la aplicación:
    - En la sección **Nombre**, escriba un nombre significativo que se mostrará a los usuarios de la aplicación. Por ejemplo:  MyWebApp
    - En **Tipos de cuenta compatibles**, seleccione **Cuentas en cualquier directorio organizativo y cuentas Microsoft personales (por ejemplo, Skype, Xbox, Outlook.com)** .

    Si hay más de un URI de redirección, deberá agregarlos desde la pestaña **Autenticación** más adelante, después de que la aplicación se haya creado correctamente.

1. Seleccione **Registrar** para crear la aplicación.

1. En la página **Información general** de la aplicación, busque el valor de **Id. de aplicación (cliente)** y regístrelo para usarlo más tarde. Necesitará este valor para configurar la aplicación más adelante en este proyecto.

1. En la lista de páginas de la aplicación, seleccione **Autenticación**.
    - En la sección **URI de redirección**, seleccione **Web** en el cuadro combinado y escriba el siguiente URI de redirección: `http://localhost:3000/auth/openid/return`.
    - En la sección **Configuración avanzada**, establezca **Dirección URL de cierre de sesión** en `http://localhost:3000`.
    - En la sección **Configuración avanzada > Concesión implícita**, marque **Tokens de id.** ya que en este ejemplo es necesario habilitar el [flujo de concesión implícito](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) para iniciar la sesión del usuario.

1. Seleccione **Guardar**.

1. Desde la página **Certificados y secretos**, en la sección **Secretos de cliente**, seleccione **Nuevo secreto de cliente**.
    - Introduzca una descripción de clave (por ejemplo, secreto de aplicación).
    - Seleccione la duración de clave **En 1 año, En 2 años** o **Nunca expira**.
    - Al hacer clic en el botón **Agregar**, se mostrará el valor de la clave. Copie el valor de clave y guárdelo en una ubicación segura.

    Necesitará esta clave más adelante para configurar la aplicación. Este valor de clave no se volverá a mostrar ni se recuperará de ningún otro modo, por lo que debe registrarlo en cuanto esté visible desde el Azure Portal.

## <a name="download-the-sample-application-and-modules"></a>Descarga de la aplicación de ejemplo y los módulos

A continuación, clone el repositorio de ejemplo e instale los módulos de NPM.

Desde el shell o la línea de comandos, ejecute:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

o

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

En el directorio raíz del proyecto, ejecute el comando:

`$ npm install`  

## <a name="configure-the-application"></a>Configuración de la aplicación

Especifique los parámetros de `exports.creds` en config.js como se indica.

* Actualice `<tenant_name>` en `exports.identityMetadata` con el nombre de inquilino de Azure AD en el formato \*.onmicrosoft.com.
* Actualice `exports.clientID` con el Id. de la aplicación que anotó en el registro de la aplicación.
* Actualice `exports.clientSecret` con el secreto de la aplicación que anotó en el registro de la aplicación.
* Actualice `exports.redirectUrl` con el URI de redirección que anotó en el registro de la aplicación.

**Configuración opcional para las aplicaciones de producción:**

* Actualice `exports.destroySessionUrl` en config.js si quiere utilizar un `post_logout_redirect_uri` diferente.

* Establezca `exports.useMongoDBSessionStore` en config.js en true si quiere usar [mongoDB](https://www.mongodb.com) u otros [almacenes de sesión compatibles](https://github.com/expressjs/session#compatible-session-stores).
El almacén de sesión predeterminado de este ejemplo es `express-session`. El almacén de sesión predeterminado no es adecuado para entornos de producción.

* Actualice `exports.databaseUri` si quiere utilizar el almacén de sesiones de mongoDB y un URI de base de datos diferente.

* Actualice `exports.mongoDBSessionMaxAge`. Aquí puede especificar cuánto tiempo quiere conservar una sesión en mongoDB. La unidad es segundo(s).

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación

Inicie el servicio de mongoDB. Si usa el almacén de sesiones de mongoDB en esta aplicación, debe [instalar mongoDB](http://www.mongodb.org/) e iniciar primero el servicio. Si usa el almacén de sesiones predeterminado, puede omitir este paso.

Ejecute la aplicación mediante el siguiente comando desde la línea de comandos.

```
$ node app.js
```

**¿Es difícil comprender la salida del servidor?:** Se usó `bunyan` para el registro en este ejemplo. La consola no tendrá mucho sentido a menos que también instale bunyan y ejecute el servidor como se mostró anteriormente, aunque canalizado a través del archivo binario bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>¡Y ya está!

Tendrá un servidor ejecutándose correctamente en `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el escenario de aplicación web que admite la plataforma de identidad de Microsoft:
> [!div class="nextstepaction"]
> [Escenario de una aplicación web que permite iniciar sesión a los usuarios](scenario-web-app-sign-user-overview.md)
