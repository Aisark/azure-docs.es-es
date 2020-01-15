---
title: Establecimiento del tiempo de expiración de inactividad en el nivel de directorio para los usuarios de Azure Portal | Microsoft Docs
description: Los administradores pueden exigir el tiempo de inactividad máximo antes de que se cierre una sesión. La directiva de tiempo de expiración de inactividad se establece en el nivel de directorio.
services: azure-portal
keywords: configuración, tiempo de expiración
author: mblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 55136b5418b0c455ef66bd322f519c1e52114b93
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640506"
---
# <a name="set-directory-level-inactivity-timeout"></a>Establecimiento del tiempo de expiración de inactividad de nivel de directorio

La configuración de tiempo de expiración de inactividad ayuda a proteger los recursos frente al acceso no autorizado si los usuarios se olvidan de proteger su estación de trabajo. Cuando un usuario ha estado inactivo durante un tiempo, se cerrará automáticamente su sesión de Azure Portal. Los administradores pueden exigir el tiempo de inactividad máximo antes de que se cierre una sesión. El tiempo de expiración de inactividad se aplica en el nivel de directorio. Para más información sobre los directorios, vea [Introducción a Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Configuración de las opciones de tiempo de expiración de inactividad

Si es un administrador y quiere aplicar un valor de tiempo de expiración de inactividad para todos los usuarios de Azure Portal, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Configuración** en el encabezado de página global.
3. Seleccione el texto del vínculo **Configurar el tiempo de espera del nivel de directorio**.

    ![Captura de pantalla en la que se muestra la configuración del portal con el texto del vínculo resaltado](./media/admin-timeout/settings.png)

4. Se abrirá una nueva página. En la página **Configurar el tiempo de espera de inactividad del nivel de directorio**, seleccione **Habilitar el tiempo de espera de inactividad del nivel de directorio para Azure Portal** para activar la opción.
5. A continuación, escriba las **horas** y los **minutos** de tiempo máximo que un usuario puede estar inactivo antes de que su sesión se cierre de forma automática.
6. Seleccione **Aplicar**.

    ![Captura de pantalla en la que se muestra la página para establecer el tiempo de espera de inactividad del nivel de directorio](./media/admin-timeout/configure.png)

Para confirmar que la directiva de tiempo de expiración de inactividad se ha establecido correctamente, seleccione **Notificaciones** en el encabezado de página global. Compruebe que se muestra una notificación correcta.

  ![Captura de pantalla en la que se muestra el mensaje de notificación correcta para el tiempo de expiración de inactividad del nivel de directorio](./media/admin-timeout/confirmation.png)

La configuración surte efecto para las nuevas sesiones. No se aplicará de forma inmediata a ningún usuario que ya haya iniciado sesión.

> [!NOTE]
> Si un administrador ha configurado un valor de tiempo de expiración del nivel de directorio, los usuarios pueden invalidar la directiva y establecer su propia duración de cierre de sesión inactiva. Pero el usuario tendrá que elegir un intervalo temporal que sea menor que el establecido en el nivel de directorio.
>

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de las preferencias de Azure Portal](set-preferences.md)
* [Exportación o eliminación de la configuración de usuario](azure-portal-export-delete-settings.md)
* [Activación del contraste alto o cambio de tema](azure-portal-change-theme-high-contrast.md)
