---
title: Introducción a los perfiles técnicos en las directivas personalizadas
titleSuffix: Azure AD B2C
description: Aprenda cómo se usan los perfiles técnicos en una directiva personalizada de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/02/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 69582291ca1da95003e26a6922899defd7d5e477
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982405"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Acerca de los perfiles técnicos en las directivas personalizadas de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un perfil técnico proporciona un marco con un mecanismo integrado para comunicarse con distintos tipos de entidades utilizando una directiva personalizada en Azure Active Directory B2C (Azure AD B2C). Los perfiles técnicos se usan para comunicarse con su inquilino de Azure AD B2C, crear un usuario o leer un perfil de usuario. Un perfil técnico puede ser autoafirmado para habilitar la interacción con el usuario. Por ejemplo, recopilar las credenciales del usuario para iniciar sesión y, a continuación, representar la página de registro o la página de restablecimiento de contraseña.

## <a name="type-of-technical-profiles"></a>Tipo de perfiles técnicos

Un perfil técnico posibilita estos tipos de escenarios:

- [Azure Active Directory](active-directory-technical-profile.md): proporciona compatibilidad para la administración de usuarios de Azure Active Directory B2C.
- [Emisor de tokens de JWT](jwt-issuer-technical-profile.md): emite un token de JWT que se devuelve a la aplicación de usuario de confianza.
- **Proveedor de factor de teléfono**: autenticación multifactor.
- [OAuth1](oauth1-technical-profile.md) : federación con cualquier proveedor de identidades del protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) : federación con cualquier proveedor de identidades del protocolo OAuth 2.0.
- [OpenID Connect](openid-connect-technical-profile.md): federación con cualquier proveedor de identidades del protocolo OpenID Connect.
- [Transformación de notificaciones](claims-transformation-technical-profile.md): llamada a transformaciones de notificaciones de salida para manipular los valores de notificaciones, validar las notificaciones o establecer valores predeterminados para un conjunto de notificaciones de salida.
- [Proveedor de RESTful](restful-technical-profile.md): llamada a los servicios de la API de REST, como validar entrada de usuario, enriquecer los datos de usuario o integrar con aplicaciones de línea de negocio.
- [SAML2](saml-technical-profile.md) : federación con cualquier proveedor de identidades del protocolo SAML.
- [Autoaserción](self-asserted-technical-profile.md): interacción con el usuario. Por ejemplo, recopilar las credenciales del usuario para iniciar sesión, representar la página de registro o restablecer la contraseña.
- [Administración de sesiones](custom-policy-reference-sso.md): administración de distintos tipos de sesiones.
- **Application Insights**
- [Contraseña de un solo uso](one-time-password-technical-profile.md): proporciona compatibilidad para administrar la generación y verificación de una contraseña de un solo uso. 

## <a name="technical-profile-flow"></a>Flujo del perfil técnico

Todos los tipos de perfiles técnicos comparten el mismo concepto. El usuario envía notificaciones de entrada, ejecuta la transformación de notificaciones y se comunica con la entidad configurada, como un proveedor de identidades, la API REST o los servicios de directorio de Azure AD. Una vez que finalice el proceso, el perfil técnico devuelve las notificaciones de salida y puede ejecutar la transformación de notificaciones de salida. El diagrama siguiente muestra cómo se procesan las transformaciones y las asignaciones a las que se hace referencia en el perfil técnico. Independientemente de la entidad con la que interactúe el perfil técnico, una vez que se ejecute la transformación de las notificaciones, las notificaciones de salida del perfil técnico se almacenan de inmediato en el contenedor de notificaciones.

![Diagrama que ilustra el flujo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation**: las notificaciones de entrada de cada [transformación de notificaciones](claimstransformations.md) de entrada se recogen del contenedor de notificaciones y, después de la ejecución, las notificaciones de salida se colocan de nuevo en el contenedor de notificaciones. Las notificaciones de salida de una transformación de notificaciones de entrada pueden ser notificaciones de entrada de una transformación de notificaciones de entrada subsiguiente.
2. **InputClaims**: las notificaciones se toman de contenedor de notificaciones y se usan para el perfil técnico. Por ejemplo, un [perfil técnico autoafirmado](self-asserted-technical-profile.md) usa las notificaciones de entrada para rellenar previamente las notificaciones de salida que proporciona el usuario. Un perfil técnico de la API REST usa las notificaciones de entrada para enviar los parámetros de entrada al punto de conexión de la API REST. Azure Active Directory usa la notificación de entrada como un identificador único para leer, actualizar o eliminar una cuenta.
3. **Ejecución de perfil técnico**: el perfil técnico intercambia las notificaciones con la entidad configurada. Por ejemplo:
    - Redirija al usuario al proveedor de identidades para completar el inicio de sesión. Después de iniciar sesión correctamente, el usuario regresa y la ejecución del perfil técnico continúa.
    - Llame a una API REST mientras envía parámetros como InputClaims y recupera información como OutputClaims.
    - Cree o actualice la cuenta de usuario.
    - Envía y comprueba el mensaje de texto de MFA.
4. **ValidationTechnicalProfiles**: para un [perfil técnico autoafirmado](self-asserted-technical-profile.md), puede llamar a un [perfil técnico de validación](validation-technical-profile.md) de entrada. El perfil técnico de validación valida los datos perfilados por el usuario y devuelve un mensaje de error o de ejecución correcta, con o sin notificaciones de salida. Por ejemplo, antes de que Azure AD B2C cree una nueva cuenta, comprueba si el usuario ya existe en los servicios de directorio. Puede llamar a un perfil técnico de la API REST para agregar su propia lógica de negocios.<p>El ámbito de las notificaciones de salida de un perfil técnico de validación se limita al perfil técnico que invoca el perfil técnico de validación y otros perfiles técnicos de validación en el mismo perfil técnico. Si desea usar las notificaciones de salida en el siguiente paso de orquestación, deberá agregar las notificaciones de salida al perfil técnico que invoca el perfil técnico de validación.
5. **OutputClaims**: las notificaciones se devuelven al contenedor de notificaciones. Puede usar dichas notificaciones en el siguiente paso de orquestación o en las transformaciones de notificaciones de salida.
6. **OutputClaimsTransformations**: las notificaciones de entrada de cada [transformación de notificaciones](claimstransformations.md) de salida se toman del contenedor de notificaciones. Las notificaciones de salida del perfil técnico de los pasos anteriores pueden ser notificaciones de entrada de una transformación de notificaciones de salida. Después de la ejecución, las notificaciones de salida se vuelven a poner en el contenedor de notificaciones. Las notificaciones de salida de una transformación de notificaciones de salida pueden ser también notificaciones de entrada de una transformación de notificaciones de salida subsiguiente.
7. **Administración de sesión de inicio de sesión único (SSO)**  - [La administración de sesión de SSO](custom-policy-reference-sso.md) controla la interacción con un usuario después de que el usuario se haya autenticado. Por ejemplo, el administrador puede controlar si se muestra la selección de proveedores de identidades, o si se tienen que volver a especificar los detalles de la cuenta local.

Un perfil técnico puede heredar de otro perfil técnico para cambiar la configuración o agregar funcionalidad nueva.  El elemento **IncludeTechnicalProfile** es una referencia al perfil técnico base del que se deriva un perfil técnico.

Por ejemplo, el perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** incluye **AAD-UserReadUsingAlternativeSecurityId**. Este perfil técnico establece el elemento de metadatos **RaiseErrorIfClaimsPrincipalDoesNotExist** en `true`y genera un error si no existe una cuenta de redes sociales en el directorio. **AAD-UserReadUsingAlternativeSecurityId-NoError** invalida este comportamiento y deshabilita el mensaje de error si el usuario no ha existido.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD UserReadUsingAlternativeSecurityId** incluye el perfil técnico de `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Ni **UserReadUsingAlternativeSecurityId-AAD-NoError** ni **AAD UserReadUsingAlternativeSecurityId** especifican el elemento **Protocol**, porque se especifica en el perfil técnico **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Un perfil técnico puede incluir o heredar otro perfil técnico, que puede incluir otro más. No existe un límite en el número de niveles. En función de las necesidades de su empresa, el recorrido del usuario puede llamar a **AAD-UserReadUsingAlternativeSecurityId**, que genera un error si no existe una cuenta de redes sociales del usuario, o **AAD-UserReadUsingAlternativeSecurityId-NoError**, que no genera ningún error.











