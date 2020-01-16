---
title: Verificaciones de correo electrónico personalizado
titleSuffix: Azure AD B2C
description: Aprenda a personalizar el correo electrónico de verificación que se envía a los clientes cuando se registran para usar las aplicaciones habilitadas para Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3d9316f42c8d0ac5b44cda2e484ca4c92110813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474730"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verificación de correo electrónico personalizado en Azure Active Directory B2C

Use el correo electrónico personalizado en Azure Active Directory B2C (Azure AD B2C) para enviar mensajes personalizados a los usuarios que se registran para usar las aplicaciones. Mediante el uso de elementos [DisplayControl](display-controls.md) (actualmente en versión preliminar) y un proveedor de correo electrónico de terceros, puede usar su propia plantilla de correo electrónico, dirección *De* y asunto, además de admitir la localización y la configuración personalizada de la contraseña de un solo uso (OTP).

La verificación del correo electrónico personalizado requiere el uso de un proveedor de correo electrónico de terceros, como [SendGrid](https://sendgrid.com) o [SparkPost](https://sparkpost.com), una API REST personalizada o cualquier proveedor de correo electrónico basado en HTTP (incluido el suyo propio). En este artículo se describe cómo configurar una solución que usa SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Creación de una cuenta de SendGrid

Si aún no tiene una, empiece por configurar una cuenta de SendGrid (los clientes de Azure pueden desbloquear 25 000 mensajes de correo electrónico gratuitos al mes). Para obtener instrucciones de configuración, consulte la sección [Creación de una cuenta de SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) en [Envío de correos electrónicos con SendGrid y Azure](../sendgrid-dotnet-how-to-send-email.md).

Asegúrese de completar la sección en la que [crea una clave de API de SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Anote la clave de API para usarla en un paso posterior.

## <a name="create-azure-ad-b2c-policy-key"></a>Creación de la clave de directiva de Azure AD B2C

A continuación, almacene la clave de API de SendGrid en una clave de directiva de Azure AD B2C que servirá de referencia para las directivas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el filtro **Directorio + suscripción** en el menú superior y elija el directorio de Azure AD B2C.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En la página de introducción, seleccione **Identity Experience Framework**.
1. Seleccione **Claves de directiva** y luego **Agregar**.
1. En **Opciones**, elija `Manual`.
1. Escriba un **nombre** para la clave de directiva. Por ejemplo, `SendGridSecret`. Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
1. En **Secreto**, escriba el secreto de cliente que haya registrado previamente.
1. En **Uso de claves**, seleccione `Signature`.
1. Seleccione **Crear**.

## <a name="create-sendgrid-template"></a>Creación de la plantilla de SendGrid

Con la cuenta de SendGrid creada y la clave de API de SendGrid almacenada en una clave de directiva de Azure AD B2C, cree una [plantilla transaccional dinámica](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/) de SendGrid.

1. En el sitio de SendGrid, abra la página [Transactional Templates](https://sendgrid.com/dynamic_templates) (Plantillas transaccionales) y seleccione **Create Template** (Crear plantilla).
1. Escriba un nombre de plantilla único, como por ejemplo `Verification email`, y, a continuación, seleccione **Save** (Guardar).
1. Para empezar a editar la nueva plantilla, seleccione **Add Version** (Agregar versión).
1. Seleccione **Code Editor** (Editor de código) y, a continuación, **Continue** (Continuar).
1. En el editor de HTML, pegue la siguiente plantilla HTML o use la suya propia. Los parámetros `{{otp}}` y `{{email}}` se reemplazarán dinámicamente con el valor de la contraseña de un solo uso y la dirección de correo electrónico del usuario.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expanda **Settings** (Configuración) a la izquierda y, en **Email Subject** (Asunto del correo electrónico), escriba `{{subject}}`.
1. Seleccione **Save Template** (Guardar plantilla).
1. Seleccione la flecha atrás para volver a la página **Transactional Templates** (Plantillas transaccionales).
1. Anote el valor de **ID** de la plantilla que creó para usarlo en un paso posterior. Por ejemplo, `d-989077fbba9746e89f3f6411f596fb96`. Este identificador se especifica al [agregar la transformación de notificaciones](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Incorporación de tipos de notificaciones de Azure AD B2C

En la directiva, agregue los siguientes tipos de notificaciones al elemento `<ClaimsSchema>` dentro de `<BuildingBlocks>`.

Estos tipos de notificaciones son necesarios para generar y verificar la dirección de correo electrónico mediante un código de contraseña de un solo uso (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Incorporación de la transformación de notificaciones

A continuación, necesita una transformación de notificaciones para generar una notificación de cadena JSON que será el cuerpo de la solicitud enviada a SendGrid.

La estructura del objeto JSON se define mediante los identificadores de la notación de puntos de InputParameters y los valores de TransformationClaimTypes de InputClaims. Los números de la notación de puntos implican matrices. Los valores provienen de los valores de InputClaims y de las propiedades "value" de InputParameters. Para más información sobre las transformaciones de notificaciones de JSON, consulte [Transformaciones de notificaciones de JSON](json-transformations.md).

Agregue la siguiente transformación de notificaciones al elemento `<ClaimsTransformations>` dentro de `<BuildingBlocks>`. Realice las siguientes actualizaciones en el XML de la transformación de notificaciones:

* Actualice el valor `template_id` de InputParameter con el identificador de la plantilla transaccional de SendGrid que creó anteriormente en [Creación de la plantilla de SendGrid](#create-sendgrid-template).
* Actualice el valor de dirección de `from.email`. Use una dirección de correo electrónico válida para evitar que el correo electrónico de verificación se marque como correo no deseado.
* Actualice el valor del parámetro de entrada de la línea de asunto `personalizations.0.dynamic_template_data.subject` con una línea de asunto adecuada para su organización.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Incorporación de definición de contenido de DataUri

Bajo las transformaciones de notificaciones dentro de `<BuildingBlocks>`, agregue el siguiente elemento [ContentDefinition](contentdefinitions.md) para hacer referencia al URI de datos de la versión 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Creación de un elemento DisplayControl

Para verificar la dirección de correo electrónico con un código de verificación que se envía al usuario, se utiliza un control de visualización de verificación.

Este control de visualización de ejemplo está configurado para:

1. Recopilar el tipo de notificación de dirección `email` del usuario.
1. Esperar a que el usuario proporcione el tipo de notificación `verificationCode` con el código que se le envía.
1. Devolver el valor de `email` al perfil técnico autoafirmado que tiene una referencia a este control de visualización.
1. Con la acción `SendCode`, generar un código OTP y enviar un correo electrónico con este código al usuario.

![Acción de correo electrónico de envío de código de verificación](media/custom-email/display-control-verification-email-action-01.png)

En las definiciones de contenido, todavía dentro de `<BuildingBlocks>`, agregue el siguiente elemento [DisplayControl](display-controls.md) de tipo [VerificationControl](display-control-verification.md) a la directiva.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Incorporación de perfiles técnicos de OTP

El perfil técnico `GenerateOtp` genera un código para la dirección de correo electrónico. El perfil técnico `VerifyOtp` verifica el código asociado a la dirección de correo electrónico. Puede cambiar la configuración del formato y la expiración de la contraseña de un solo uso. Para más información sobre los perfiles técnicos de OTP, consulte [Definición de un perfil técnico de una contraseña de un solo uso en una directiva personalizada de Azure AD B2C](one-time-password-technical-profile.md).

Agregue los siguientes perfiles técnicos al elemento `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Incorporación de un perfil técnico de API REST

Este perfil técnico de API REST genera el contenido del correo electrónico (con el formato de SendGrid). Para más información sobre los perfiles técnicos de RESTful, consulte [Definición de un perfil técnico de RESTful en una directiva personalizada en Azure Active Directory B2C](restful-technical-profile.md).

Al igual que hizo con los perfiles técnicos de OTP, agregue los siguientes perfiles técnicos al elemento `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Referencia al elemento DisplayControl

En el paso final, agregue una referencia al elemento DisplayControl que creó. Si usó una versión anterior de la directiva de Azure AD B2C, reemplace el perfil técnico autoafirmado existente `LocalAccountSignUpWithLogonEmail` por el siguiente. Este perfil técnico usa `DisplayClaims` con una referencia al elemento DisplayControl.

Para más información, consulte [Definición de un perfil técnico de RESTful en una directiva personalizada en Azure Active Directory B2C](restful-technical-profile.md) y [Controles de visualización](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar un ejemplo de una directiva de verificación de correo electrónico personalizado en GitHub:

[Verificación de correo electrónico personalizado: DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Para obtener información sobre el uso de una API REST personalizada o de cualquier proveedor de correo electrónico SMTP basado en HTTP, consulte [Definición de un perfil técnico de RESTful en una directiva personalizada en Azure Active Directory B2C](restful-technical-profile.md).
