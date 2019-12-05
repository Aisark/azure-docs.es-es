---
title: 'Registro combinado de SSPR y MFA: Azure Active Directory'
description: Registro de autoservicio de restablecimiento de contraseña y Multi-Factor Authentication de Azure AD (versión preliminar)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3221e6424b449066510667f8067e3bc6d8aec12a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381958"
---
# <a name="combined-security-information-registration-preview"></a>Registro de información de seguridad combinado (vista preliminar)

Antes del registro combinado, los usuarios se registraban a los métodos de autenticación para Azure Multi-factor Authentication y el autoservicio de restablecimiento de contraseña (SSPR) por separado. La gente estaba confundida por el hecho de que se usaban métodos parecidos para Multi-Factor Authentication y SSPR pero, aún así, se tenían que registrar en las dos características. Ahora, con el registro combinado, los usuarios pueden registrarse una vez y obtener las ventajas de Multi-Factor Authentication y SSPR.

![Página de mi perfil que muestra información de seguridad registrada de un usuario](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de habilitar la nueva experiencia, revise esta documentación centrada en el administrador y la documentación centrada en el usuario para asegurarse de que entiende las funciones y el efecto de esta característica. Con el fin de preparar a los usuarios para la nueva experiencia y ayudar a garantizar un lanzamiento satisfactorio, puede basar el aprendizaje en la [documentación de usuario](../user-help/user-help-security-info-overview.md).

El registro de información de seguridad combinado de Azure AD no está actualmente disponible para las nubes nacionales, como Azure US Government, Azure Alemania o Azure China 21Vianet.

|     |
| --- |
| El registro de información de seguridad combinado para el autoservicio de restablecimiento de contraseña de Azure Active Directory (Azure AD) y Multi-Factor Authentication es una Característica en versión preliminar (GB) pública de Azure AD. Para más información sobre las versiones preliminares, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Los usuarios que están habilitados para la versión preliminar original y la experiencia de registro combinado mejorada verán el nuevo comportamiento. Los usuarios que están habilitados para ambas experiencias verán solo la nueva experiencia de mi perfil. La nueva página de mi perfil se alinea con el aspecto del registro combinado y ofrece una experiencia perfecta para los usuarios. Los usuarios pueden ver mi perfil yendo a [https://myprofile.microsoft.com](https://myprofile.microsoft.com).

> [!NOTE] 
> Podría encontrar un mensaje de error al intentar obtener acceso a la opción de información de seguridad. Por ejemplo, "No podemos iniciar su sesión". En este caso, confirme que no tiene ningún objeto de configuración o de directiva de grupo que bloquee las cookies de terceros en el explorador web. 

Las páginas de mi perfil se localizan basándose en la configuración de idioma del equipo que accede a la página. Microsoft almacena el idioma usado más recientemente en la caché del explorador, por lo que los posteriores intentos de acceder a las páginas se representarán en el último idioma utilizado. Si se borra la caché, las páginas se volverán a representar. Si quiere forzar un idioma específico, puede agregar `?lng=<language>` al final de la dirección URL, donde `<language>` es el código del idioma que quiere representar.

![Configuración de SSPR u otros métodos de comprobación de seguridad](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponibles en el registro combinado

El registro combinado admite los siguientes métodos y acciones de autenticación:

|   | Register | Change | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sí (máximo de 5) | Sin | Sí |
| Aplicación autenticadora distinta | Sí (máximo de 5) | Sin | Sí |
| Token de hardware | Sin | No | Sí |
| Teléfono | Sí | Sí | Sí |
| Teléfono alternativo | Sí | Sí | Sí |
| Teléfono del trabajo | Sin | No | Sin |
| Email | Sí | Sí | Sí |
| Preguntas de seguridad | Sí | Sin | Sí |
| Contraseñas de aplicación | Sí | Sin | Sí |

> [!NOTE]
> Las contraseñas de aplicación solo están disponibles para los usuarios a los que se les ha exigido Multi-Factor Authentication. Las contraseñas de aplicación no están disponibles para los usuarios que están habilitados para Multi-Factor Authentication través de una directiva de acceso condicional.

Como método de Multi-Factor Authentication predeterminado, los usuarios pueden establecer una de las opciones siguientes:

- Microsoft Authenticator: notificación.
- Aplicación autenticadora o token de hardware: código.
- Llamada de teléfono.
- Mensaje de texto.

A medida que sigamos agregando más métodos de autenticación a Azure AD, estos estarán disponibles en el registro combinado.

## <a name="combined-registration-modes"></a>Modo de registro combinado

Existen dos modos de registro combinado: interrupción y administración.

- **Modo de interrupción** es una experiencia de asistente, que se presenta a los usuarios cuando se registran o actualizan su información de seguridad en el inicio de sesión.

- **Modo de administración** forma parte del perfil de usuario y permite administrar la información de seguridad a los usuarios.

En ambos modos, los usuarios que han registrado previamente un método que se puede usar para Multi-Factor Authentication necesitarán ejecutar Multi-Factor Authentication antes de acceder a su información de seguridad.

### <a name="interrupt-mode"></a>Modo de interrupción

El registro combinado respeta las directivas de Multi-Factor Authentication y SSPR, si ambos están habilitados para el inquilino. Estas directivas controlan si se interrumpe a un usuario para que se registre durante el inicio de sesión y los métodos que hay disponibles para el registro.

Estos son algunos escenarios en los que a los usuarios se les puede solicitar el registro o la actualización de su información de seguridad:

- Registro Multi-Factor Authentication exigido mediante Identity Protection: Se les pide a los usuarios que se registren durante el inicio de sesión. Registran los métodos Multi-Factor Authentication y los métodos SSPR (si el usuario está habilitado para SSPR).
- Registro Multi-Factor Authentication exigido mediante Multi-Factor Authentication por usuario: Se les pide a los usuarios que se registren durante el inicio de sesión. Registran los métodos Multi-Factor Authentication y los métodos SSPR (si el usuario está habilitado para SSPR).
- Registro Multi-Factor Authentication exigido mediante acceso condicional u otras directivas: Se les pide a los usuarios que se registren cuando usen un recurso que requiera Multi-Factor Authentication. Registran los métodos Multi-Factor Authentication y los métodos SSPR (si el usuario está habilitado para SSPR).
- Registro SSPR forzado: Se les pide a los usuarios que se registren durante el inicio de sesión. Solo registran los métodos SSPR.
- Actualización SSPR forzada: Los usuarios deben revisar su información de seguridad en un intervalo que establece el administrador. Se les muestra a los usuarios la información y pueden confirmar la información actual o realizar cambios si es necesario.

Cuando se exige el registro, se les muestra a los usuarios el número mínimo de los métodos necesarios para cumplir las directivas de Multi-Factor Authentication y SSPR, de la más a la menos segura.

Por ejemplo:

- Un usuario está habilitado para SSPR. La directiva SSPR requiere dos métodos para restablecer y ha habilitado el código de aplicación móvil, el correo y el teléfono.
   - Este usuario debe registrar dos métodos.
      - De manera predeterminada, se le muestra al usuario la aplicación autenticadora y el teléfono.
      - El usuario puede elegir registrar el correo en lugar de la aplicación autenticadora o el teléfono.

Este diagrama de flujo describe los métodos que se muestran a un usuario cuando se le interrumpe para que se registre durante el inicio de sesión:

![Diagrama de flujo de información de seguridad combinada](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Si tiene Multi-Factor Authentication y SSPR habilitadas, se recomienda que aplique el registro Multi-Factor Authentication.

Si la directiva SSPR requiere que los usuarios revisen su información de seguridad a intervalos normales, se interrumpe a los usuarios durante el inicio de sesión y se les muestra todos los métodos registrados. Pueden confirmar la información actual si está actualizado o realizar cambios en caso necesario.

### <a name="manage-mode"></a>Modo de administración

Los usuarios pueden acceder al modo de administración si van a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) o seleccionando **Información de seguridad** en mi perfil. Ahí, los usuarios pueden agregar métodos, eliminar o cambiar los métodos existentes, cambiar el método predeterminado, entre otras cosas.

## <a name="key-usage-scenarios"></a>Escenarios de uso claves

### <a name="set-up-security-info-during-sign-in"></a>Configuración de la información de seguridad durante el inicio de sesión

Un administrador tiene registro forzado.

Un usuario no ha configurado toda la información de seguridad requerida y entra en Azure Portal. Después de escribir el nombre de usuario y la contraseña, se le solicita al usuario que configure la información de seguridad. Luego, el usuario sigue los pasos que se muestran en el asistente para configurar la información de seguridad requerida. Si la configuración lo permite, el usuario puede configurar otros métodos aparte de los que se muestran de manera predeterminada. Después de completar el asistente, los usuarios revisan los métodos que han configurado y el método predeterminado para Multi-Factor Authentication. Para completar el proceso de instalación, el usuario confirma la información y continúa en Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Configuración de la información de seguridad desde mi perfil

Un administrador no tiene registro forzado.

Un usuario que todavía no ha configurado toda la información de seguridad requerida entra en [https://myprofile.microsoft.com](https://myprofile.microsoft.com). El usuario selecciona **Información de seguridad** en el panel izquierdo. Desde allí, el usuario decide agregar un método, selecciona uno de los que hay disponibles y sigue los pasos para configurarlo. Cuando termina, el usuario ve el método que acaba de configurar en la página de Información de seguridad.

### <a name="delete-security-info-from-my-profile"></a>Eliminación de información de seguridad desde mi perfil

Un usuario que previamente ha configurado al menos un método navega a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). El usuario decide eliminar uno de los métodos previamente registrados. Cuando termina, el usuario ya no ve ese método en la página de Información de seguridad.

### <a name="change-the-default-method-from-my-profile"></a>Cambio del método predeterminado desde mi perfil

Un usuario que previamente ha configurado al menos un método que se puede usar para Multi-Factor Authentication navega a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). El usuario cambia el método predeterminado actual a un método predeterminado distinto. Cuando termina, el usuario ve ese método predeterminado nuevo en la página de Información de seguridad.

## <a name="next-steps"></a>Pasos siguientes

[Forzar a los usuarios a que vuelvan a registrar los métodos de autenticación](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Habilitación del registro combinado en el inquilino](howto-registration-mfa-sspr-combined.md)

[Uso de SSPR y MFA, y creación de informes de detalles](howto-authentication-methods-usage-insights.md)

[Métodos disponibles para Multi-Factor Authentication y SSPR](concept-authentication-methods.md)

[Configuración del autoservicio de restablecimiento de contraseña](howto-sspr-deployment.md)

[Configuración de Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
