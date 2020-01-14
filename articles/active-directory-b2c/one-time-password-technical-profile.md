---
title: Habilitación de la verificación de la contraseña de un solo uso (OTP)
titleSuffix: Azure AD B2C
description: Obtenga información sobre cómo configurar un escenario de contraseña de un solo uso (OTP) mediante directivas personalizadas de Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c50edb03fe849c70596c0bfb3cdc2dafa15f136f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475054"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definición de un perfil técnico de una contraseña de un solo uso en una directiva personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) proporciona compatibilidad para administrar la generación y verificación de una contraseña de un solo uso. Use un perfil técnico para generar un código y, a continuación, verifique el código más adelante.

El perfil técnico de la contraseña de un solo uso también puede devolver un mensaje de error durante la verificación del código. Puede diseñar la integración con la contraseña de un solo uso mediante el uso de un **perfil técnico de validación**. Un perfil técnico de validación llama al perfil técnico de la contraseña de un solo uso para verificar un código. El perfil técnico de validación valida los datos que proporciona el usuario antes de que continúe el recorrido del usuario. Con el perfil técnico de validación, se muestra un mensaje de error en una página autoafirmada.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `Proprietary`. El atributo **handler** debe contener el nombre completo del ensamblado del controlador de protocolo que usa Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

En el ejemplo siguiente se muestra un perfil técnico de contraseña de un solo uso:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generación de código

El primer modo de este perfil técnico es para generar un código. A continuación, se muestran las opciones que se pueden configurar para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de las notificaciones que se deben enviar al proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| identificador | Sí | Identificador que se usa identificar al usuario que debe verificar el código más adelante. Normalmente se usa como el identificador del destino en el que se entrega el código, por ejemplo, la dirección de correo electrónico o el número de teléfono. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al proveedor de protocolo de contraseña de un solo uso.

### <a name="output-claims"></a>Notificaciones de salida

El elemento **InputClaims** contiene una lista de las notificaciones que genera el proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| otpGenerated | Sí | Código generado cuya sesión se administra mediante Azure AD B2C. |

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

La configuración siguiente se puede usar para establecer la generación de código y el mantenimiento:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | No | Tiempo en segundos hasta la expiración del código. Mínimo: `60`; máximo: `1200`; valor predeterminado: `600`. |
| CodeLength | No | Longitud del código. El valor predeterminado es `6`. |
| CharacterSet | No | Juego de caracteres del código, con formato para usarse en una expresión regular. Por ejemplo, `a-z0-9A-Z`. El valor predeterminado es `0-9`. El juego de caracteres debe incluir un mínimo de 10 caracteres diferentes en el conjunto especificado. |
| NumRetryAttempts | No | Número de intentos de verificación antes de que el código se considere no válido. El valor predeterminado es `5`. |
| ReuseSameCode | No | Indica si se debe proporcionar un código duplicado en lugar de generar un código nuevo cuando el código proporcionado no ha expirado y sigue siendo válido. El valor predeterminado es `false`. |

### <a name="returning-error-message"></a>Devolución de mensajes de error

No se devuelve ningún mensaje de error para el modo de generación de código.

### <a name="example"></a>Ejemplo

En el siguiente ejemplo, `TechnicalProfile` se usa para generar un código:

```XML
<TechnicalProfile Id="GenerateCode">
    <DisplayName>Generate Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">600</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="NumRetryAttempts">5</Item>
        <Item Key="ReuseSameCode">false</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
    </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Compruebe el código.

El segundo modo de este perfil técnico es verificar un código. A continuación, se muestran las opciones que se pueden configurar para este modo.

### <a name="input-claims"></a>Notificaciones de entrada

El elemento **InputClaims** contiene una lista de las notificaciones que se deben enviar al proveedor de protocolo de contraseña de un solo uso. También puede asignar el nombre de la notificación al nombre que se define a continuación.

| ClaimReferenceId | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| identificador | Sí | Identificador que se usa para identificar al usuario que ha generado previamente un código. Normalmente se usa como el identificador del destino en el que se entrega el código, por ejemplo, la dirección de correo electrónico o el número de teléfono. |
| otpToVerify | Sí | Código de verificación que proporciona el usuario. |

El elemento **InputClaimsTransformations** puede contener una colección de elementos **InputClaimsTransformation** que se usan para modificar las notificaciones de entrada o generar otras nuevas antes del envío al proveedor de protocolo de contraseña de un solo uso.

### <a name="output-claims"></a>Notificaciones de salida

No se proporcionan notificaciones de salida durante la verificación del código de este proveedor de protocolo.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

### <a name="metadata"></a>Metadatos

La configuración siguiente se puede usar para establecer el mensaje de error que se muestra cuando se produce un error en la verificación del código:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | Mensaje que se mostrará al usuario si la sesión de verificación de código ha expirado. Es posible que el código haya expirado o que nunca se haya generado para un identificador determinado. |
| UserMessageIfMaxRetryAttempted | No | Mensaje que se mostrará al usuario si ha superado el número máximo de intentos de verificación permitidos. |
| UserMessageIfInvalidCode | No | Mensaje que se mostrará al usuario si ha proporcionado un código no válido. |

### <a name="returning-error-message"></a>Devolución de mensajes de error

Tal y como se describe en la sección [Metadatos](#metadata), puede personalizar el mensaje de error que se muestra al usuario para los distintos casos de errores. También puede localizar esos mensajes si agrega la configuración regional como prefijo, por ejemplo:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Ejemplo

En el siguiente ejemplo, `TechnicalProfile` se usa para verificar un código:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```