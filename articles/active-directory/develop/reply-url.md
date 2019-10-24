---
title: Limitaciones y restricciones de URI de redireccionamiento o URL de respuesta en la Plataforma de identidad de Microsoft
description: Limitaciones y restricciones de URL de respuesta o URI de redireccionamiento
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cc6ab0342682bce7befdfe412221ec581312be
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389592"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Limitaciones y restricciones de URI de redireccionamiento o URL de respuesta

Un URI de redireccionamiento, o URL de respuesta, es la ubicación a la que el servidor de autorización enviará al usuario una vez que se haya autorizado correctamente a la aplicación y se haya concedido un código de autorización o token de acceso. El código o token se encuentra en el URI de redireccionamiento o token de respuesta, por lo que es importante que registre la ubicación correcta como parte del proceso de registro de la aplicación.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URI de redireccionamiento

En la tabla siguiente se muestra el número máximo de URI de redireccionamiento que se pueden agregar al registrar la aplicación.

| Cuentas en las que se inicia sesión | Número máximo de URI de redireccionamiento | DESCRIPCIÓN |
|--------------------------|---------------------------------|-------------|
| Cuentas profesionales o educativas de Microsoft en un inquilino de Azure Active Directory (Azure AD) de una organización | 256 | El campo `signInAudience` del manifiesto de aplicación está establecido en *AzureADMyOrg* o *AzureADMultipleOrgs*. |
| Cuentas personales de Microsoft y profesionales o educativas | 100 | El campo `signInAudience` del manifiesto de aplicación está establecido en *AzureADandPersonalMicrosoftAccount*. |

## <a name="maximum-uri-length"></a>Longitud máxima de URI

Puede usar un máximo de 256 caracteres para cada URI de redireccionamiento que agregue a un registro de aplicación.

## <a name="supported-schemes"></a>Esquemas admitidos
En la actualidad, el modelo de aplicación de Azure AD admite esquemas HTTP y HTTPS para las aplicaciones que inician sesión en cuentas profesionales o educativas de Microsoft en el inquilino de Azure Active Directory (Azure AD) de cualquier organización. El campo `signInAudience` del manifiesto de aplicación está establecido en *AzureADMyOrg* o *AzureADMultipleOrgs*. En el caso de las aplicaciones que inician sesión en cuentas personales de Microsoft y en cuentas profesionales y educativas (que establecen `signInAudience`en *AzureADandPersonalMicrosoftAccount* ), solo se permite el esquema HTTPS.

> [!NOTE]
> La nueva experiencia de [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) no permite a los programadores agregar URI con esquema HTTP comodín en la interfaz de usuario. La adición de un URI HTTP para las aplicaciones que inician sesión en cuentas profesionales o educativas solo está permitida a través del editor de manifiestos de la aplicación. A partir de ahora, las nuevas aplicaciones no podrán usar esquemas HTTP en el URI de redireccionamiento. Aun así, las aplicaciones antiguas que contengan esquemas HTTP en los URI de redireccionamiento seguirán funcionando. Los programadores deben usar esquemas HTTPS en los URI de redirección.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restricciones en el uso de un carácter comodín en los URI

Los URI con caracteres comodín, como `https://*.contoso.com`, son prácticos, pero deben evitarse. El uso de caracteres comodín en el URI de redireccionamiento tiene implicaciones de seguridad. Según la especificación de OAuth 2.0 ([sección 3.1.2 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI de punto de conexión de redireccionamiento debe ser un URI absoluto. 

El modelo de aplicación de Azure AD no admite URI con caracteres comodín para las aplicaciones que están configuradas para iniciar sesión en cuentas personales de Microsoft y en cuentas profesionales o educativas. Aun así, se permiten los URI con caracteres comodín para las aplicaciones que actualmente están configuradas para iniciar sesión en cuentas profesionales o educativas en el inquilino de Azure AD de la organización. 
 
> [!NOTE]
> La nueva experiencia de [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) no permite a los desarrolladores agregar URI con caracteres comodín en la interfaz de usuario. La adición de un URI con caracteres comodín para las aplicaciones que inician sesión en cuentas profesionales o educativas solo está permitida a través del editor de manifiestos de la aplicación. A partir de ahora, las nuevas aplicaciones no podrán usar caracteres comodín en el URI de redireccionamiento. Aun así, las aplicaciones antiguas que contienen caracteres comodín en los URI de redireccionamiento seguirán funcionando.

Si su escenario requiere más URI de redireccionamiento de los permitidos en el límite máximo, en lugar de agregar un URI de redireccionamiento con caracteres comodín, considere la posibilidad de seguir uno de los enfoques que se indican a continuación.

### <a name="use-a-state-parameter"></a>Uso de un parámetro de estado

Si tiene varios subdominios y si su escenario requiere redirigir a los usuarios tras su correcta autenticación a la misma página en la que comenzaron, puede ser útil usar un parámetro de estado. 

Según este enfoque:

1. Cree un URI de redireccionamiento "compartido" por cada aplicación para procesar los tokens de seguridad que reciba del punto de conexión de autorización.
1. La aplicación puede enviar parámetros específicos de la aplicación (por ejemplo, la dirección URL del subdominio donde se originó el usuario o la información de marca) en el parámetro de estado. Cuando use un parámetro de estado, protéjase contra las infracciones de CSRF (falsificación de solicitud entre sitios), tal como se especifica en la [sección 10.12 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12). 
1. Los parámetros específicos de la aplicación incluirán toda la información necesaria para que la aplicación represente la experiencia correcta para el usuario, es decir, para que construya el estado de la aplicación adecuado. El punto de conexión de autorización de Azure AD elimina el código HTML del parámetro de estado, por lo que debe asegurarse de no pasar contenido HTML en este parámetro.
1. Cuando Azure AD envía una respuesta al URI de redireccionamiento "compartido", enviará el parámetro de estado de vuelta a la aplicación.
1. Después, la aplicación puede usar el valor del parámetro de estado para determinar a qué dirección URL debe enviar al usuario. Asegúrese de validar la protección contra CSRF.

> [!NOTE]
> Este enfoque permite que un cliente en peligro modifique los parámetros adicionales que se envían en el parámetro de estado, con lo que se redirige al usuario a una dirección URL diferente, que es la [amenaza de redirector abierto](https://tools.ietf.org/html/rfc6819#section-4.2.4) que se describe en RFC 6819. Por lo tanto, el cliente debe proteger estos parámetros, para lo que debe cifrar el estado o comprobarlo por otros medios, por ejemplo, mediante la validación del nombre de dominio en el URI de redireccionamiento con el token.

### <a name="add-redirect-uris-to-service-principals"></a>Adición de URI de redireccionamiento a entidades de servicio

Otro enfoque consiste en agregar URI de redireccionamiento a las [entidades de servicio](app-objects-and-service-principals.md#application-and-service-principal-relationship) que representan el registro de la aplicación en cualquier inquilino de Azure AD. Siga este enfoque si no puede usar un parámetro de estado o si su escenario requiere la adición de nuevos URI de redireccionamiento al registro de la aplicación para cada nuevo inquilino que se admita. 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el [manifiesto de aplicación](reference-app-manifest.md).
