---
title: 'API web protegida: configuración del código de la aplicación | Azure Active Directory'
description: Obtenga información sobre cómo compilar una API web protegida y configurar el código de la aplicación.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488849"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>API web protegida: adición de autorización a la API

En este artículo se describe cómo se puede agregar autorización a la API web. Esta protección garantiza que solo llamen a la API:

- Aplicaciones en nombre de usuarios que tienen los ámbitos correctos.
- Aplicaciones de demonio que tienen los roles de aplicación correctos.

> [!NOTE]
> Los fragmentos de código de este artículo se extraen de los siguientes ejemplos, que son totalmente funcionales.
>
> - [Tutorial incremental de la API web de ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) en GitHub
> - [Ejemplo de API web de ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Para proteger una API web ASP.NET/ASP.NET Core, debe agregar el atributo `[Authorize]` en uno de los siguientes elementos:

- El propio controlador, si quiere que todas las acciones del controlador estén protegidas.
- La acción de controlador individual para la API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Aun así, esta protección no es suficiente. Solo garantiza que ASP.NET/ASP.NET Core valide el token. La API debe comprobar que el token usado para llamar a la API web se haya solicitado con las notificaciones esperadas, en particular las siguientes:

- Los *ámbitos*, si se llama a la API en nombre de un usuario.
- Los *roles de aplicación*, si se puede llamar a la API desde una aplicación de demonio.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Comprobación de los ámbitos de las API a las que se llama en nombre de los usuarios

Si una aplicación cliente llama a la API en nombre de un usuario, debe solicitar un token de portador con ámbitos específicos para la API. (Consulte [Configuración de código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token)).

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

El método `VerifyUserHasAnyAcceptedScope` hará algo similar a lo siguiente:

- Comprobará que hay una notificación denominada `http://schemas.microsoft.com/identity/claims/scope` o `scp`.
- Comprobará que la notificación tiene un valor que contiene el ámbito que espera la API.

```CSharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Este [código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) es para ASP.NET Core. Para ASP.NET, solo tiene que reemplazar `HttpContext.User` por `ClaimsPrincipal.Current` y reemplazar el tipo de notificación `"http://schemas.microsoft.com/identity/claims/scope"` por `"scp"`. (Vea también el fragmento de código incluido más adelante en este artículo).

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Comprobación de los roles de aplicación de las API a las que llaman aplicaciones de demonio

Si una [aplicación de demonio](scenario-daemon-overview.md) llama a la API web, la aplicación debe solicitar un permiso de aplicación a la API web. Ya hemos visto en [Exposición de los permisos de aplicación (roles de aplicación)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) que la API expone estos permisos (por ejemplo, el rol de aplicación `access_as_application`).
Ahora debe hacer que las API comprueben que el token recibido contiene la notificación `roles` y que esta notificación tiene el valor esperado. El código que realiza esta comprobación es similar al código que verifica los permisos delegados, con la diferencia de que, en lugar de comprobar `scopes`, la acción del controlador comprobará `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

El método `ValidateAppRole()` podría parecerse al siguiente:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

Esta vez, el fragmento de código es para ASP.NET. Para ASP.NET Core, solo tiene que reemplazar `ClaimsPrincipal.Current` por `HttpContext.User` y reemplazar el nombre de la notificación `"roles"` por `"http://schemas.microsoft.com/identity/claims/roles"`. (Vea también el fragmento de código incluido anteriormente en este artículo).

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceptación de tokens solo de aplicación si la API web debe invocarse únicamente mediante aplicaciones de demonio

La notificación `roles` también se usa para los usuarios de patrones de asignación de usuario. (Consulte [Procedimientos para agregar roles de aplicación en la aplicación y recibirlos en el token](howto-add-app-roles-in-azure-ad-apps.md)). Por lo tanto, el simple hecho de comprobar los roles permitirá a las aplicaciones iniciar sesión como usuarios y viceversa, si los roles son asignables a ambos. Le recomendamos que declare roles diferentes para los usuarios y las aplicaciones a fin de evitar esta confusión.

Si quiere que únicamente las aplicaciones de demonio puedan llamar a la API web, agregue una condición al validar el rol de aplicación para indicar que el token es solo de aplicación:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Si aplica la condición inversa, permitirá que llamen a la API solo las aplicaciones que inician la sesión de un usuario.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Paso a producción](scenario-protected-web-api-production.md)
