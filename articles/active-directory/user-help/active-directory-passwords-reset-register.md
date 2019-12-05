---
title: 'Registro de la información de autenticación para llevar a cabo el restablecimiento de la contraseña: Azure AD'
description: Registre la información sobre su método de verificación para el autoservicio de restablecimiento de contraseña de Azure AD. De este modo, podrá restablecer su propia contraseña sin la ayuda del administrador.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9748c0d04f958648f7919b7ac29dc58d5a137ac
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279045"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre la información del método de comprobación para restablecer su propia contraseña

> [!IMPORTANT]
> ¿Está aquí porque no puede iniciar sesión? Si es así, consulte [Restablecimiento de la contraseña profesional o educativa](active-directory-passwords-update-your-own-password.md).

Como usuario final, puede restablecer su contraseña o desbloquear su cuenta con el autoservicio de restablecimiento de contraseñas (SSPR) de Azure Active Directory (Azure AD). Para poder usar esta funcionalidad, debe registrar los métodos de comprobación o confirmar los predefinidos que haya rellenado el administrador.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registro o confirmación de los datos de autenticación con SSPR

1. Abra el explorador web en el dispositivo y vaya a la [página de registro en el restablecimiento de contraseña](https://aka.ms/ssprsetup).
2. Escriba el nombre de usuario y la contraseña proporcionados por el administrador.
3. Según el modo en que el personal de TI haya realizado la configuración, una o varias de las opciones siguientes estarán a su disposición para que lleve a cabo la configuración y la verificación. Si el administrador tiene permiso para usar su información, puede rellenar una parte en su lugar.
    * **Teléfono del trabajo**: Solo el administrador puede establecer esta opción.
    * **Teléfono de autenticación**: Establezca esta opción en otro número de teléfono al que tenga acceso. Un ejemplo es un teléfono móvil que pueda recibir un mensaje de texto o una llamada.
    * **Correo electrónico de autenticación**: Establezca esta opción en una dirección de correo electrónico alternativa a la que pueda tener acceso sin usar la contraseña que quiere restablecer.
    * **Preguntas de seguridad**: El administrador ha aprobado esta lista de preguntas para que las responda. No puede usar la misma pregunta o respuesta más de una vez.
4. Proporcione la información requerida por el administrador y compruébela. Si hay más de una opción, se recomienda registrar varios métodos. Así se proporciona flexibilidad cuando uno de los métodos no esté disponible. Un ejemplo es cuando viaja y no puede tener acceso a su teléfono del trabajo.

    ![Registre los métodos de verificación y seleccione Finalizar][Register]

5. Seleccione **Finalizar**. Ahora puede usar SSPR cuando lo necesite.

Los datos que escriba en el **número de teléfono** o en el **correo electrónico de autenticación** no serán visibles en el directorio global. Las únicas personas que pueden ver estos datos son el propio usuario y los administradores. Solo el propio usuario puede ver las respuestas a las preguntas de seguridad.

Los administradores pueden requerir que confirme los métodos de verificación después de un período de tiempo para asegurarse de que todavía tiene los métodos adecuados registrados.

## <a name="common-problems-and-their-solutions"></a>problemas comunes y sus soluciones

 A continuación se presentan algunos casos terror comunes y sus soluciones:

| Caso de error| ¿Qué tipo de error aparece?| Solución |
| --- | --- | --- |
| Al escribir mi identificador de usuario, aparece una página "Póngase en contacto con su administrador" | Póngase en contacto con el administrador. <br> <br> Hemos detectado que la contraseña de su cuenta de usuario no está administrada por Microsoft. Como consecuencia, no podemos restablecer automáticamente su contraseña. <br> <br> Póngase en contacto con el personal de TI para obtener ayuda adicional. | Está viendo este mensaje porque el personal de TI administra su contraseña en el entorno local y no le permite restablecerla desde el vínculo **No se puede tener acceso a su cuenta**. <br> <br> Para restablecer la contraseña, póngase en contacto directamente con el personal de TI para obtener ayuda. Hágales saber que desea restablecer la contraseña para que puedan habilitar esta característica para usted.|
| Después de escribir mi identificador de usuario, recibo el error "Su cuenta no está habilitada para el restablecimiento de contraseña". | La cuenta no está habilitada para restablecer la contraseña. <br> <br> Su personal de TI no ha configurado la cuenta para utilizarla con este servicio. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje, es que el personal de TI no ha habilitado el restablecimiento de contraseña para su organización desde el vínculo **No se puede tener acceso a su cuenta** o bien no le han autorizado para usar la característica. <br> <br> Para restablecer la contraseña, seleccione el vínculo **Póngase en contacto con un administrador**. Se enviará un correo electrónico al personal de TI de su empresa. El correo electrónico les permite saber que desea restablecer la contraseña para que puedan habilitar esta característica para usted. |
| Después de escribir mi identificador de usuario, recibo el error "No se pudo comprobar su cuenta". | No se ha podido comprobar su cuenta. <br> <br> Si lo desea, podemos ponernos en contacto con un administrador de su organización para que restablezca la contraseña. | Si ve este mensaje, es que está habilitado para usar el restablecimiento de contraseña, pero no se ha registrado para usar el servicio. Para registrarse para el restablecimiento de contraseña, vaya a la [página de registro de restablecimiento de contraseña](https://aka.ms/ssprsetup) después de haber recuperado el acceso a su cuenta. <br> <br> Para restablecer la contraseña, seleccione el vínculo **Póngase en contacto con un administrador** para enviar un correo electrónico al personal de TI de la compañía. |

## <a name="next-steps"></a>Pasos siguientes

* [Cambiar la contraseña mediante el autoservicio de restablecimiento de contraseñas](active-directory-passwords-update-your-own-password.md)
* [Página de registro en el restablecimiento de contraseña](https://aka.ms/ssprsetup)
* [Portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/)
* [Cuando no puedes iniciar sesión en tu cuenta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro en el restablecimiento de contraseña que muestra los métodos registrados y botón Finalizar"

