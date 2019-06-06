---
title: Descripción de directivas personalizadas del paquete de inicio en Azure Active Directory B2C | Microsoft Docs
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b52f1a4cb6837dd779dcf4edac140bb13e06eacb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509560"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Descripción de las directivas personalizadas del paquete del inicio de directivas personalizadas de Azure AD B2C

En esta sección se enumeran todos los elementos básicos de la directiva B2C_1A_base que se incluyen con el **paquete de inicio** y que se aprovechan para la creación de sus propias directivas mediante la herencia de la *directiva B2C_1A_base_extensions*.

Como tal, se centra particularmente en los tipos de notificaciones, las transformaciones de notificación, las definiciones de contenido, los proveedores de notificaciones con sus perfiles técnicos y los recorridos de usuario principales ya definidos.

> [!IMPORTANT]
> Microsoft no otorga ninguna garantía, expresa ni implícita, con respecto a la información proporcionada a continuación. En cualquier momento se pueden introducir cambios, antes de la hora de disponibilidad general, en ese mismo momento o después.

Sus propias directivas y la directiva B2C_1A_base_extensions pueden invalidar estas definiciones y extender esta directiva principal proporcionando unas nuevas, según sea necesario.

Los elementos básicos de la directiva *B2C_1A_base* son tipos de notificaciones, transformaciones de notificación y definiciones de contenido. Se puede hacer referencia a estos elementos en sus propias directivas o en la *directiva B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Esquemas de notificaciones

Estos esquemas de notificaciones se dividen en tres secciones:

1.  La primera sección que muestra las notificaciones mínimas necesarias para que los recorridos de usuario funcionen correctamente.
2.  Una segunda sección que muestra las notificaciones necesarias para que los parámetros de cadena de consulta y otros parámetros especiales se pasen a otros proveedores de notificaciones, en especial login.microsoftonline.com para la autenticación. **No modifique estas notificaciones**.
3.  Y, finalmente, una tercera sección que muestra las notificaciones adicionales opcionales que pueden recopilarse del usuario, almacenarse en el directorio y enviarse en tokens durante el inicio de sesión. En esta sección se pueden agregar los nuevos tipos de notificaciones que se recopilan del usuario o se envían en el token.

> [!IMPORTANT]
> El esquema de notificaciones contiene restricciones sobre ciertas notificaciones, como contraseñas y nombres de usuario. La directiva del marco de confianza (TF) trata a Azure AD como cualquier otro proveedor de notificaciones y todas sus restricciones se modelan en la directiva personalizada. Una directiva se podría modificar para agregar más restricciones o usar otro proveedor de notificaciones para el almacenamiento de credenciales que tenga sus propias restricciones.

A continuación se enumeran los tipos de notificaciones disponibles.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Notificaciones que son necesarias para los recorridos de usuario

Las siguientes notificaciones son necesarias para que los recorridos de usuario funcionen correctamente:

| Tipo de notificaciones | DESCRIPCIÓN |
|-------------|-------------|
| *UserId* | Nombre de usuario |
| *signInName* | Nombre de inicio de sesión |
| *tenantId* | Identificador de inquilino (id.) del objeto de usuario en Azure AD B2C |
| *objectId* | Identificador de objeto (id.) del objeto de usuario en Azure AD B2C |
| *password* | Contraseña |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Directivas de contraseña usadas por Azure AD B2C para determinar la seguridad de la contraseña, su caducidad, etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Número de teléfono del usuario. |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Dirección de correo electrónico que puede usarse para ponerse en contacto con el usuario. |
| *signInNamesInfo.emailAddress* | Dirección de correo electrónico que el usuario puede usar para iniciar sesión. |
| *otherMails* | Direcciones de correo electrónico que pueden usarse para ponerse en contacto con el usuario. |
| *userPrincipalName* | Nombre de usuario tal como se almacena en Azure AD B2C |
| *upnUserName* | Nombre de usuario para la creación del nombre principal de usuario. |
| *mailNickName* | Nombre de alias de correo electrónico del usuario tal como se almacena en Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Notificación que especifica si se recopilaron atributos del usuario. |
| *executed-PhoneFactor-Input* | Notificación que especifica si se recopiló un nuevo número de teléfono del usuario. |
| *authenticationSource* | Especifica si el usuario se autenticó en el proveedor de identidades sociales, login.microsoftonline.com o en una cuenta local. |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Notificaciones necesarias para los parámetros de cadena de consulta y otros parámetros especiales

Para pasar parámetros especiales (incluidos algunos parámetros de cadena de consulta), se requieren las siguientes notificaciones a otros proveedores de notificaciones:

| Tipo de notificaciones | DESCRIPCIÓN |
|-------------|-------------|
| *nux* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *nca* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *prompt* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *mkt* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *lc* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *grant_type* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *scope* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *client_id* | Parámetro especial pasado para la autenticación con la cuenta local en login.microsoftonline.com. |
| *objectIdFromSession* | Parámetro proporcionado por el proveedor de administración de sesiones predeterminado para indicar que el identificador de objeto se ha recuperado de una sesión SSO |
| *isActiveMFASession* | Parámetro proporcionado por la administración de sesiones MFA para indicar que el usuario tiene una sesión activa de MFA. |

### <a name="additional-optional-claims-that-can-be-collected"></a>Notificaciones (opcionales) adicionales que se pueden recopilar

Las siguientes notificaciones son notificaciones adicionales que pueden recopilarse de los usuarios, almacenarse en el directorio y enviarse en el token. Tal y como se ha descrito anteriormente, se pueden agregar notificaciones adicionales a esta lista.

| Tipo de notificaciones | DESCRIPCIÓN |
|-------------|-------------|
| *givenName* | Nombre dado del usuario (también conocido como nombre de pila) |
| *surname* | Apellido del usuario (también conocido como nombre de familia) |
| *Extension_picture* | Foto del usuario de las redes sociales |

## <a name="claim-transformations"></a>Transformaciones de notificación

Las transformaciones de notificación disponibles se enumeran a continuación.

| Transformación de notificación | DESCRIPCIÓN |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definiciones de contenido

En esta sección se describen las definiciones de contenido ya declaradas en la directiva *B2C_1A_base*. Se puede hacer referencia a estas definiciones de contenido, se pueden anular o se pueden extender según sea necesario en sus propias directivas, así como en la directiva *B2C_1A_base_extensions*.

| Proveedor de notificaciones | DESCRIPCIÓN |
|-----------------|-------------|
| *Facebook* | |
| *Inicio de sesión en una cuenta local* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Autoafirmado* | |
| *Cuenta local* | |
| *Administración de sesiones* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *Emisor de tokens* | |

## <a name="technical-profiles"></a>Perfiles técnicos

En esta sección se describen los perfiles técnicos ya declarados por el proveedor de notificaciones en la directiva *B2C_1A_base*. Se puede hacer referencia adicionalmente a estos perfiles técnicos, se pueden anular o se pueden extender según sea necesario en sus propias directivas, así como en la directiva *B2C_1A_base_extensions*.

### <a name="technical-profiles-for-facebook"></a>Perfiles técnicos de Facebook

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Perfiles técnicos de inicio de sesión en una cuenta local

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Perfiles técnicos de Phone Factor

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Perfiles técnicos de Azure Active Directory

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *AAD-Common* | Perfil técnico incluido por los otros perfiles técnicos de AAD-xxx |
| *AAD-UserWriteUsingAlternativeSecurityId* | Perfil técnico de inicios de sesión sociales |
| *AAD-UserReadUsingAlternativeSecurityId* | Perfil técnico de inicios de sesión sociales |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Perfil técnico de inicios de sesión sociales |
| *AAD-UserWritePasswordUsingLogonEmail* | Perfil técnico de cuentas locales |
| *AAD-UserReadUsingEmailAddress* | Perfil técnico de cuentas locales |
| *AAD-UserWriteProfileUsingObjectId* | Perfil técnico de actualización de registros de usuario mediante objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Perfil técnico de actualización de registros de usuario mediante objectId |
| *AAD-UserWritePasswordUsingObjectId* | Perfil técnico de actualización de registros de usuario mediante objectId |
| *AAD-UserReadUsingObjectId* | Perfil técnico que se usa para leer datos después de que se autentica el usuario |

### <a name="technical-profiles-for-self-asserted"></a>Perfiles técnicos autoafirmados

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Perfiles técnicos de cuenta local

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Perfiles técnicos de administración de sesiones

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Nombre de perfil que se usa para eliminar la ambigüedad en una sesión de AAD entre el registro y el inicio de sesión. |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Perfiles técnicos del motor de directiva de marco de confianza

Actualmente, no se ha definido ningún perfil técnico para el proveedor de notificaciones **Trustframework Policy Engine TechnicalProfiles**.

### <a name="technical-profiles-for-token-issuer"></a>Perfiles técnicos del emisor de tokens

| Perfil técnico | DESCRIPCIÓN |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Recorridos de usuario

En esta sección se describen los recorridos de usuario ya declarados en la directiva *B2C_1A_base*. Se puede hacer referencia adicionalmente a estos recorridos de usuario, se pueden anular o se pueden extender según sea necesario en sus propias directivas, así como en la directiva *B2C_1A_base_extensions*.

| Recorrido de usuario | DESCRIPCIÓN |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
