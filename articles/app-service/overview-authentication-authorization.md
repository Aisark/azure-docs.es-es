---
title: 'Autenticación y autorización: Azure App Service | Microsoft Docs'
description: Referencia e información general conceptual de la característica de autenticación o autorización para Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: e308b44fffff451daa92cbf19209a1bcbfd4bff6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087985"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticación y autorización en Azure App Service

> [!NOTE]
> En este momento, AAD V2 (incluido MSAL) no se admite para Azure App Services y Azure Functions. Compruebe si hay actualizaciones.
>

Azure App Service incluye compatibilidad con autenticación y autorización para que pueda proporcionar inicio de sesión a los usuarios y acceder a los datos escribiendo una cantidad mínima de código o directamente sin código en la aplicación web, API RESTful y back-end móvil, así como [Azure Functions](../azure-functions/functions-overview.md). En este artículo se describe cómo App Service le ayuda a simplificar la autenticación y autorización para la aplicación. 

Para proteger la autenticación y la autorización es necesario entender perfectamente la seguridad, incluida la federación, el cifrado, la administración de [JSON Web Token (JWT)](https://wikipedia.org/wiki/JSON_Web_Token), los [tipos de concesión](https://oauth.net/2/grant-types/), etc. App Service proporciona estas utilidades para que pueda dedicar más tiempo y energía a proporcionar un valor empresarial a su cliente.

> [!NOTE]
> No es necesario usar App Service para la autenticación y autorización. Muchos marcos web están incluidos en las características de seguridad y puede usarlos si lo desea. Si necesita más flexibilidad de la que App Service proporciona, también puede escribir sus propias utilidades.  
>

Para información específica de aplicaciones móviles nativas, consulte [Autenticación y autorización en Azure Mobile Apps](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Cómo funciona

El módulo de autenticación y autorización se ejecuta en el mismo espacio aislado que el código de aplicación. Cuando está habilitado, cada solicitud HTTP entrante pasa a través de él antes de que el código de aplicación lo controle.

![](media/app-service-authentication-overview/architecture.png)

Este módulo controla varios aspectos de la aplicación:

- Autentica a los usuarios con el proveedor especificado
- Valida, almacena y actualiza tokens
- Administra la sesión autenticada
- Inyecta información de identidad en los encabezados de solicitud

El módulo se ejecuta por separado del código de aplicación y se configura mediante los parámetros de la aplicación. No se necesitan SDK, idiomas específicos o cambios en el código de aplicación. 

### <a name="user-claims"></a>Notificaciones de usuario

Para todos los marcos de lenguaje, App Service pone las notificaciones de usuario a disposición del código inyectándolas en los encabezados de solicitud. Para las aplicaciones de ASP.NET 4.6, App Service rellena [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) con las notificaciones del usuario autenticado, de forma que usted puede seguir el patrón de código de .NET estándar, incluido el atributo `[Authorize]`. De forma similar, para las aplicaciones PHP, App Service rellena la variable `_SERVER['REMOTE_USER']`. En el caso de las aplicaciones Java, se puede acceder a las notificaciones [desde el servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Para [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` no se hidrata para el código .NET, pero todavía puede encontrar las notificaciones de usuario en los encabezados de solicitud.

Para más información, consulte [Access user claims](app-service-authentication-how-to.md#access-user-claims) (Acceso a las notificaciones de usuario).

### <a name="token-store"></a>Almacén de tokens

App Service proporciona un almacén de tokens integrado, que es un repositorio de tokens que están asociados a los usuarios de las aplicaciones web, API o aplicaciones móviles nativas. Al habilitar la autenticación con cualquier proveedor, este almacén de tokens pasa a estar inmediatamente disponible para la aplicación, si el código de aplicación necesita acceder a los datos de estos proveedores en nombre del usuario, como: 

- publicar en la escala de tiempo de Facebook del usuario autenticado
- leer los datos corporativos del usuario de Graph API de Azure Active Directory o incluso de Microsoft Graph

Normalmente, debe escribir código para recopilar, almacenar y actualizar estos tokens en la aplicación. Con el almacén de tokens, simplemente [recupera los tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) cuando los necesita e [indica a App Service que los actualice](app-service-authentication-how-to.md#refresh-identity-provider-tokens) cuando dejan de ser válidos. 

Los tokens de identificador, los tokens de acceso y los tokens de actualización se almacenaron en caché durante la sesión autenticada y solamente el usuario asociado puede acceder a ellos.  

Si no necesita trabajar con tokens en la aplicación, puede deshabilitar el almacén de tokens.

### <a name="logging-and-tracing"></a>Registro y seguimiento

Si [habilita el registro de aplicaciones](troubleshoot-diagnostic-logs.md), verá los seguimientos de autenticación y autorización directamente en los archivos de registro. Si ve un error de autenticación que no esperaba, puede encontrar cómodamente todos los detalles examinando los registros de aplicaciones existentes. Si habilita el [seguimiento de solicitudes erróneas](troubleshoot-diagnostic-logs.md), puede ver exactamente qué rol puede haber desempeñado el módulo de autenticación y autorización en una solicitud errónea. En los registros de seguimiento, busque las referencias a un módulo denominado `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Proveedores de identidades

App Service usa la [identidad federada](https://en.wikipedia.org/wiki/Federated_identity), en la cual un proveedor de identidades de terceros almacena las identidades de usuario y el flujo de autenticación para usted. Existen cinco proveedores de identidades de forma predeterminada: 

| Proveedor | Punto de conexión de inicio de sesión |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Cuenta Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Cuando habilita la autenticación y autorización con uno de estos proveedores, su punto de conexión de inicio de sesión está disponible para la autenticación de usuarios y para la validación de tokens de autenticación del proveedor. Se puede proporcionar a los usuarios cualquier número de estas opciones de inicio de sesión con facilidad. También puede integrar otro proveedor de identidades o [su propia solución de identidad personalizada][custom-auth].

## <a name="authentication-flow"></a>Flujo de autenticación

El flujo de autenticación es el mismo para todos los proveedores, pero varía en función de si desea iniciar sesión con el SDK del proveedor:

- Sin el SDK del proveedor: la aplicación delega el inicio de sesión federado a App Service. Por lo general, suele ser el caso de las aplicaciones de explorador, que pueden presentar la página de inicio de sesión del proveedor al usuario. El código del servidor administra el proceso de inicio de sesión, por lo que también se denomina _flujo dirigido por el servidor_ o _flujo de servidor_. Este caso se aplica a las aplicaciones de explorador. También se aplica a las aplicaciones nativas que proporcionan inicio de sesión a los usuarios mediante el SDK de cliente de Mobile Apps porque el SDK abre una vista web para proporcionar inicio de sesión a los usuarios con autenticación de App Service. 
- Con el SDK del proveedor: la aplicación inicia manualmente la sesión del usuario con el proveedor y luego envía el token de autenticación a App Service para su validación. Por lo general, suele ser el caso de las aplicaciones sin explorador, que no pueden presentar la página de inicio de sesión del proveedor al usuario. El código de aplicación administra el proceso de inicio de sesión, por lo que también se denomina _flujo dirigido por el cliente_ o _flujo de cliente_. Este caso se aplica a las API REST, [Azure Functions](../azure-functions/functions-overview.md) y los clientes de explorador de JavaScript, así como a las aplicaciones de explorador que necesitan más flexibilidad en el proceso de inicio de sesión. También se aplica a las aplicaciones móviles nativas que proporciona inicio de sesión a los usuarios con el SDK del proveedor.

> [!NOTE]
> Las llamadas desde una aplicación de explorador de confianza en App Service y las llamadas a otra REST API en App Service o [Azure Functions](../azure-functions/functions-overview.md) se pueden autenticar utilizando el flujo dirigido por el servidor. Para obtener más información, consulte [Personalización de la autenticación y autorización en Azure App Service](app-service-authentication-how-to.md).
>

En la tabla siguiente se muestran los pasos del flujo de autenticación.

| Paso | Sin el SDK del proveedor | Con el SDK del proveedor |
| - | - | - |
| 1. Inicio de sesión del usuario | Redirige el cliente a `/.auth/login/<provider>`. | El código de cliente proporciona inicio de sesión al usuario directamente con el SDK del proveedor y recibe un token de autenticación. Para información, consulte la documentación del proveedor. |
| 2. Autenticación posterior | El proveedor redirige el cliente a `/.auth/login/<provider>/callback`. | El código de cliente [publica el token del proveedor](app-service-authentication-how-to.md#validate-tokens-from-providers) en `/.auth/login/<provider>` para la validación. |
| 3. Establecer la sesión autenticada | App Service agrega una cookie autenticada a la respuesta. | App Service devuelve su propio token de autenticación al código de cliente. |
| 4. Servir contenido autenticado | El cliente incluye la cookie de autenticación en las solicitudes posteriores (controladas automáticamente por explorador). | El código de cliente presenta el token de autenticación en el encabezado `X-ZUMO-AUTH` (controlado automáticamente por SDK de cliente de Mobile Apps). |

Para los exploradores del cliente, App Service puede dirigir automáticamente todos los usuarios no autenticados a `/.auth/login/<provider>`. También se pueden presentar a los usuarios uno o varios vínculos `/.auth/login/<provider>` para iniciar sesión en la aplicación con sus proveedores preferidos.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamiento de la autorización

En [Azure Portal](https://portal.azure.com), puede configurar la autorización de App Service con varios comportamientos cuando la solicitud entrante no esté autenticada.

![](media/app-service-authentication-overview/authorization-flow.png)

Los encabezados siguientes describen las opciones.

### <a name="allow-anonymous-requests-no-action"></a>Permitir solicitudes anónimas (ninguna acción)

Esta opción traslada la autorización del tráfico sin autenticar al código de aplicación. Para las solicitudes autenticadas, App Service también transfiere información de autenticación en los encabezados HTTP. 

Esta opción proporciona más flexibilidad a la hora de controlar las solicitudes anónimas. Por ejemplo, le permite [presentar varios proveedores de inicio de sesión](app-service-authentication-how-to.md#use-multiple-sign-in-providers) a los usuarios. Sin embargo, debe escribir código. 

### <a name="allow-only-authenticated-requests"></a>Permitir solo solicitudes autenticadas

La opción es **Iniciar sesión con \<proveedor>** . App Service redirige todas las solicitudes anónimas a `/.auth/login/<provider>` para el proveedor que elija. Si la solicitud anónima procede de una aplicación móvil nativa, la respuesta devuelta es `HTTP 401 Unauthorized`.

Con esta opción, no es necesario escribir ningún código de autenticación en la aplicación. Una autorización más precisa, como la autorización específica de rol, se puede controlarse mediante la inspección de las notificaciones del usuario (consulte [Access user claims](app-service-authentication-how-to.md#access-user-claims) (Acceso a las notificaciones de usuario)).

> [!CAUTION]
> Este método de restricción del acceso se aplica a todas las llamadas a la aplicación, lo que puede no ser deseable para las aplicaciones que necesitan una página de inicio disponible públicamente, como muchas aplicaciones de una sola página.

## <a name="more-resources"></a>Más recursos

[Tutorial: Autenticación y autorización de usuarios de un extremo a otro en Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticación y autorización de usuarios de un extremo a otro en Azure App Service para Linux](containers/tutorial-auth-aad.md)  
[Customize authentication and authorization in App Service](app-service-authentication-how-to.md) (Personalización de la autenticación y autorización en App Service)

Guías de procedimientos específicas del proveedor:

* [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory][AAD]
* [Configuración de la aplicación para usar el inicio de sesión de Facebook][Facebook]
* [Configuración de la aplicación para usar el inicio de sesión de Google][Google]
* [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft][MSA]
* [Configuración de la aplicación para usar el inicio de sesión de Twitter][Twitter]
* [Uso de Uso de la autenticación personalizada en una aplicación][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
