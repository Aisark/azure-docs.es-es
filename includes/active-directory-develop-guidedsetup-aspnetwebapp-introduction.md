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
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5a1ff10901d10fb274a6fa1418f04e1f20113cb7
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203639"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adición de inicio de sesión con Microsoft a una aplicación web ASP.NET

Esta guía muestra cómo implementar el inicio de sesión con Microsoft mediante una solución MVC de ASP.NET con una aplicación basada en explorador web tradicional mediante OpenID Connect.

Al final de esta guía, la aplicación podrá aceptar inicios de sesión de cuentas personales (como outlook.com, live.com y otras), así como cuentas profesionales y educativas de cualquier empresa u organización que se haya integrado con Azure Active Directory.

> Esta guía requiere Visual Studio 2015 Update 3 o Visual Studio 2017.  ¿No lo tiene?  [Descargue Visual Studio 2017 de manera gratuita](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funcionamiento de la aplicación de ejemplo generada por esta guía

![Muestra cómo la aplicación de ejemplo generados por este funciona tutoriales](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral-updated.png)

Esta aplicación de ejemplo que se creó en esta guía se basa en un escenario en el que un usuario usa el explorador para obtener acceso a un sitio web de ASP.NET que a su vez solicitará a un usuario que se autentique a través de un botón de inicio de sesión. En esa situación, la mayoría del trabajo para representar la página web se produce en el servidor.

## <a name="libraries"></a>Bibliotecas

Esta guía utiliza las siguientes bibliotecas:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite a una aplicación que use OpenIDConnect para la autenticación|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite a una aplicación que mantenga la sesión del usuario mediante cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que aplicaciones basadas en OWIN se ejecuten en IIS mediante la canalización de solicitudes ASP.NET|
