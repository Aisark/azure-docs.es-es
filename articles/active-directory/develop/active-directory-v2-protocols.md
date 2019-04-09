---
title: Más información acerca de los protocolos de autorización compatibles con Azure AD v2.0 | Microsoft Docs
description: Guía de los protocolos que admite el punto de conexión de Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56970091da74cfc389d60ad91f430fcb64d4bba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266977"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>Protocolos de v2.0: OAuth 2.0 y OpenID Connect

El punto de conexión v2.0 puede usar Azure Active Directory (Azure AD) para identidad como servicio con protocolos estándar del sector, OpenID Connect y OAuth 2.0. Aunque el servicio sea compatible con el estándar, puede haber diferencias sutiles entre dos implementaciones cualquiera de estos protocolos. La información que aquí se describe será útil si decide escribir su código mediante el envío y la administración directos de solicitudes HTTP o mediante el uso de una biblioteca de código abierto de terceros, en lugar de usar una de nuestras [bibliotecas de código abierto](reference-v2-libraries.md).

> [!NOTE]
> No todas las características ni escenarios de Azure AD son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Conceptos básicos

En casi todos los flujos de OAuth 2.0 y OpenID Connect hay cuatro partes implicadas en el intercambio:

![Funciones de OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* El **servidor de autorización** es el punto de conexión v2.0 y el responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. Al servidor de autorización también se le conoce como proveedor de identidad: controla de forma segura todo lo que tenga que ver con la información del usuario, su acceso y las relaciones de confianza entre las partes de un flujo.
* El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.
* El **cliente de OAuth** es su aplicación, identificada por su identificador de aplicación. El cliente de OAuth suele ser la parte con la que interactúa el usuario final y solicita tokens del servidor de autorización. El cliente debe contar con el permiso del propietario del recurso para acceder a este.
* El **servidor de recursos** es donde residen el recurso o los datos. Se confía en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura y usa tokens de acceso de portador para asegurarse de que se puede conceder acceso a un recurso.

## <a name="app-registration"></a>Registro de aplicaciones

Se deben registrar todas las aplicaciones que desea que acepte tanto personales y cuentas profesionales o educativas a través de la nueva **registros de aplicaciones (versión preliminar)** experiencia en el [portal Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) para estos usuarios puedan iniciar sesión uso de OAuth 2.0 u OpenID Connect. El proceso de registro de la aplicación recopilará y asignará algunos valores a la aplicación:

* Un **id. de aplicación** que identifica de forma única su aplicación
* Un **URI de redirección** o **identificador de paquete** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación
* Algunos otros valores específicos de cada escenario.

Para obtener más información, aprenda a [registrar una aplicación](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Puntos de conexión

Una vez registrada, la aplicación se comunica a Azure AD mediante el envío de solicitudes al extremo v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Donde `{tenant}` puede adoptar uno de cuatro valores:

| Valor | DESCRIPCIÓN |
| --- | --- |
| `common` | Permite que los usuarios con cuentas personales de Microsoft y con cuentas profesionales o educativas de Azure AD inicien sesión en la aplicación. |
| `organizations` | Permite que solo los usuarios con cuentas profesionales o educativas de Azure AD inicien sesión en la aplicación. |
| `consumers` | Permite que solo los usuarios con cuentas personales de Microsoft (MSA) inicien sesión en la aplicación. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Permite que solo los usuarios con cuentas profesionales o educativas de un inquilino específico de Azure AD inicien sesión en la aplicación. Puede usarse el nombre de dominio descriptivo del inquilino de Azure AD o bien el identificador de GUID del inquilino. |

Para obtener información sobre cómo interactuar con estos puntos de conexión, elija un tipo de aplicación en particular en la sección [Protocolos](#protocols) y siga los vínculos para obtener más información.

> [!TIP]
> Cualquier aplicación registrada en Azure AD puede usar el punto de conexión v2.0, incluso si no inician sesión las cuentas personales.  De este modo, puede migrar las aplicaciones existentes a la versión 2.0 y [MSAL](reference-v2-libraries.md) sin volver a crear la aplicación.  

## <a name="tokens"></a>Tokens

La implementación v2.0 de OAuth 2.0 y OpenID Connect hace un uso generalizado de tokens de portador, incluidos los representados como JWT. Un token portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido. En este sentido, el "portador" es cualquier parte que pueda presentar el token. Aunque una parte debe autenticarse primero con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para asegurar el token en la transmisión y el almacenamiento, este puede interceptarse y ser utilizado por un usuario no deseado. Mientras que algunos tokens de seguridad disponen de un mecanismo integrado para evitar ser usados por partes no autorizadas, los tokens portadores no tienen este mecanismo y deben transportarse en un canal seguro como, por ejemplo, la seguridad de la capa de transporte (HTTPS). Si un token de portador se transmite sin cifrar, un usuario malintencionado puede utilizar un ataque de tipo "Man in the middle" para adquirir el token y usarlo para obtener acceso no autorizado a un recurso protegido. Los mismos principios de seguridad se aplican al almacenamiento o almacenamiento en caché de tokens portadores para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura. Para otras consideraciones sobre la seguridad de los tokens portadores, consulte la [Sección 5 de RFC 6750](https://tools.ietf.org/html/rfc6750).

Encontrará más detalles sobre los diferentes tipos de token que se usan en el punto de conexión v2.0 en [la referencia de token del punto de conexión v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Si está listo para ver algunas solicitudes de ejemplo, comience con uno de los siguiente tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar cuál es el flujo correcto para usted, vea [los tipos de aplicaciones que puede compilar con v2.0](v2-app-types.md).

* [Compilar aplicaciones móviles y nativas con OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Crear aplicaciones web con OpenID Connect](v2-protocols-oidc.md)
* [Compilar aplicaciones de una página con el flujo implícito de OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Compilar los demonios o procesos de servidor con el flujo de credenciales de cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtención de tokens en una API web con el flujo de on-behalf-of de OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
