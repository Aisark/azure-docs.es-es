---
title: 'Guía de inicio rápido: Autoservicio de restablecimiento de contraseña de Azure AD'
description: En esta guía de inicio rápido, aprenderá a configurar rápidamente el autoservicio de restablecimiento de contraseña de Azure AD para permitir a los usuarios restablecer sus propias contraseñas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58e3254d499e013dc686bf6b7d53f919a457c901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371291"
---
# <a name="quickstart-self-service-password-reset"></a>Guía de inicio rápido: Restablecimiento de la contraseña de autoservicio

En esta guía de inicio rápido aprenderá a configurar el autoservicio de restablecimiento de contraseña (SSPR) como un medio sencillo con el que los administradores de TI pueden permitir que los usuarios restablezcan sus contraseñas o desbloqueen sus cuentas.

## <a name="prerequisites"></a>Requisitos previos

* Un inquilino de Azure AD activo con al menos una licencia de prueba habilitada.
* Una cuenta con privilegios de administrador global.
* Un usuario de prueba sin privilegios de administrador con una contraseña que conozca; si necesita crear un usuario, consulte el artículo [Inicio rápido: Adición de nuevos usuarios a Azure Active Directory](../add-users-azure-active-directory.md).
* Un grupo piloto para las pruebas del que el usuario de prueba sin privilegios de administrador sea miembro; si necesita crear un grupo, consulte el artículo [Creación de un grupo e incorporación de miembros en Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Habilitar el autoservicio de restablecimiento de contraseña

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. Desde el inquilino de Azure AD existente, en **Azure Portal**, en **Azure Active Directory**, seleccione **Restablecimiento de contraseña**.

2. En la página **Propiedades**, bajo la opción **Se habilitó el restablecimiento de contraseña del autoservicio**, elija **Seleccionados**.
    * En **Seleccionar grupo**, seleccione el grupo piloto creado como parte de la sección de requisitos previos de este artículo.
    * Haga clic en **Save**(Guardar).

3. En la página **Métodos de autenticación**, elija lo siguiente:
   * Número de métodos requeridos para el restablecimiento: **1**
   * Métodos disponibles para los usuarios:
      * **Correo electrónico**
      * **Código de la aplicación para dispositivos móviles (vista previa)**
   * Haga clic en **Save**(Guardar).

     ![Elección de métodos de autenticación para SSPR][Authentication]

4. En la página **Registro**, elija lo siguiente:
   * Exigir a los usuarios que se registren al iniciar sesión: **Sí**
   * Establecer el número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación: **365**

## <a name="test-self-service-password-reset"></a>Autoservicio de restablecimiento de contraseña de prueba

Ahora probaremos la configuración del autoservicio de restablecimiento de prueba con un usuario de prueba. Dado que Microsoft impone requisitos de autenticación estrictos para las cuentas de administrador de Azure, realizar la prueba con una cuenta de administrador puede cambiar el resultado. Para más información acerca de la directiva de contraseñas de administrador, consulte nuestro artículo sobre la [directiva de contraseñas](concept-sspr-policy.md).

1. Abra una nueva ventana del explorador en modo de incógnito o InPrivate, y vaya a [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Inicie sesión con un usuario de prueba sin privilegios de administrador y registre su teléfono de autenticación.
3. Una vez que haya terminado, haga clic en el botón marcado como **parece correcto** y cierre la ventana del explorador.
4. Abra una nueva ventana del explorador en modo de incógnito o InPrivate, y vaya a [https://aka.ms/sspr](https://aka.ms/sspr).
5. Escriba el identificador de usuario de los usuarios de prueba sin privilegios de administrador y los caracteres del CAPTCHA y, a continuación, haga clic en **Siguiente**.
6. Siga los pasos de comprobación para restablecer la contraseña

## <a name="clean-up-resources"></a>Limpieza de recursos

Deshabilitar el autoservicio de restablecimiento de contraseña es fácil. Abra el inquilino de Azure AD y vaya a **Propiedades** > **Restablecimiento de contraseña** y seleccione **Ninguno** en **Se habilitó el restablecimiento de contraseña del autoservicio**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a configurar rápidamente el autoservicio de restablecimiento de contraseña para los usuarios que solo están en la nube. Para averiguar cómo completar una implementación más detallada, continúe con nuestra guía de implementación.

> [!div class="nextstepaction"]
> [Implementación del autoservicio de restablecimiento de contraseña](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticación de Azure AD disponibles y cantidad requerida"
