---
title: Información sobre Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: La Biblioteca de autenticación de Microsoft (MSAL) permite que los desarrolladores de aplicaciones adquieran tokens para llamar a API web protegidas. Estas API web pueden ser Microsoft Graph, otras API de Microsoft, API web de terceros o su propia API web. MSAL admite varias plataformas y arquitecturas de aplicación.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: dedd9f3458118117546a900aa1a2c8c5aa627100
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473728"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Introducción a la Biblioteca de autenticación de Microsoft (MSAL)
La Biblioteca de autenticación de Microsoft (MSAL) permite que los desarrolladores adquieran [tokens](developer-glossary.md#security-token) desde el punto de conexión de la Plataforma de identidad de Microsoft para acceder a API web protegidas. Estas API web pueden ser Microsoft Graph, otras API de Microsoft, API web de terceros o su propia API web. MSAL está disponible para .NET, JavaScript, Android e iOS, que admiten muchas plataformas y arquitecturas de aplicación distintas.

MSAL ofrece varias formas de obtener tokens, con una API coherente para una variedad de plataformas. Usar MSAL brinda las ventajas siguientes:

* No es necesario usar directamente las bibliotecas de OAuth ni el código en el protocolo en la aplicación.
* Adquiere tokens en nombre de un usuario o en nombre de una aplicación (cuando se aplica a la plataforma).
* Mantiene una caché de tokens y actualiza los tokens en nombre del usuario cuando están por expirar. No es necesario que el usuario controle la expiración de los tokens.
* Lo ayuda a especificar para qué publico quiere que la aplicación inicie sesión (su organización, varias organizaciones, cuentas personales, profesionales y educativas de Microsoft, identidades sociales con Azure AD B2C, usuarios en nubes soberanas y nacionales).
* Lo ayuda a configurar la aplicación a partir de archivos de configuración.
* Lo ayuda a solucionar problemas de la aplicación mediante la exposición de excepciones, registros y telemetría que requieren acción.

## <a name="application-types-and-scenarios"></a>Escenarios y tipos de aplicación
Con MSAL, es posible adquirir un token desde varios tipos de aplicación: aplicaciones web, API web, aplicaciones de una sola página (JavaScript), aplicaciones móviles y nativas y aplicaciones de lado servidor y demonios.

MSAL se puede usar en muchos escenarios de aplicación, incluidos:

* [Single page applications (JavaScript)](scenario-spa-overview.md) (Aplicaciones de una sola página [JavaScript])
* [Web app signing in users](scenario-web-app-sign-user-overview.md) (Aplicación web que inicia sesión de los usuarios)
* [Web application signing in a user and calling a web API on behalf of the user](scenario-web-app-call-api-overview.md) (Aplicación web que inicia sesión de un usuario y llama a una API web en nombre del usuario)
* [Protecting a web API so only authenticated users can access it](scenario-protected-web-api-overview.md) (Protección de una API web para que solo los usuarios autenticados puedan acceder a ella)
* [Web API calling another downstream web API on behalf of the signed-in user](scenario-web-api-call-api-overview.md) (API web que llama a otra API web de nivel inferior en nombre del usuario que inició sesión)
* [Desktop application calling a web API on behalf of the signed-in user](scenario-desktop-overview.md) (Aplicación de escritorio que llama a una API web en nombre del usuario que inició sesión)
* [Mobile application calling a Web API on behalf of the user who's signed-in interactively](scenario-mobile-overview.md) (Aplicación móvil que llama a una API web en nombre del usuario que inicio sesión de manera interactiva)
* [Desktop/service daemon application calling web API on behalf of itself](scenario-daemon-overview.md) (Aplicación de demonio de escritorio/servicio que llama a una API web en su propio nombre)

## <a name="languages-and-frameworks"></a>Lenguajes y plataformas

| Biblioteca | Plataformas y marcos compatibles|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Plataforma universal de Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Marcos de JavaScript/TypeScript como AngularJS, Ember.js o Durandal.js|
| [MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL para iOS y macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS y macOS|
| [MSAL Java (versión preliminar)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (versión preliminar)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Diferencias entre ADAL y MSAL

La Biblioteca de autenticación de Active Directory (ADAL) se integra con el punto de conexión de Azure AD para desarrolladores (v1.0), donde MSAL se integra con el punto de conexión de la Plataforma de identidad de Microsoft (v2.0). El punto de conexión v1.0 admite cuentas profesionales, pero no cuentas personales. El punto de conexión v2.0 es la unión de las cuentas personales y de las cuentas profesionales de Microsoft en un único sistema de autenticación. Con MSAL además puede obtener autenticaciones para Azure AD B2C.

Para información más específica, lea sobre la [migración a MSAL.NET desde ADAL.NET](msal-net-migration.md) y la [migración a MSAL.js desde ADAL.js](msal-compare-msal-js-and-adal-js.md).
