---
title: Configuración de la directiva de registro de autenticación multifactor en Azure Active Directory Identity Protection | Microsoft Docs
description: Aprenda cómo configurar la directiva de registro de autenticación multifactor en Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd1049551d7dbc4823636dfdc00f64afab72cdf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008603"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Instrucciones: Configuración de la directiva de registro de autenticación multifactor

Azure AD Identity Protection ayuda a administrar la puesta en servicio de registro de la autenticación multifactor (MFA) configurando una directiva para requerir el registro de MFA. Este artículo explica lo que puede usarse la directiva y cómo configurarlo.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>¿Qué es la directiva de registro de la autenticación multifactor?

Azure Multi-Factor Authentication es un método que se usa para comprobar la identidad del usuario que requiere usar algo más que un nombre de usuario y una contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios. En el orden de los usuarios puedan responder a las solicitudes MFA, primero debe registrar para MFA. 

Se recomienda requerir Azure Multi-Factor Authentication en los inicios de sesión de usuario por los siguientes motivos:

- Ofrece autenticación segura con una gama de opciones de comprobación sencillas.

- Desempeña un papel fundamental en la preparación de su organización para protegerse y recuperarse frente a cuentas comprometidas.


Para obtener más detalles sobre la MFA, consulte [¿qué es Azure Multi-factor Authentication?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>¿Cómo puedo acceder a la directiva de registro de MFA?
   
En la hoja **Azure AD Identity Protection**, en la sección [Configurar](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy), está la directiva de registro de MFA.
   
![Directiva de MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Configuración de directiva

Al configurar la directiva de registro de MFA, deberá establecer:

- Usuarios y grupos a los que se aplica la directiva:

    ![Usuarios y grupos](./media/howto-mfa-policy/11.png)

- El control que desea aplicar-requisito de registro de MFA:  

    ![Access](./media/howto-mfa-policy/12.png)

- Estado de la directiva:

    ![Aplicar directiva](./media/howto-mfa-policy/14.png)


El cuadro de diálogo de configuración de directivas proporciona una opción para calcular el impacto de la configuración.

![Impacto estimado](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Experiencia del usuario


Para obtener información general de la experiencia de usuario relacionada, consulte:

* [Flujo de registro de autenticación multifactor](flows.md#multi-factor-authentication-registration).  
* [Experiencias de inicio de sesión con Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Pasos siguientes

Para obtener una visión general de Azure AD Identity Protection, consulte la [Introducción a Azure AD Identity Protection](overview.md).
