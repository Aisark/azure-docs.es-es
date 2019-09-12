---
title: Ejemplos de código de la Plataforma de identidad de Microsoft | Microsoft Docs
description: Se proporciona un índice de los ejemplos de código disponibles para la Plataforma de identidad de Microsoft (punto de conexión v2.0), organizados por escenario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c62bf4a887bb6d3b24cdd1234a586c61df17df4
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183120"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Ejemplos de código de la Plataforma de identidad de Microsoft (punto de conexión v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Puede usar la plataforma de identidad de Microsoft para:

- Agregar autenticación y autorización a sus aplicaciones web y API web.
- Requerir un token de acceso para tener acceso a una API web protegida.

Este artículo describe brevemente y proporciona vínculos a ejemplos para el punto de conexión de la Plataforma de identidad de Microsoft. Estos ejemplos le muestran cómo se hace y también proporciona fragmentos de código que puede usar en sus aplicaciones. En la página de ejemplos de código, encontrará temas Léame detallados que le ayudarán con los requisitos, la instalación y la configuración. Los comentarios dentro del código lo ayudarán a comprender las secciones críticas.

> [!NOTE]
> Si está interesado en los ejemplos de v1.0, consulte los [ejemplos de código de Azure AD (punto de conexión v1.0)](sample-v1-code.md).

Para entender el escenario básico de cada tipo de ejemplo, consulte el artículo sobre los [tipos de aplicaciones para el punto de conexión de la Plataforma de identidad de Microsoft](v2-app-types.md).

También puede contribuir a los ejemplos en GitHub. Para saber cómo, vea [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicación de página única

En estos ejemplos se muestra cómo escribir una aplicación de página única protegida con la Plataforma de identidad de Microsoft. Estos ejemplos usan uno de los tipos de MSAL.js.

| Plataforma | DESCRIPCIÓN | Vínculo |
| -------- | --------------------- | -------- |
| ![Esta imagen muestra el logotipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Llama a Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Esta imagen muestra el logotipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Llama a B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagen muestra el logotipo de JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Llama a su propia API web |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Esta imagen muestra el logotipo de Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Llama a Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Esta imagen muestra el logotipo de Angular](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Llama a Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Aplicaciones web

Los ejemplos siguientes ilustran las aplicaciones web que inician la sesión de los usuarios. Algunos ejemplos también muestran la aplicación que llama a Microsoft Graph o a su propia API web con la identidad del usuario.

| Plataforma | Únicamente inicio de sesión del usuario | Inicio de sesión del usuario y llamada a Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagen muestra el logotipo de ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET Core WebApp signs-in users tutorial](https://aka.ms/aspnetcore-webapp-sign-in) (Tutorial de inicio de sesión para los usuarios de WebApp de ASP.NET Core) | Mismo ejemplo en la fase [WebApp de ASP.NET Core calls Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) (WebApp de ASP.NET Core llama a Microsoft Graph) |
| ![Esta imagen muestra el logotipo de ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Inicio rápido de ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagen muestra el logotipo de Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp): una aplicación web MSAL4J que llama a Microsoft Graph |
| ![Esta imagen muestra el logotipo de Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Inicio rápido de Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Esta imagen muestra el logotipo de Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicaciones de escritorio y móviles de cliente público

Los siguientes ejemplos muestran aplicaciones cliente público (aplicaciones de escritorio o móviles) que tienen acceso a Microsoft Graph API o a una API web en nombre de un usuario. Todas estas aplicaciones cliente usan la Biblioteca de autenticación de Microsoft (MSAL).

| Aplicación cliente | Plataforma | Flujo y concesión | Llama a Microsoft Graph | Llamada a una API web de ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Escritorio (WPF)      | ![Esta imagen muestra el logotipo de .NET/C#](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Escritorio (consola)   | ![Esta imagen muestra el logotipo de .NET/C# (escritorio)](media/sample-v2-code/logo_NET.png) | [Autenticación integrada de Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Escritorio (consola)   | ![Esta imagen muestra el logotipo de .NET/C# (escritorio)](media/sample-v2-code/logo_NETcore.png) | [Nombre de usuario/contraseña](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobile (Android, iOS, UWP)   | ![Esta imagen muestra el logotipo de .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Esta imagen muestra iOS/Objective-C o Swift](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Móvil (Android)   | ![Esta imagen muestra el logotipo de Android](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Aplicaciones demonio

En el siguiente ejemplo se muestra una aplicación que accede a Microsoft Graph API con su propia identidad (sin ningún usuario).

| Aplicación cliente | Plataforma | Flujo y concesión | Llama a Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Consola | ![Esta imagen muestra el logotipo de .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciales de cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicación web | ![Esta imagen muestra el logotipo de ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciales de cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Aplicaciones sin periféricos

El siguiente ejemplo muestra aplicaciones cliente públicas que se ejecutan en un dispositivo sin un explorador web. La aplicación puede ser una herramienta de línea de comandos, una aplicación que se ejecuta en Linux o Mac o una aplicación de IoT. El ejemplo incluye aplicaciones que acceden a Microsoft Graph API en nombre de un usuario que inicia sesión de manera interactiva en otro dispositivo (como un teléfono móvil). Esta aplicación cliente usa la Biblioteca de autenticación de Microsoft (MSAL).

| Aplicación cliente | Plataforma | Flujo y concesión | Llama a Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Escritorio (consola)   | ![Esta imagen muestra el logotipo de .NET/C# (escritorio)](media/sample-v2-code/logo_NETcore.png) | [Flujo de código de dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>API web

En los ejemplos siguientes se muestra cómo proteger una API web con el punto de conexión de la Plataforma de identidad de Microsoft y cómo llamar a una API de nivel inferior desde la API web.

| Plataforma | Muestra |
| -------- | ------------------- |
| ![Esta imagen muestra el logotipo de ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | API web (servicio) de ASP.NET Core de [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagen muestra el logotipo de ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API web (servicio) de [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Otros ejemplos de Microsoft Graph

Para ver [ejemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) y tutoriales que muestren los diferentes patrones de uso de Microsoft Graph API, incluida la autenticación con Azure AD, consulte [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Ejemplos y tutoriales de la comunidad de Microsoft Graph).

## <a name="see-also"></a>Otras referencias

- [Introducción a Azure Active Directory para desarrolladores (v1.0)](v1-overview.md)
- [Conceptos y referencia de Graph API de Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Biblioteca auxiliar de Graph API de Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
