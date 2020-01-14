---
title: Protocolos OAuth 2.0 y OpenID Connect - Plataforma de identidad de Microsoft | Azure
description: Una guía a los protocolos OAuth 2.0 y OpenID Connect compatibles con el punto de conexión de la Plataforma de identidad de Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b25f1753e6dcb415927ee6278c36b3b179dd353
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424727"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protocolos OAuth 2.0 y OpenID Connect en la Plataforma de identidad de Microsoft

El punto de conexión de la Plataforma de identidad de Microsoft para identidad como servicio con los protocolos estándar del sector, OpenID Connect y OAuth 2.0. Aunque el servicio sea compatible con el estándar, puede haber diferencias sutiles entre dos implementaciones cualquiera de estos protocolos. La información que aquí se describe será útil si decide escribir su código mediante el envío y la administración directos de solicitudes HTTP o mediante el uso de una biblioteca de código abierto de terceros, en lugar de usar una de nuestras [bibliotecas de código abierto](reference-v2-libraries.md).

> [!NOTE]
> No todas las características y escenarios de Azure AD son compatibles con el punto de conexión de la plataforma de identidad de Microsoft. Para determinar si debe usar el punto de conexión de la plataforma de identidad de Microsoft, conozca las [limitaciones de dicha plataforma](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Conceptos básicos

En casi todos los flujos de OAuth 2.0 y OpenID Connect hay cuatro partes implicadas en el intercambio:

![Diagrama que muestra los roles de OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* El **servidor de autorización** es el punto de conexión de la Plataforma de identidad de Microsoft y el responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. Al servidor de autorización también se le conoce como proveedor de identidad: controla de forma segura todo lo que tenga que ver con la información del usuario, su acceso y las relaciones de confianza entre las partes de un flujo.
* El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.
* El **cliente de OAuth** es su aplicación, identificada por su identificador de aplicación. El cliente de OAuth suele ser la parte con la que interactúa el usuario final y solicita tokens del servidor de autorización. El cliente debe contar con el permiso del propietario del recurso para acceder a este.
* El **servidor de recursos** es donde residen el recurso o los datos. Confía en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura y usa access_tokens de portador para garantizar que se puede conceder el acceso a un recurso.

## <a name="app-registration"></a>Registro de aplicación

Todas las aplicaciones que quieren aceptar tanto cuentas personales como profesionales o educativas se deben registrar a través de la experiencia de **Registros de aplicaciones** en [Azure Portal](https://aka.ms/appregistrations) antes de poder iniciar la sesión de estos usuarios mediante OAuth 2.0 u OpenID Connect. El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

* Un **id. de aplicación** que identifica de forma única su aplicación
* Un **URI de redireccionamiento** (opcional) que puede usarse para dirigir las respuestas de nuevo a la aplicación.
* Algunos otros valores específicos de cada escenario.

Para obtener más información, aprenda a [registrar una aplicación](quickstart-register-app.md).

## <a name="endpoints"></a>Puntos de conexión

Una vez que la aplicación se registra, se comunica con la Plataforma de identidad de Microsoft mediante el envío de solicitudes al punto de conexión:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Donde `{tenant}` puede adoptar uno de cuatro valores:

| Value | Descripción |
| --- | --- |
| `common` | Permite que los usuarios con cuentas personales de Microsoft y con cuentas profesionales o educativas de Azure AD inicien sesión en la aplicación. |
| `organizations` | Permite que solo los usuarios con cuentas profesionales o educativas de Azure AD inicien sesión en la aplicación. |
| `consumers` | Permite que solo los usuarios con cuentas personales de Microsoft (MSA) inicien sesión en la aplicación. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Permite que solo los usuarios con cuentas profesionales o educativas de un inquilino específico de Azure AD inicien sesión en la aplicación. Puede usarse el nombre de dominio descriptivo del inquilino de Azure AD o bien el identificador de GUID del inquilino. |

Para obtener información sobre cómo interactuar con estos puntos de conexión, elija un tipo de aplicación en particular en la sección [Protocolos](#protocols) y siga los vínculos para obtener más información.

> [!TIP]
> Cualquier aplicación registrada en Azure AD puede usar el punto de conexión de la Plataforma de identidad de Microsoft, incluso si no inician sesión en cuentas personales.  De este modo, puede migrar las aplicaciones existentes a la Plataforma de identidad de Microsoft y [MSAL](reference-v2-libraries.md) sin volver a crear la aplicación.  

## <a name="tokens"></a>Tokens

La implementación de OAuth 2.0 y OpenID Connect para la Plataforma de identidad de Microsoft hace un uso generalizado de tokens de portador, incluidos los representados como JWT. Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con la Plataforma de identidad de Microsoft para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token de portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso no autorizado a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](https://tools.ietf.org/html/rfc6750).

Encontrará más información sobre los distintos tipos de tokens que se usan en el punto de conexión de la Plataforma de identidad de Microsoft en [la referencia de token de punto de acceso de la Plataforma de identidad de Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Si está listo para ver algunas solicitudes de ejemplo, comience con uno de los siguiente tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar cuál es el flujo correcto para usted, vea [los tipos de aplicaciones que puede compilar con la Plataforma de identidad de Microsoft](v2-app-types.md).

* [Creación de aplicaciones móviles y nativas con OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Creación de aplicaciones web con OpenID Connect](v2-protocols-oidc.md)
* [Creación de aplicaciones de una sola página con el flujo implícito de OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Creación de demonios o procesos del lado servidor con el flujo de credenciales de cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtención de tokens en una API web con el flujo "en nombre de" de OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
