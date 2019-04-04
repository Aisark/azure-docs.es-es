---
title: Ejemplos de transformación de notificaciones de cuentas de redes sociales para el esquema Marco de experiencia de identidad de Azure Active Directory B2C | Microsoft Docs
description: Ejemplos de transformación de notificaciones de cuentas de redes sociales para el esquema Marco de experiencia de identidad de Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53608654392d7efb73b6dadac14f01a94bb035a7
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893527"
---
# <a name="social-accounts-claims-transformations"></a>Transformaciones de notificaciones de cuentas de redes sociales

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En Azure Active Directory (Azure AD) B2C, las identidades de cuentas de redes sociales se almacenan en un atributo `userIdentities` de un tipo de notificación **alternativeSecurityIdCollection**. Cada elemento de **alternativeSecurityIdCollection** especifica el emisor (nombre del proveedor de identidades, como facebook.com) y `issuerUserId`, que es un identificador de usuario único para el emisor.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

En este artículo, se proporcionan ejemplos de uso de las transformaciones de notificaciones de cuenta de red social del esquema Marco de experiencia de identidad en Azure AD B2C. Para obtener más información, vea [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Crea una representación JSON de la propiedad alternativeSecurityId del usuario que puede usarse en las llamadas Azure Active Directory. Para obtener más información, vea [Esquema de AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| item | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | Elemento ClaimType que especifica el identificador de usuario único usado por el proveedor de identidades de redes sociales. |
| InputClaim | identityProvider | string | Elemento ClaimType que especifica el nombre del proveedor de identidades de cuentas de redes sociales, como facebook.com. |
| OutputClaim | alternativeSecurityId | string | Elemento ClaimType que se produce después de invocar a ClaimsTransformation. Contiene información sobre la identidad de un usuario de cuenta de red social. El **emisor** es el valor de la notificación `identityProvider`. El elemento **issuerUserId** es el valor de la notificación `key` en formato Base 64. |

Use esta transformación de notificaciones para generar un elemento ClaimType `alternativeSecurityId`. Se usa en todos los perfiles técnicos de proveedores de identidades de redes sociales, como `Facebook-OAUTH`. La siguiente transformación de notificaciones recibe el id. de cuenta de red social del usuario y el nombre del proveedor de identidades. El resultado de este perfil técnico es un formato de cadena JSON que puede usarse en los servicios de directorio de Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **key**: 12334
    - **identityProvider**: facebook.com
- Notificaciones de salida:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Agrega un elemento `AlternativeSecurityId` a una notificación `alternativeSecurityIdCollection`.

| item | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Elemento ClaimType que se agregará a la notificación de salida. |
| InputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que usa la transformación de notificaciones (si están disponibles en la directiva). Si se especifica, la transformación de notificaciones agrega el elemento `item` al final de la colección. |
| OutputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que se producen después de invocar a ClaimsTransformation. La nueva colección que contiene los dos elementos de entrada `collection` y `item`. |

En el ejemplo siguiente, se vincula una nueva identidad de red social a una cuenta existente. Para vincular una nueva identidad de red social:
1. En los perfiles técnicos **AAD-UserReadUsingAlternativeSecurityId** y **AAD-UserReadUsingObjectId**, se muestra la salida de la notificación **alternativeSecurityIds** del usuario.
1. Pida al usuario que inicie sesión con uno de los proveedores de identidades que no esté asociado a este usuario.
1. Mediante el **CreateAlternativeSecurityId** de notificaciones de transformación, cree un nuevo **alternativeSecurityId** tipo con el nombre de notificación `AlternativeSecurityId2`
1. Realice una llamada a la transformación de notificaciones **AddItemToAlternativeSecurityIdCollection** para agregar la notificación **AlternativeSecurityId2** a la notificación **AlternativeSecurityIds** existente.
1. Hacer persistir la notificación **alternativeSecurityIds** en la cuenta de usuario

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Notificaciones de salida:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Muestra la lista de emisores de la notificación **alternativeSecurityIdCollection** en una nueva notificación **stringCollection**.

| item | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | El elemento ClaimType que se usará para obtener la lista de proveedores de identidades (emisor). |
| OutputClaim | identityProvidersCollection | stringCollection | Elementos ClaimTypes que se producen después de invocar a ClaimsTransformation. Lista de proveedores de identidades asociados a la notificación de entrada alternativeSecurityIdCollection |

La siguiente transformación de notificaciones lee la notificación **alternativeSecurityIds** del usuario y extrae la lista de nombres de proveedores de identidades asociados a esa cuenta. Use el resultado de **identityProvidersCollection** para mostrar al usuario la lista de proveedores de identidades asociados a la cuenta. O bien, en la página de selección de proveedor de identidades, filtre la lista de proveedores de identidades según la notificación **identityProvidersCollection** de salida. Por lo tanto, el usuario puede vincular una nueva identidad de red social que aún no esté asociada a la cuenta.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Notificaciones de entrada:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Notificaciones de salida:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Quita un elemento **AlternativeSecurityId** de una notificación **alternativeSecurityIdCollection**.

| item | TransformationClaimType | Tipo de datos | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | Elemento ClaimType que contiene el nombre del proveedor de identidades que se quitará de la colección. |
| InputClaim | collection | alternativeSecurityIdCollection | Elemento ClaimTypes que se usará en la transformación de notificaciones. La transformación de notificaciones quita el elemento identityProvider de la colección. |
| OutputClaim | collection | alternativeSecurityIdCollection | Elementos ClaimTypes que se producen después de invocar a ClaimsTransformation. Nueva colección, después de quitar el elemento identityProvider de la colección. |

En el ejemplo siguiente, se desvincula una de las identidades de red social de una cuenta existente. Para desvincular una identidad de red social:
1. En los perfiles técnicos **AAD-UserReadUsingAlternativeSecurityId** y **AAD-UserReadUsingObjectId**, se muestra la salida de la notificación **alternativeSecurityIds** del usuario.
2. Pida al usuario que seleccione la cuenta de red social que quiere quitar de la lista de proveedores de identidades asociados a este usuario.
3. Realiza una llamada a un perfil técnico de transformación de notificaciones que llama a la transformación de notificaciones **RemoveAlternativeSecurityIdByIdentityProvider**, que ha quitado la identidad de red social seleccionada, mediante el nombre del proveedor de identidades.
4. Hace persistir la notificación **alternativeSecurityIds** en la cuenta de usuario.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Ejemplo

- Notificaciones de entrada:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Notificaciones de salida:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
