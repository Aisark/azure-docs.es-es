---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "60297710"
---
## <a name="test-your-code"></a>Prueba del código

Presione **F5** para ejecutar el proyecto en Visual Studio. La aplicación **MainWindow** se muestra tal como aparece aquí:

![Prueba de la aplicación](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

La primera vez que ejecute la aplicación y seleccione el botón **Llamar a Microsoft Graph API**, se le pedirá que inicie sesión. Para probar la aplicación, use una cuenta de Azure Active Directory (cuenta profesional o educativa) o una cuenta Microsoft (live.com, outlook.com).

![Inicie sesión en la aplicación.](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Consentimiento para el acceso a la aplicación

La primera vez que inicie sesión en su aplicación, también se le pedirá que dé su consentimiento para permitir que la aplicación acceda a su perfil e inicie sesión, como se muestra a continuación:

![Consentimiento para el acceso a la aplicación](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visualización de los resultados de la aplicación

Después de iniciar sesión, verá la información del perfil de usuario que devuelve la llamada a Microsoft Graph API. Los resultados se muestran en el cuadro **API Call Results** (Resultados de la llamada a la API). La información básica sobre el token que se ha adquirido a través de la llamada a `AcquireTokenInteractive` o a `AcquireTokenSilent` debe estar visible en el cuadro **Información de token**. Los resultados contienen las siguientes propiedades:

|Propiedad  |Formato  |DESCRIPCIÓN |
|---------|---------|---------|

|**Nombre de usuario**  | <span> user@domain.com </span> | El nombre de usuario que se utiliza para identificar al usuario. | | **Token expira** | Fecha y hora | La hora a la que expira el token. MSAL amplía la fecha de expiración al renovar el token según sea necesario.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito *user.read* para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se van a registrar en el Portal de registro de aplicaciones. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Microsoft Graph API requiere el ámbito *Calendars.read* para mostrar los calendarios del usuario.

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado *Calendars.Read* a la información del registro de la aplicación. A continuación, agregue el ámbito *Calendars.Read* a la llamada `acquireTokenSilent`.

>[!NOTE]
>Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
