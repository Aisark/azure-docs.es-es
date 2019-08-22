---
title: Configuración de aplicaciones cliente (Biblioteca de autenticación de Microsoft) | Azure
description: Obtenga información sobre las opciones de configuración de aplicaciones cliente públicas y confidenciales en la Biblioteca de autenticación de Microsoft (MSAL).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedca8dfb60d976723508bb89cab7d5b6dda1b9
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532919"
---
# <a name="application-configuration-options"></a>Opciones de configuración de aplicaciones

En el código, se inicializa una nueva aplicación cliente pública o confidencial (o el agente de usuario para MSAL.js) para autenticar y obtener tokens. Puede establecer una serie de opciones de configuración al inicializar la aplicación cliente en la Biblioteca de autenticación de Microsoft (MSAL). Estas opciones se dividen en dos grupos:

- Opciones de registro, incluidas:
    - [Autoridad](#authority) (compuesta por la [instancia](#cloud-instance) del proveedor de identidades y la [audiencia](#application-audience) de inicio de sesión para la aplicación, y posiblemente el Id. de inquilino).
    - [Id. de cliente](#client-id).
    - [URI de redirección](#redirect-uri).
    - [Secreto de cliente](#client-secret) (aplicaciones cliente confidenciales).
- [Opciones de registro](#logging), incluido el nivel de registro, control de los datos personales y el nombre del componente que usa la biblioteca.

## <a name="authority"></a>Autoridad
La autoridad es una dirección URL que indica un directorio desde el que MSAL puede solicitar tokens. Las autoridades comunes son:

- https\://login.microsoftonline.com/\<inquilino\>/, donde &lt;inquilino&gt; es el identificador del inquilino de Azure Active Directory (Azure AD) o un dominio asociado a este inquilino de Azure AD. Se usa solo para iniciar sesión en los usuarios de una organización específica.
- https\://login.microsoftonline.com/common/. Se usa para el inicio de sesión de los usuarios con cuentas profesionales y educativas o cuentas personales de Microsoft.
- https\://login.microsoftonline.com/organizations/. Se usa para el inicio de sesión de los usuarios con cuentas profesionales y educativas.
- https\://login.microsoftonline.com/consumers/. Se usa para el inicio de sesión de los usuarios que solo tienen cuentas personales de Microsoft (anteriormente conocidas como cuentas de Windows Live ID).

La configuración de la entidad debe ser coherente con lo que se declare en el portal de registro de aplicaciones.

La dirección URL de la autoridad se compone de la instancia y la audiencia.

La autoridad puede ser:
- Una autoridad de Azure AD Cloud.
- Una autoridad de Azure AD B2C. Consulte [B2C specifics](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics) (Especificaciones de B2C).
- Una autoridad de Servicios de federación de Active Directory (AD FS). Consulte [AD FS Support](https://aka.ms/msal-net-adfs-support) (Compatibilidad con AD FS).

Las autoridades de Azure AD Cloud tienen dos partes:
- La *instancia* del proveedor de identidades.
- La *audiencia* de inicio de sesión para la aplicación.

La instancia y la audiencia se pueden concatenar y proporcionar como la dirección URL de la autoridad. En versiones anteriores a MSAL 3 de MSAL 3.*x*, el usuario debía componer la autoridad por sí mismo, en función de la nube de destino y la audiencia de inicio de sesión.  En este diagrama se muestra cómo se compone la dirección URL de la autoridad:

![Cómo se compone la dirección URL de la autoridad](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Nombre de la instancia en la nube
La *instancia* se usa para especificar si la aplicación inicia la sesión de los usuarios desde la nube pública de Azure o desde nubes nacionales. Mediante el uso de MSAL en el código, puede establecer la instancia en la nube de Azure usando una enumeración o pasando la dirección URL a la [instancia en la nube nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como el miembro `Instance` (si la conoce).

MSAL.NET devolverá una excepción explícita si se especifican `Instance` y `AzureCloudInstance`.

Si no se especifica una instancia, la aplicación se dirigirá a la instancia en la nube pública de Azure (la instancia de la dirección URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Audiencia de la aplicación

La audiencia de inicio de sesión depende de las necesidades empresariales de la aplicación:
- Si es desarrollador de líneas de negocio (LOB), probablemente producirá una aplicación de inquilino único que se usará solo en su organización. En ese caso, debe especificar la organización, ya sea por el Id. de inquilino (el Id. de la instancia de Azure AD) o por un nombre de dominio asociado a la instancia de Azure AD.
- Si es ISV, puede que quiera que los usuarios inicien sesión con sus cuentas profesionales o educativas en cualquier organización o en determinadas organizaciones (aplicación multiinquilino). Pero también es posible que quiera que los usuarios inicien sesión con sus cuentas personales de Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Cómo especificar la audiencia en el código o la configuración
Mediante el uso de MSAL en el código, se especifica la audiencia con uno de los siguientes valores:
- La enumeración de la audiencia de la autoridad de Azure AD.
- El Id. de inquilino, que puede ser:
  - Un GUID (el Id. de la instancia de Azure AD), para las aplicaciones de inquilino único.
  - Un nombre de dominio asociado a la instancia de Azure AD (también para las aplicaciones de inquilino único).
- Uno de estos marcadores de posición como un Id. de inquilino en lugar de la enumeración de la audiencia de la autoridad de Azure AD:
    - `organizations` para una aplicación multiinquilino,
    - `consumers` para iniciar la sesión de los usuarios solo con sus cuentas personales,
    - `common` para el inicio de sesión de los usuarios con sus cuentas profesionales y educativas o sus cuentas personales de Microsoft.

MSAL devolverá una excepción significativa si especifican la audiencia y el Id. de inquilino de la autoridad de Azure AD.

Si no se especifica una audiencia, la aplicación se dirigirá a cuentas de Azure AD y personales de Microsoft como audiencia (es decir, se comportará como si se especificara `common`).

### <a name="effective-audience"></a>Audiencia eficaz
La audiencia eficaz para su aplicación será la mínima (si hay una intersección) entre la audiencia que estableció en la aplicación y la audiencia especificada en el registro de aplicaciones. De hecho, la experiencia de [Registros de aplicaciones](https://aka.ms/appregistrations) permite especificar la audiencia (los tipos de cuenta compatibles) para la aplicación. Para más información, consulte [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](quickstart-register-app.md).

Actualmente, la única manera de que una aplicación inicie la sesión de los usuarios con solo cuentas personales de Microsoft es configurar estas dos opciones:
- Establezca la audiencia del registro de aplicaciones en `Work and school accounts and personal accounts`.
- Establezca la audiencia del código o de la configuración en `AadAuthorityAudience.PersonalMicrosoftAccount` (o `TenantID` ="consumers").

## <a name="client-id"></a>Id. de cliente
El Id. de cliente es el Id. de (cliente) aplicación único que Azure AD asignó a la aplicación cuando esta se registró.

## <a name="redirect-uri"></a>URI de redireccionamiento
El URI de redirección es el URI al que el proveedor de identidades enviará los tokens de seguridad.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirección para aplicaciones cliente públicas
Si es desarrollador de aplicaciones cliente públicas que usa MSAL:
- Puede usar `.WithDefaultRedirectUri()` en aplicaciones de escritorio o UWP (MSAL.net 4.1+). Este método establecerá la propiedad de URI de redireccionamiento de la aplicación cliente pública en el URI de redireccionamiento predeterminado recomendado para las aplicaciones cliente públicas. 

  Plataforma  | URI de redireccionamiento  
  ---------  | --------------
  Aplicación de escritorio (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | Valor de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Esto permite realizar el inicio de sesión único con el explorador, al establecer el valor en el resultado de WebAuthenticationBroker.GetCurrentApplicationCallbackUri(), que debe registrar.
  .NET Core | `https://localhost`. Esto permite que el usuario use el explorador del sistema para la autenticación interactiva, ya que .NET Core no tiene una interfaz de usuario para la vista web insertada en este momento.

- No es necesario agregar un identificador URI de redireccionamiento si se compila una aplicación Xamarin Android e iOS que no admite agente (el URI de redireccionamiento se establece automáticamente en `msal{ClientId}://auth` para Xamarin Android e iOS).

- Debe configurar el URI de redireccionamiento en [Registros de aplicaciones](https://aka.ms/appregistrations):

   ![URI de redirección en Registros de aplicaciones](media/msal-client-application-configuration/redirect-uri.png)

Puede invalidar el URI de redirección mediante la propiedad `RedirectUri` (por ejemplo, si usa agentes). Estos son algunos ejemplos de URI de redirección para ese escenario:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Para más información, consulte la [documentación para Android e iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirección para aplicaciones cliente confidenciales
Para las aplicaciones web, el URI de redirección (o URI de respuesta) es el URI que Azure AD usará para enviar el token a la aplicación. Puede ser la dirección URL de la aplicación web o la API web si la aplicación confidencial es una de estas. El URI de redirección debe estar registrado en el registro de aplicaciones. Este registro es especialmente importante al implementar una aplicación que se ha probado inicialmente a nivel local. A continuación, debe agregar la dirección URL de respuesta de la aplicación implementada en el portal de registro de aplicaciones.

Para las aplicaciones demonio, no es necesario especificar un URI de redirección.

## <a name="client-secret"></a>Secreto del cliente
Esta opción especifica el secreto de cliente para la aplicación cliente confidencial. Este secreto (contraseña de la aplicación) lo proporcionan el portal de registro de aplicaciones o Azure AD durante el registro de la aplicación con PowerShell AzureAD, AzureRM o CLI de Azure.

## <a name="logging"></a>Registro
Las demás opciones de configuración permiten realizar registros y solucionar problemas. Consulte el artículo [Registro](msal-logging.md) para obtener información sobre cómo usarlas.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo [crear instancias de aplicaciones cliente mediante MSAL.NET](msal-net-initializing-client-applications.md).

Obtenga información sobre cómo [crear instancias de aplicaciones cliente mediante MSAL.js](msal-js-initializing-client-applications.md).
