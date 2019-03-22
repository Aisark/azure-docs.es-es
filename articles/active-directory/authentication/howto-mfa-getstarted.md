---
title: Introducción a Azure MFA en la nube
description: Introducción a Microsoft Azure Multi-Factor Authentication con acceso condicional
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07e98d5a93b8576c06e9be8274a974b1b574da4b
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217756"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Implementación de Azure Multi-factor Authentication en la nube

Empezar con Azure Multi-factor Authentication (Azure MFA) es un proceso sencillo.

Antes de empezar, asegúrese de que cumple los siguientes requisitos previos:

* Una cuenta de administrador global en el inquilino de Azure AD. Si necesita ayuda para completar este paso, consulte nuestro artículo [Get started with Azure AD](../get-started-azure-ad.md) (Introducción a Azure AD).
* Licencias correctas asignadas a los usuarios. Si necesita más información, consulte el artículo [Cómo conseguir Azure Multi-Factor Authentication](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Elección del modo de habilitación

**Enabled by conditional access policy** (Habilitada mediante la directiva de acceso condicional): en este artículo se explica este método. Se trata del medio más flexible para habilitar la verificación en dos pasos para los usuarios. La habilitación de la directiva de acceso condicional solo funciona con Azure MFA en la nube y es una característica premium de Azure AD.

**Enabled by Azure AD Identity Protection** (Habilitada por Azure AD Identity Protection): este método utiliza la directiva de riesgos de Azure AD Identity Protection para exigir la verificación en dos pasos solo según el riesgo de inicio de sesión para todas las aplicaciones en la nube. Este método requiere una licencia de Azure Active Directory P2. Puede encontrar más información acerca de este método en [Procedimiento de configuración de la directiva de riesgo del usuario](../identity-protection/howto-user-risk-policy.md).

**Enabled by changing user state** (Habilitada mediante el cambio de estado de usuario): se trata del método tradicional para exigir la verificación en dos pasos. Funciona tanto en Azure MFA en la nube como en el servidor de Azure MFA. El uso de este método requiere que los usuarios realicen la verificación **cada vez** que inicien sesión e invalida las directivas de acceso condicional. Puede encontrar más información sobre este método en [Exigencia de verificación en dos pasos para un usuario](howto-mfa-userstates.md).

> [!Note]
> Puede encontrar más información sobre licencias y precios en las páginas de precios de [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) y [Multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="choose-authentication-methods"></a>Elección de métodos de autenticación

Habilite al menos un método de autenticación para los usuarios según los requisitos de la organización. Se ha detectado que, cuando se habilita para los usuarios, la aplicación Microsoft Authenticator ofrece la mejor experiencia de usuario. Si tiene que saber qué métodos están disponibles y cómo configurarlos, consulte el artículo [¿Qué son los métodos de autenticación?](concept-authentication-methods.md).

> [!IMPORTANT]
> A partir de marzo de 2019 las opciones de llamada de teléfono no estará disponible para los usuarios MFA y SSPR en inquilinos de Azure AD gratuito de prueba. Mensajes SMS no se ven afectados por este cambio. Llamada de teléfono seguirá estando disponible para los usuarios de inquilinos de Azure AD de pago. Este cambio solo afecta a los inquilinos de Azure AD gratuito de prueba.

## <a name="get-users-to-enroll"></a>Inscripción de los usuarios

Cuando haya habilitado la directiva de acceso condicional, los usuarios se verán obligados a inscribirse la próxima vez que usen una aplicación protegida con la directiva. Si habilita una directiva que requiera MFA para todos los usuarios en todas las aplicaciones en la nube, esta acción podría causar serios problemas para los usuarios y el departamento de soporte técnico. Se recomienda pedir a los usuarios que registren los métodos de autenticación con antelación mediante el portal de registro en [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Muchas organizaciones consideran que la creación de mensajes de correo electrónico, tarjetas de mesa y pósteres ayuda a fomentar la adopción.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitación de Multi-Factor Authentication con acceso condicional

Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador global.

### <a name="choose-verification-options"></a>Elección de opciones de comprobación

Para poder habilitar Azure Multi-Factor Authentication, la organización debe determinar qué opciones de comprobación permite. En este ejercicio, se habilitan Llamada al teléfono y Mensaje de texto al teléfono, ya que son opciones genéricas que la mayoría puede utilizar. Puede encontrar más información sobre los métodos de autenticación y su uso en el artículo, [What are authentication methods?](concept-authentication-methods.md) (¿Qué son los métodos de autenticación?).

1. Vaya a **Azure Active Directory**, **Usuarios**, **Multi-Factor Authentication**.

   ![Acceso al portal de Multi-factor Authentication desde la hoja de usuarios de Azure AD en Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. En la nueva pestaña que se abre, vaya a **valor de configuración del servicio**.
1. En **opciones de comprobación**, active todas las casillas para los métodos disponibles para los usuarios.

   ![Configuración de métodos de verificación en la pestaña de configuración del servicio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Haga clic en **Save**(Guardar).
5. Cierre la pestaña **configuración del servicio**.

### <a name="create-conditional-access-policy"></a>Creación de una directiva de acceso condicional

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador global.
1. Vaya a **Azure Active Directory** , **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Escriba un nombre descriptivo para la directiva.
1. En **usuarios y grupos**:
   * En la pestaña **Incluir**, seleccione el botón de radio **Todos los usuarios**.
   * RECOMENDACIÓN: En la pestaña **Excluir**, active la casilla de **Usuarios y grupos** y elija un grupo que se utilizará para las exclusiones cuando los usuarios no tengan acceso a sus métodos de autenticación.
   * Haga clic en **Done**(Listo).
1. En **Aplicaciones en la nube**, seleccione el botón de selección **Todas las aplicaciones en la nube**.
   * OPCIONAL: En la pestaña **Excluir**, elija aplicaciones en la nube para las que su organización no requiere MFA.
   * Haga clic en **Done**(Listo).
1. En la sección **Condiciones**:
   * OPCIONAL: Si ha habilitado Azure Identity Protection, puede optar por integrar la evaluación de riesgos de inicio de sesión en la directiva.
   * OPCIONAL: Si ha configurado ubicaciones de confianza o ubicaciones con nombre, puede especificar que se incluyan o excluyan esas ubicaciones de la directiva.
1. En **Conceder**, asegúrese de que el botón de selección **Conceder acceso** está seleccionado.
    * Active la casilla **Requerir autenticación multifactor**.
    * Haga clic en **Seleccionar**.
1. Omita la sección **Sesión**.
1. Establezca la opción **Habilitar directiva** en **Activada**.
1. Haga clic en **Create**(Crear).

![Creación de una directiva de acceso condicional que habilite MFA para los usuarios de Azure Portal en el grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Prueba de Azure Multi-Factor Authentication

Para confirmar que la directiva de acceso condicional funciona, pruebe a iniciar sesión en un recurso que no requiera MFA y, luego, en la instancia de Azure Portal que requiera MFA.

1. Abra una nueva ventana del explorador en modo de incógnito o InPrivate, y vaya a [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Inicie sesión con el usuario de prueba que creó como parte de la sección de requisitos previos de este artículo y tenga en cuenta que no debe pedirle que complete MFA.
   * Cierre la ventana del explorador.
2. Abra una nueva ventana del explorador en modo de incógnito o InPrivate, y vaya a [https://portal.azure.com](https://portal.azure.com).
   * Inicie sesión con el usuario de prueba creado como parte de la sección de requisitos previos de este artículo y tenga en cuenta que ahora se le debería exigir que se registre y use Azure Multi-Factor Authentication.
   * Cierre la ventana del explorador.

## <a name="next-steps"></a>Pasos siguientes

¡Enhorabuena!, ha configurado Azure Multi-Factor Authentication en la nube.

¿Por qué se solicitó o no a un usuario que realizará MFA? Consulte la sección [Informe de inicios de sesión de Azure AD en los informes del documento Azure Multi-factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Para configurar opciones adicionales tales como direcciones IP de confianza, mensajes de voz personalizados y alertas de fraude, vea el artículo [Configuración de Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Puede encontrar información sobre cómo administrar la configuración de usuario de Azure Multi-factor Authentication en el artículo [Administrar la configuración de usuario con Azure Multi-Factor Authentication en la nube](howto-mfa-userdevicesettings.md).

[Registro convergente para autoservicio de restablecimiento de contraseña de Azure AD y Azure Multi-Factor Authentication](concept-registration-mfa-sspr-converged.md).
