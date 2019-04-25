---
title: 'Copia de seguridad y recuperación con la aplicación Microsoft Authenticator: Azure Active Directory | Microsoft Docs'
description: Aprenda a realizar copias de seguridad y recuperar sus credenciales de cuenta con la aplicación Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9634e2578ea256d1dec71389f676ee53627e6272
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474236"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Copia seguridad y recuperación de las credenciales de cuenta con la aplicación Microsoft Authenticator

**Se aplica a:**

- Dispositivos iOS

La aplicación Microsoft Authenticator realiza una copia de seguridad en la nube de las credenciales de la cuenta y la configuración de aplicación relacionada, como el orden de las cuentas. Después de la copia de seguridad, también puede utilizar la aplicación para recuperar la información en un dispositivo nuevo y evitar así un posible bloqueo o tener que volver a crear las cuentas.

> [!IMPORTANT]
> Necesita una cuenta Microsoft personal y una cuenta de iCloud para cada ubicación de almacenamiento de copia de seguridad. Pero en esa ubicación de almacenamiento, puede copiar varias cuentas. Por ejemplo, puede tener una cuenta personal, una educativa y otra de otro fabricante como Facebook, Google, etc.
> 
> Solo se almacenan las credenciales de la cuenta personal y de terceros, lo que incluye el nombre de usuario y el código de verificación de la cuenta necesarios para demostrar la identidad. No almacenamos ninguna otra información asociada con las cuentas, como mensajes de correo electrónico o archivos. Tampoco asociamos ni compartimos las cuentas de ningún modo con otros productos ni servicios. Finalmente, el administrador de TI no obtendrá información de estas cuentas.

## <a name="back-up-your-account-credentials"></a>Copia de seguridad de las credenciales de cuenta
Antes de hacer copias de seguridad las credenciales, debe tener:

- Una [cuenta Microsoft](https://account.microsoft.com/account) personal como cuenta de recuperación.

- Una [cuenta de iCloud](https://www.icloud.com/) para la ubicación de almacenamiento real. 

El inicio simultáneo en las dos cuentas ofrece mayor seguridad para la información de copia de seguridad.

**Para activar la copia de seguridad en la nube**
-   En el dispositivo iOS, seleccione **Ajustes**, **Copia de seguridad** y active **Copia en iCloud**.

    Las credenciales de cuenta se copian en la cuenta de iCloud.

    ![Pantalla de configuración de iOS que muestra la ubicación de los valores de Copia en iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recuperación de las credenciales de cuenta en el dispositivo nuevo
Puede recuperar las credenciales de su cuenta de la cuenta de iCloud, con la misma cuenta de recuperación de Microsoft que configuró al realizar la copia de seguridad de la información.

### <a name="to-recover-your-information"></a>Recuperación de la información
1.  En el dispositivo iOS, abra la aplicación Microsoft Authenticator y seleccione **Iniciar recuperación** en la parte inferior de la pantalla.

    ![Aplicación de Microsoft Authenticator que muestra dónde debe hacer clic en Iniciar recuperación](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2.  Inicie sesión en la cuenta de recuperación con la misma cuenta Microsoft personal que utilizó durante la copia de seguridad.

    Las credenciales de la cuenta se recuperan en el dispositivo nuevo.

Una vez finalizada la recuperación, podrá notar que los códigos de verificación de la cuenta Microsoft personal en la aplicación Microsoft Authenticator difieren en el teléfono nuevo y el antiguo. Esto es así porque cada dispositivo tiene sus propias credenciales únicas, aunque ambas son válidas y funcionan al iniciar sesión con el teléfono asociado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperación de otras cuentas con más detalles de verificación
Si usa notificaciones push con las cuentas personales, profesionales o educativas, obtendrá una alerta en la pantalla para que proporcione más detalles de verificación para recuperar la información. Dado que las notificaciones push requieren credenciales vinculadas con los dispositivos específicos y no se envían a través de la red, debe demostrar su identidad para que se creen en el dispositivo.

Para las cuentas Microsoft personales, puede probar su identidad mediante la especificación de la contraseña junto con un número de teléfono o correo electrónico alternativos. Para las cuentas profesionales o educativas, debe digitalizar un código QR que le proporcione el proveedor de la cuenta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Proporcionar verificación adicional para las cuentas personales
1.  En la pantalla **Cuentas** de la aplicación Microsoft Authenticator, seleccione la flecha desplegable junto a la cuenta que desea recuperar.

    ![Aplicación Microsoft Authenticator que muestra las cuentas disponibles con las flechas desplegables asociadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2.  Seleccione **Sign in to recover** (Iniciar sesión para la recuperación), escriba la contraseña y confirme el número de teléfono o la dirección de correo electrónico como verificación adicional.

    ![Aplicación Microsoft Authenticator que permite escribir la información de inicio de sesión](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Proporcionar verificación adicional para las cuentas profesionales o educativas
1.  En la pantalla **Cuentas** de la aplicación Microsoft Authenticator, seleccione la flecha desplegable junto a la cuenta que desea recuperar.

    ![Aplicación Microsoft Authenticator que muestra las cuentas disponibles con las flechas desplegables asociadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2.  Seleccione **Digitalizar código QR para completar la recuperación** y lea el código QR.

    ![Aplicación Microsoft Authenticator que permite digitalizar el código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para más información acerca de cómo obtener un código QR, consulte [Introducción a la aplicación Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) o [Configuración de la información de seguridad para usar una aplicación autenticadora (versión preliminar)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), en función de si el administrador ha activado la información de seguridad.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Solución de problemas de copia de seguridad y recuperación
Existen diversos motivos para que la copia de seguridad no esté disponible:

-   **Cambio de sistema operativo.** La copia de seguridad se almacena en la opción de almacenamiento en la nube proporcionada por el sistema operativo del teléfono, lo que significa no estará disponible si cambia de iOS a Android (y viceversa). En esta situación, debe volver a crear manualmente la cuenta en la aplicación.

-   **Problemas de red o contraseña.** Asegúrese de que está conectado a una red y ha iniciado sesión en su cuenta de iCloud con el mismo AppleID que usó en su último iPhone.

-   **Eliminación por error.** Es posible que haya eliminado la cuenta de copia de seguridad del dispositivo anterior o al administrar la cuenta de almacenamiento en la nube. En esta situación, debe volver a crear manualmente la cuenta en la aplicación.

-   **Cuentas de Microsoft Authenticator existentes.** Si ya ha configurado cuentas en la aplicación Microsoft Authenticator, la aplicación no podrá recuperar las cuentas de copia de seguridad. Evitar la recuperación garantiza que los datos de la cuenta no se sobreescriben con información obsoleta. En esta situación, debe eliminar cualquier información de cuenta existente de la configuración de las cuentas existentes en la aplicación Authenticator para recuperar la copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha realizado la copia de seguridad de las credenciales de la cuenta y las ha recuperado en un dispositivo nuevo, use la aplicación Microsoft Authenticator para verificar su identidad. Para obtener más información, consulte [iniciar sesión en sus cuentas mediante la aplicación Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Temas relacionados

- [¿Qué es la aplicación Microsoft Authenticator?](user-help-auth-app-overview.md)

- [Preguntas frecuentes de la aplicación Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
