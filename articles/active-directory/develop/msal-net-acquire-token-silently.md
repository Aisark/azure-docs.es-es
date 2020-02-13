---
title: Adquisición de un token de la memoria caché (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo adquirir un token de acceso de forma silenciosa (a partir de la caché de tokens) mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084837"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtención de un token a partir de la caché de tokens mediante MSAL.NET

Al adquirir un token de acceso mediante la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET), el token se almacena en caché. Cuando la aplicación necesita un token, primero debe llamar al método `AcquireTokenSilent` para comprobar si hay un token aceptable en la caché. En muchos casos, es posible adquirir otro token con más ámbitos basado en un token de la caché. También puede actualizar un token cuando va a expirar (ya que la caché de tokens también contiene un token de actualización).

El patrón recomendado es llamar primero al método `AcquireTokenSilent`.  Si `AcquireTokenSilent` produce un error, deberá adquirir el token mediante otros métodos.

En el ejemplo siguiente, la aplicación primero intenta adquirir un token a partir de la caché de tokens.  Si se produce una excepción `MsalUiRequiredException`, la aplicación adquiere un token de forma interactiva. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
