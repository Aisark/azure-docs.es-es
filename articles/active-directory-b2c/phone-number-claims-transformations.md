---
title: Transformaciones de notificaciones de número de teléfono en directivas personalizadas
titleSuffix: Azure AD B2C
description: Referencia de directivas personalizadas para las transformaciones de notificaciones de número de teléfono en Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c43e3386886456eed0c58fefd0fb1212795db66c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475042"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definición de transformaciones de notificaciones de número de teléfono en Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se proporcionan referencias y ejemplos de uso de las transformaciones de notificaciones de números de teléfono del esquema Identity Experience Framework en Azure Active Directory B2C (Azure AD B2C). Para obtener más información sobre las transformaciones de notificaciones en general, vea [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Esta notificación valida el formato del número de teléfono. Si se encuentra en un formato válido, cámbielo a un formato estándar utilizado por Azure AD B2C. Si el número de teléfono proporcionado no tiene un formato válido, se devuelve un mensaje de error.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Notificación del tipo de cadena desde el que se realiza la conversión. |
| OutputClaim | outputClaim | string | Resultado de esta transformación de notificaciones. |

La transformación de notificaciones **ConvertStringToPhoneNumberClaim** siempre se ejecuta desde un [perfil técnico de validación](validation-technical-profile.md) llamado por un [perfil técnico autoafirmado](self-asserted-technical-profile.md) o un [control de visualización](display-controls.md). Los metadatos de un perfil técnico autoafirmado **UserMessageIfClaimsTransformationInvalidPhoneNumber** controlan el mensaje de error que se presenta al usuario.

![Diagrama de la ruta de acceso de ejecución del mensaje de error](./media/phone-authentication/assert-execution.png)

Puede usar esta transformación de notificaciones para asegurarse de que la notificación de la cadena proporcionada es un número de teléfono válido. Si no es así, se emite un mensaje de error. En el ejemplo siguiente se comprueba que el valor de ClaimType **phoneString** es realmente un número de teléfono válido y, a continuación, se devuelve el número de teléfono con el formato de Azure AD B2C estándar. De lo contrario, se emite un mensaje de error.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

El perfil técnico autoafirmado que llama al perfil técnico de validación que contiene esta transformación de notificaciones puede definir el mensaje de error.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
  - **inputClaim**: +1 (123) 456-7890
- Notificaciones de salida:
  - **outputClaim**: +11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Extrae el código de país y el número nacional de la notificación de entrada y, opcionalmente, produce una excepción si el número de teléfono proporcionado no es válido.

| Elemento | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | Notificación de cadena del número de teléfono. El número de teléfono tiene que estar en formato internacional y estar completo con un signo "+" y un código de país al principio. |
| InputParameter | throwExceptionOnFailure | boolean | [Opcional] Parámetro que indica si se produce una excepción cuando el número de teléfono no es válido. El valor predeterminado es False. |
| InputParameter | countryCodeType | string | [Opcional] Parámetro que indica el tipo de código de país de la notificación de salida. Los valores disponibles son **CallingCode** (el código de llamada internacional de un país, por ejemplo, +1) o **ISO3166** (el código de país ISO-3166 de dos letras). |
| OutputClaim | nationalNumber | string | Notificación de cadena para el número nacional del número de teléfono. |
| OutputClaim | countryCode | string | Notificación de cadena para el código de país del número de teléfono. |


Si la transformación de notificaciones **GetNationalNumberAndCountryCodeFromPhoneNumberString** se ejecuta desde un [perfil técnico de validación](validation-technical-profile.md) al que llama un [perfil técnico autoafirmado](self-asserted-technical-profile.md) o una [acción de control de visualización](display-controls.md#display-control-actions), los metadatos del perfil técnico autoafirmado **UserMessageIfPhoneNumberParseFailure** controlan el mensaje de error que se presenta al usuario.

![Diagrama de la ruta de acceso de ejecución del mensaje de error](./media/phone-authentication/assert-execution.png)

Puede usar esta transformación de notificaciones para dividir un número de teléfono completo en el código de país y el número nacional. Si el número de teléfono proporcionado no es válido, puede optar por generar un mensaje de error.

En el ejemplo siguiente se intenta dividir el número de teléfono en el número nacional y el código de país. Si el número de teléfono es válido, el número nacional invalidará el número de teléfono. Si el número de teléfono no es válido, no se iniciará ninguna excepción y el número de teléfono seguirá teniendo su valor original.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

El perfil técnico autoafirmado que llama al perfil técnico de validación que contiene esta transformación de notificaciones puede definir el mensaje de error.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Ejemplo 1

- Notificaciones de entrada:
  - **phoneNumber**: +49 (123) 456-7890
- Parámetros de entrada:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Notificaciones de salida:
  - **nationalNumber**: 1234567890
  - **countryCode** DE

### <a name="example-2"></a>Ejemplo 2

- Notificaciones de entrada:
  - **phoneNumber**: +49 (123) 456-7890
- Parámetros de entrada
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Notificaciones de salida:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
