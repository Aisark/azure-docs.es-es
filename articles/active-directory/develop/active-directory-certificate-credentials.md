---
title: Credenciales de certificado en Azure AD
titleSuffix: Microsoft identity platform
description: Este artículo describe el registro y el uso de credenciales de certificados para la autenticación de aplicaciones
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fca872d639ab5c2d4053656cdd3e68a59fdc1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473963"
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciales de certificado para la autenticación de aplicaciones

Azure Active Directory (Azure AD) permite que una aplicación use sus propias credenciales para la autenticación, por ejemplo, en el flujo de concesión de credenciales de cliente de OAuth 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md) y [v2.0](v2-oauth2-client-creds-grant-flow.md)) y el flujo en nombre de ([v1.0](v1-oauth2-on-behalf-of-flow.md) y [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Un formato de credencial que una aplicación puede utilizar para autenticación es una aserción de JSON Web Token (JWT) firmada con un certificado que la aplicación posea.

## <a name="assertion-format"></a>Formato de aserción
Para calcular la aserción, puede usar una de las muchas bibliotecas de [JSON Web Token](https://jwt.ms/) en el idioma que prefiera. La información que lleva el token es la siguiente:

### <a name="header"></a>Encabezado

| Parámetro |  Comentario |
| --- | --- |
| `alg` | Debe ser **RS256** |
| `typ` | Debe ser **JWT** |
| `x5t` | Debe ser la huella digital SHA-1 del certificado X.509 |

### <a name="claims-payload"></a>Notificaciones (carga útil)

| Parámetro |  Comentarios |
| --- | --- |
| `aud` | Audience: Debe ser **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Fecha de expiración: la fecha en que el token expira. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que la validez del token expira.|
| `iss` | Emisor: debe ser el valor de client_id (identificador de la aplicación del servicio de cliente) |
| `jti` | GUID: el identificador de JWT |
| `nbf` | No antes de: fecha antes de la cual el token no se puede usar. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento en que se emitió el token. |
| `sub` | Asunto: En cuanto a `iss` debe ser el valor de client_id (identificador de la aplicación del servicio de cliente) |

### <a name="signature"></a>Firma

La firma se calcula aplicando el certificado como se describe en la [especificación RFC7519 de JSON Web Token](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Ejemplo de una aserción de JWT descodificada

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

## <a name="example-of-an-encoded-jwt-assertion"></a>Ejemplo de una aserción de JWT codificada

La cadena siguiente es un ejemplo de aserción codificada. Si observa detenidamente, verá tres secciones separadas por puntos (.):
* La primera sección codifica el encabezado.
* La segunda sección codifica la carga útil.
* La última sección es la firma calculada con los certificados a partir del contenido de las dos primeras secciones.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registro del certificado con Azure AD

Puede asociar las credenciales del certificado con la aplicación de cliente en Azure AD a través de Azure Portal mediante cualquiera de los métodos siguientes:

### <a name="uploading-the-certificate-file"></a>Cargar el archivo de certificado

En el registro de aplicación de Azure para la aplicación cliente:
1. Seleccione **Certificados y secretos**. 
2. Haga clic en **Cargar certificado** y seleccione el archivo del certificado que va a cargar.
3. Haga clic en **Agregar**.
  Una vez que se carga el certificado, se muestra la huella digital, la fecha de inicio y los valores de expiración. 

### <a name="updating-the-application-manifest"></a>Actualizar el manifiesto de aplicación

Si tiene un certificado, debe calcular:

- `$base64Thumbprint`, que es la codificación base64 del hash del certificado
- `$base64Value`, que es la codificación base64 de los datos sin procesar del certificado

También debe proporcionar un GUID para identificar la clave en el manifiesto de la aplicación (`$keyId`).

En el registro de aplicación de Azure para la aplicación cliente:
1. Seleccione **Manifiesto** para abrir el manifiesto de aplicación.
2. Reemplace la propiedad *keyCredentials* por la información del nuevo certificado con el siguiente esquema.

   ```
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Guarde las modificaciones en el manifiesto de aplicación y cárguelo en Azure AD. 

   La propiedad `keyCredentials` es multivalor, por lo que puede cargar varios certificados para una administración de claves más eficaz.
   
## <a name="code-sample"></a>Código de ejemplo

> [!NOTE]
> Debe calcular el encabezado X5T usando el hash del certificado y convirtiéndolo a una cadena Base64. En C#, el resultado será similar al siguiente: `System.Convert.ToBase64String(cert.GetCertHash());`

En el ejemplo de código de [Authenticating to Azure AD in daemon apps with certificates](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) (Autenticación en Azure AD en aplicaciones de demonio con certificado) se muestra cómo una aplicación utiliza sus propias credenciales para la autenticación. También se muestra cómo se puede [crear un certificado autofirmado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) mediante el comando de PowerShell `New-SelfSignedCertificate`. También puede usar los [scripts de creación de aplicaciones](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) para crear los certificados, calcular la huella digital, etc.
