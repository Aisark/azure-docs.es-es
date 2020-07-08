---
title: Problemas en la configuración de SSO con contraseña para una aplicación de la galería de Azure AD
description: Comprender los problemas más comunes a los que se enfrentan los usuarios al configurar un inicio de sesión único con contraseña para las aplicaciones que ya se muestran en la galería de aplicaciones de Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fda2657a68fc8a3fd293c0c6001a71f5970548
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763523"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problemas en la configuración del inicio de sesión único con contraseña para una aplicación de la galería de Azure AD

Este artículo le ayuda a conocer los problemas habituales a los que se enfrentan los usuarios al configurar un **inicio de sesión único con contraseña** con una aplicación de la galería de Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Se rellenan las credenciales, pero la extensión no las envía

En general, este problema sucede si el proveedor de la aplicación ha cambiado recientemente la página de inicio de sesión para agregar un campo, cambiar un identificador que se utiliza para detectar los campos de nombre de usuario y contraseña, o modificar cómo funciona la experiencia de inicio de sesión para su aplicación. Afortunadamente, en muchos casos, Microsoft puede trabajar con los proveedores de aplicaciones para resolver rápidamente estos problemas.

Aunque Microsoft tiene tecnologías para detectar automáticamente cuándo se interrumpen estas integraciones, puede que no sea posible encontrar estos problemas de forma inmediata o que se tarde algún tiempo en corregirlos. En el caso de que una de estas integraciones no funcione correctamente, abra un caso de soporte técnico para que podamos solucionar el problema lo más rápido posible.

**Si está en contacto con el proveedor de la aplicación,** pídale que se comunique con Microsoft para que podamos trabajar juntos con el fin de integrar de forma nativa su aplicación con Azure Active Directory. Para empezar puede enviar al proveedor a [Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Las credenciales se rellenan y se envían, pero la página indica que son incorrectas

Para resolver este problema, intente primero estas cosas:

- Pídale al usuario que intente primero **iniciar sesión en el sitio web de la aplicación directamente** con las credenciales almacenadas para dicho sitio.

  * Si el inicio de sesión funciona, a continuación, indíquele al usuario que haga clic en el botón **Actualizar credenciales** situado en el **icono de la aplicación** en la sección de **aplicaciones** del [panel de acceso a las aplicaciones](https://myapps.microsoft.com/) para actualizarlas con el nombre de usuario y la contraseña más recientes.

  * Si usted u otro administrador ha asignado las credenciales para este usuario, busque la asignación de aplicación del usuario o del grupo yendo a la pestaña **Usuarios y grupos** de la aplicación, seleccione la asignación y haga clic en el botón **Actualizar credenciales**.

- Si el usuario se asignó sus propias credenciales, pídale al usuario **que compruebe si su contraseña ha expirado en la aplicación** y si es así, **que actualice su contraseña expirada** iniciando sesión directamente en la aplicación.

  * Después de actualizar la contraseña en la aplicación, indíquele al usuario que haga clic en el botón **Actualizar credenciales** situado en el **icono de la aplicación** en la sección de **aplicaciones** del [panel de acceso a las aplicaciones](https://myapps.microsoft.com/) para actualizarlas con el nombre de usuario y la contraseña más recientes.

  * Si usted u otro administrador ha asignado las credenciales para este usuario, busque la asignación de aplicación del usuario o del grupo yendo a la pestaña **Usuarios y grupos** de la aplicación, seleccione la asignación y haga clic en el botón **Actualizar credenciales**.

- Dígale al usuario que actualice la extensión de explorador del Panel de acceso siguiendo los pasos que aparecen más adelante en la sección [Cómo instalar la extensión de explorador del Panel de acceso](#how-to-install-the-access-panel-browser-extension).

- Compruebe que la extensión de explorador del Panel de acceso está habilitada y se está ejecutando en el explorador del usuario.

- Asegúrese de que los usuarios no intentan iniciar sesión en la aplicación desde el Panel de acceso en **los modos incógnito, inPrivate o privado**. La extensión del Panel de acceso no es compatible con estos modos.

Si las sugerencias anteriores no funcionan, puede que se haya producido un cambio en el lado de la aplicación que haya interrumpido provisionalmente la integración de la aplicación con Azure AD. Por ejemplo, esto puede ocurrir cuando el proveedor de la aplicación introduce un script en su página que tiene un comportamiento diferentes con las entradas manuales y con las automatizadas, lo que hace que una integración automatizada, como la nuestra, se interrumpa. Afortunadamente, en muchos casos, Microsoft puede trabajar con los proveedores de aplicaciones para resolver rápidamente estos problemas.

Aunque Microsoft tiene tecnologías para detectar automáticamente cuándo se interrumpen las integraciones de aplicaciones, puede que no sea posible encontrar estos problemas de forma inmediata o que se tarde algún tiempo en corregirlos. Cuando una integración no funciona correctamente, puede abrir un caso de soporte técnico para que podamos corregirlo lo antes posible. 

Además, **si está en contacto con el proveedor de la aplicación,** **pídale que cuente con nosotros** para que podamos trabajar juntos con el fin de integrar de forma nativa su aplicación con Azure Active Directory. Para empezar puede enviar al proveedor a [Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>La extensión funciona en Chrome y Firefox, pero no en Internet Explorer

Hay dos principales causas de este problema:

- Dependiendo de la configuración de seguridad habilitada en Internet Explorer, si el sitio web no es parte de una **Zona de confianza**, a veces se impide que nuestro script ejecute la aplicación.

  *  Para resolver este problema, pida al usuario que **agregue el sitio web de la aplicación** a la lista de **Sitios de confianza** en la **configuración de seguridad de Internet Explorer**. Puede enviar a los usuarios al artículo sobre [cómo agregar un sitio a la lista de sitios de confianza](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) para obtener instrucciones detalladas.

- En circunstancias excepcionales, la validación de seguridad de Internet Explorer puede ocasionar a veces que la página se cargue más lentamente que la ejecución del script.

  * Desgraciadamente esta situación puede variar dependiendo de la versión del explorador, la velocidad del equipo o el sitio visitado. En este caso, se recomienda ponerse en contacto con soporte técnico para que podamos solucionar la integración para esta aplicación específica.

Además, **si está en contacto con el proveedor de la aplicación,** **pídale que cuente con nosotros** para que podamos trabajar juntos con el fin de integrar de forma nativa su aplicación con Azure Active Directory. Para empezar puede enviar al proveedor a [Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Compruebe si la página de inicio de sesión de la aplicación ha cambiado recientemente, o si requiere un campo adicional

Si la página de inicio de sesión de la aplicación ha cambiado de forma considerable, esto hace que a veces nuestras integraciones se interrumpan. Un ejemplo de esto es cuando un proveedor de aplicación agrega un campo de inicio de sesión, un captcha o una autenticación multifactor a sus experiencias. Afortunadamente, en muchos casos, Microsoft puede trabajar con los proveedores de aplicaciones para resolver rápidamente estos problemas.

Aunque Microsoft tiene tecnologías para detectar automáticamente cuándo se interrumpen las integraciones de aplicaciones, puede que no sea posible encontrar estos problemas de forma inmediata o que se tarde algún tiempo en corregirlos. Cuando una integración no funciona correctamente, puede abrir un caso de soporte técnico para que podamos corregirlo lo antes posible. 

Además, **si está en contacto con el proveedor de la aplicación,** **pídale que cuente con nosotros** para que podamos trabajar juntos con el fin de integrar de forma nativa su aplicación con Azure Active Directory. Para empezar puede enviar al proveedor a [Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Cómo instalar la extensión de explorador del Panel de acceso

Para instalar la extensión de explorador del Panel de acceso, siga estos pasos:

1.  Abra el [Panel de acceso](https://myapps.microsoft.com) en uno de los exploradores admitidos e inicie sesión como **usuario** en su instancia de Azure AD.

2.  Haga clic en una **aplicación de SSO con contraseña** en el Panel de acceso.

3.  En el mensaje que le pregunta si desea instalar el software, seleccione **Instalar ahora**.

4.  Se le dirigirá al vínculo de descarga en función del explorador. **Agregue** la extensión al explorador.

5.  Si el explorador lo solicita, seleccione **Habilitar** o **Permitir** la extensión.

6.  Una vez instalada, **reinicie** la sesión del explorador.

7.  Inicie sesión en el Panel de acceso y vea si puede **iniciar** las aplicaciones de SSO con contraseña

También puede descargar la extensión para Chrome y Firefox desde los siguientes vínculos directos:

-   [Extensión del Panel de acceso para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensión del panel de acceso para Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](application-proxy-configure-single-sign-on-with-kcd.md)

