---
title: 'Configuración de la información de seguridad (versión preliminar) para usar llamadas de teléfono: Azure Active Directory | Microsoft Docs'
description: Cómo configurar la información de seguridad para verificar su identidad mediante llamadas de teléfono.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1620be30d8cdf3a592ab0fc118938783579689
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475000"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Configuración de la información de seguridad (versión preliminar) para usar llamadas de teléfono
Puede seguir estos pasos para agregar sus métodos de restablecimiento de contraseña y comprobación en dos pasos. Una vez que haya configurado esto por primera vez, puede volver a la página **Información de seguridad** para agregar, actualizar o eliminar su información de seguridad.

Si se le pide que configure esto justo después de haber iniciado sesión en su cuenta profesional o educativa, consulte los pasos detallados en el artículo de [configuración de su información de seguridad desde el símbolo del sistema de página de inicio de sesión](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Información de seguridad no admite el uso de extensiones telefónicas. Incluso si agrega el formato correcto +1 4255551234X12345, las extensiones se quitan antes de realizarse la llamada.
> 
> Si no ve una opción telefónica, es posible que su organización no le permita usar un número de teléfono para la verificación. En este caso, tendrá que elegir otro método o ponerse en contacto con su administrador para obtener más ayuda.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configuración de llamadas de teléfono desde la página Información de seguridad
Dependiendo de la configuración de su organización, es posible que pueda usar llamadas de teléfono como uno de sus métodos de información de seguridad.

>[!Note]
>Si desea recibir un mensaje de texto en lugar de una llamada de teléfono, siga los pasos descritos en el artículo de [configuración de la información de seguridad para usar mensajería de texto](security-info-setup-text-msg.md).

### <a name="to-set-up-phone-calls"></a>Para configurar llamadas de teléfono

1. Inicie sesión en su cuenta profesional o educativa y vaya a su página https://myprofile.microsoft.com/.

    ![Página Mi Perfil, que muestra los vínculos de Información de seguridad resaltados](media/security-info/securityinfo-myprofile-page.png)

2. Seleccione **Información de seguridad** en el panel de navegación izquierdo o en el vínculo del bloque **Información de seguridad** y, después, seleccione **Agregar método** en la página **Información de seguridad**.

    ![Página Información de seguridad con la opción Agregar método resaltada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. En la página **Agregar un método**, seleccione **Teléfono** en la lista desplegable y, después, seleccione **Agregar**.

    ![Cuadro Agregar método, con Teléfono seleccionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. En la página **Teléfono**, escriba el número de teléfono de su dispositivo móvil, elija **Llamarme** y, a continuación, seleccione **Siguiente**.

    ![Adición de número de teléfono y elección de llamadas de teléfono](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Responda a la llamada de teléfono de verificación, enviada al número de teléfono que escribió, y siga las instrucciones.

    La página cambia para mostrar su éxito.

    ![Notificación de éxito, que conecta el número de teléfono, la opción para recibir llamadas de teléfono y su cuenta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    La información de seguridad se actualiza y puede usar llamadas de teléfono para comprobar su identidad al usar la comprobación en dos pasos o el restablecimiento de contraseña. Si desea hacer que las llamadas de teléfono sean su método predeterminado, consulte la sección sobre cómo [cambiar su método de información de seguridad predeterminado](#change-your-default-security-info-method) de este artículo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Eliminación de llamadas de teléfono de sus métodos de información de seguridad
Si ya no desea usar las llamadas de teléfono como método de información de seguridad, puede quitarlo de la página **Información de seguridad**.

>[!Important]
>Si elimina las llamadas de teléfono por error, no existe modo alguno de deshacerlo. Tendrá que volver a agregar el método, siguiendo los pasos de la sección sobre cómo [configurar llamadas de teléfono](#set-up-phone-calls-from-the-security-info-page) de este artículo.

### <a name="to-delete-phone-calls"></a>Para eliminar las llamadas telefónicas

1. En la página **Información de seguridad**, seleccione el vínculo **Eliminar** situado junto a la opción **Teléfono**.

    ![Vínculo para eliminar el método del teléfono de la información de seguridad](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Seleccione **Sí** en el cuadro de confirmación para eliminar el número de **teléfono**. Una vez que su número de teléfono se elimine, se quitará de su información de seguridad y desaparecerá de la página **Información de seguridad**. Si **Teléfono** es su método predeterminado, el valor predeterminado cambiará a otro método disponible.
    
## <a name="change-your-default-security-info-method"></a>Cambio de su método de información de seguridad predeterminado
Si desea que las llamadas de teléfono sean el método predeterminado usado al iniciar sesión en su cuenta profesional o educativa mediante la comprobación en dos pasos o para las solicitudes de restablecimiento de contraseña, puede establecerlo en la página **Información de seguridad**.

### <a name="to-change-your-default-security-info-method"></a>Para cambiar su método de información de seguridad predeterminado

1. En la página **Información de seguridad**, seleccione el vínculo **Cambiar** situado junto a la información **Método de inicio de sesión predeterminado**.

    ![Cambio de vínculo del método de inicio de sesión predeterminado](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Seleccione **Teléfono: llamada ( *_su_número_de_teléfono_* )** en la lista desplegable de métodos disponibles y, a continuación, seleccione **Confirmar**.

    ![Elección de método para el inicio de sesión predeterminado](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    El método predeterminado usado para el inicio de sesión se cambia en **Teléfono: llamada ( *_su_número_de_teléfono_* )** .

## <a name="additional-security-info-methods"></a>Métodos de información de seguridad adicionales
Tiene opciones adicionales para determinar cómo su organización se pone en contacto con usted para comprobar su identidad, basándose en lo está intentando hacer. Entre estas opciones se incluyen:

- **Aplicación autenticadora.** Descargue y use una aplicación autenticadora para obtener una notificación de aprobación o un código de aprobación generado de forma aleatoria para la verificación en dos pasos o el restablecimiento de contraseña. Para obtener instrucciones paso a paso sobre cómo configurar y usar la aplicación Microsoft Authenticator, consulte [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Configuración de la información de seguridad para usar una aplicación de autenticador).

- **Texto de dispositivo móvil.** Escriba el número del dispositivo móvil y obtendrá un código que podrá usar para la verificación en dos pasos o el restablecimiento de contraseña. Para obtener instrucciones paso a paso sobre cómo comprobar su identidad con un mensaje de texto (SMS), consulte [Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md) (Configuración de la información de seguridad para usar mensajería de texto).

- **Dirección de correo electrónico.** Escriba la dirección de correo electrónico profesional o educativa para recibir un correo electrónico para el restablecimiento de contraseña. Esta opción no está disponible para la verificación en dos pasos. Para obtener instrucciones detalladas sobre cómo configurar el correo electrónico, vea [Configuración de la información de seguridad para usar el correo electrónico](security-info-setup-email.md).

- **Preguntas de seguridad.** Responda a algunas preguntas de seguridad creadas por el administrador para su organización. Esta opción solo está disponible para el restablecimiento de contraseña y no para la verificación en dos pasos. Para obtener instrucciones paso a paso sobre cómo configurar las preguntas de seguridad, consulte el artículo [Configuración de la información de seguridad para usar las preguntas de seguridad](security-info-setup-questions.md).
    
    >[!Note]
    >Si faltan algunas de estas opciones, lo más probable es que su organización no permita esos métodos. Si este es el caso, tendrá que elegir uno de los métodos que sí están disponibles o ponerse en contacto con su administrador para obtener más ayuda.

## <a name="next-steps"></a>Pasos siguientes

- Restablezca la contraseña si ha perdido u olvidado la que tenía acudiendo al [portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/) o siga los pasos descritos en el artículo [Restablecimiento de la contraseña profesional o educativa](user-help-reset-password.md).

- Obtenga soluciones de problemas, sugerencias y ayuda para los problemas de inicio de sesión en el artículo [Cuando no puedes iniciar sesión en tu cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).