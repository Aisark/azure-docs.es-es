---
title: Borrado de la caché del token usando Microsoft Authentication Library para .NET
titleSuffix: Microsoft identity platform
description: Aprenda a borrar la caché del token usando Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802916"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Borrado de la caché de tokens mediante MSAL.NET

Al [adquirir un token de acceso](msal-acquire-cache-tokens.md) mediante Microsoft Authentication Library para .NET (MSAL.NET), el token se almacena en caché. Cuando la aplicación necesita un token, primero debe llamar al método `AcquireTokenSilent` para comprobar si hay un token aceptable en la caché. 

Para borrar la caché, es necesario quitar las cuentas de la caché. No obstante, esto no elimina la cookie de sesión que se encuentra en el explorador.  En el ejemplo siguiente se crea una instancia de una aplicación cliente pública, se obtienen las cuentas de la aplicación y se quitan las cuentas.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Para obtener más información sobre la adquisición y el almacenamiento en caché de los tokens, vea [Adquirir un token de acceso](msal-acquire-cache-tokens.md).
