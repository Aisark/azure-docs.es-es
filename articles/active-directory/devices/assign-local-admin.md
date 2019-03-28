---
title: Administración del grupo de administradores locales en dispositivos unidos a Azure AD | Microsoft Docs
description: Aprenda a asignar roles de Azure al grupo de administradores locales de un dispositivo Windows.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: joflore
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da55370df55bcd9122bf87c561b00f3106cc6c58
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521897"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Administración del grupo de administradores locales en dispositivos unidos a Azure AD

Para administrar un dispositivo Windows, debe ser miembro del grupo de administradores locales. Como parte del proceso de unión a Azure Active Directory (Azure AD), Azure AD actualiza la pertenencia de este grupo en un dispositivo. Puede personalizar la actualización de la pertenencia para satisfacer los requisitos de su negocio. Una actualización de pertenencia es, por ejemplo, útil si desea permitir que el personal del soporte técnico realice tareas que requieran derechos de administrador en un dispositivo.

En este artículo se explica cómo funciona la actualización de la pertenencia y cómo puede personalizarla durante una unión a Azure AD. El contenido de este artículo no se aplica a una unión a Azure AD **híbrido**.


## <a name="how-it-works"></a>Cómo funciona

Al conectar un dispositivo Windows con Azure AD mediante una unión a Azure AD, Azure AD agrega los siguientes principios de seguridad al grupo de administradores locales del dispositivo:

- El rol de administrador global de Azure AD
- El rol de administrador de dispositivos de Azure AD 
- El usuario que realiza la unión a Azure AD   

Al agregar los roles de Azure AD al grupo de administradores locales, puede actualizar los usuarios que pueden administrar un dispositivo en cualquier momento en Azure AD sin modificar nada en el dispositivo. Actualmente, no se pueden asignar grupos a un rol de administrador.
Azure AD también agrega el rol de administrador de dispositivos de Azure AD al grupo de administradores locales para admitir el principio de privilegio mínimo (PoLP). Además de los administradores globales, también puede habilitar a los usuarios a los que *solo* se ha asignado el rol de administrador de dispositivos para administrar uno. 


## <a name="manage-the-global-administrators-role"></a>Administración del rol de administrador global

Para ver y actualizar la pertenencia al rol de administrador global, consulte:

- [Visualización de todos los miembros de un rol de administrador en Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)

- [Asignación de un usuario a roles de administrador en Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Administración del rol de administrador de dispositivos 

En Azure Portal, puede administrar el rol de administrador de dispositivos en la página **Dispositivos**. Para abrir la página **Dispositivos**:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global o administrador de dispositivos.
2. En la barra de navegación izquierda, haga clic en **Azure Active Directory**. 
3. En la sección **Administrar**, haga clic en **Dispositivos**.
4. En la página **Dispositivos**, haga clic en **Configuración del dispositivo**.

Para modificar el rol de administrador de dispositivos, configure **Administradores locales adicionales en dispositivos unidos a Azure AD**.  

![Otros administradores locales](./media/assign-local-admin/10.png)

>[!NOTE]
> Esta opción requiere un inquilino de Azure AD Premium. 


Los administradores de dispositivos se asignan a todos los dispositivos unidos a Azure AD. No se puede definir el ámbito de los administradores de dispositivos a un conjunto específico de dispositivos. La actualización del rol de administrador de dispositivos no necesariamente tiene un impacto inmediato en los usuarios afectados. Para los dispositivos en los que un usuario ya ha iniciado sesión, la actualización de privilegios se lleva a cabo:
     

- Cuando un usuario cierra sesión.
- Después de 4 horas, cuando se emite un nuevo token de actualización principal. 




## <a name="manage-regular-users"></a>Administración de los usuarios normales

De forma predeterminada, Azure AD agrega el usuario que realiza la unión a Azure AD al grupo de administradores del dispositivo. Si desea evitar que los usuarios normales se conviertan en administradores locales, tiene las siguientes opciones:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot): Windows Autopilot le ofrece una opción para evitar que el usuario principal que realiza la unión se convierta en un administrador local. Puede hacerlo mediante la [creación de un perfil de Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
 
- [Inscripción masiva](https://docs.microsoft.com/intune/windows-bulk-enroll): una unión a Azure AD que se realiza en el contexto de una inscripción masiva tiene lugar en el contexto de un usuario creado automáticamente. Los usuarios que inician sesión después de que se hayan unido un dispositivo no se agregan al grupo de administradores.   



## <a name="manually-elevate-a-user-on-a-device"></a>Elevación manual de un usuario en un dispositivo 

Además de usar el proceso de unión a Azure AD, también puede elevar manualmente un usuario regular para que se convierta en un administrador local en un dispositivo específico. Este paso requiere que ya sea miembro del grupo de administradores locales. 

A partir de la versión **Windows 10 1709**, puede realizar esta tarea en **Configuración -> Cuentas -> Otros usuarios**. Seleccione **Agregar un usuario de trabajo o escuela**, escriba el UPN del usuario en **Cuenta de usuario** y seleccione *Administrador* en **Tipo de cuenta**  
 
Además, también puede agregar usuarios mediante el símbolo del sistema:

- Si los usuarios del inquilino están sincronizados desde Active Directory local, utilice `net localgroup administrators /add "Contoso\username"`.

- Si se crean los usuarios del inquilino de Azure AD, utilice `net localgroup administrators /add "AzureAD\UserUpn"`.


## <a name="considerations"></a>Consideraciones 

No puede asignar grupos al rol de administrador de dispositivos, solo se permiten usuarios individuales.

Los administradores de dispositivos se asignan a todos los dispositivos unidos a Azure AD. No se puede limitar a un conjunto específico de dispositivos.

Cuando se quitan usuarios del rol de administrador de dispositivos, estos siguen teniendo el privilegio de administrador local en un dispositivo siempre que hayan iniciado sesión en él. El privilegio se revoca durante el siguiente inicio de sesión, o al cabo de cuatro horas cuando se emite un nuevo token de actualización principal.



## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre cómo administrar dispositivos en Azure Portal, vea [Managing devices using the Azure portal (Administración de dispositivos con Azure Portal)](device-management-azure-portal.md)

- Para más información sobre el acceso condicional basado en dispositivo, consulte las [directivas de acceso condicional basado en dispositivos para Azure Active Directory](../conditional-access/require-managed-devices.md).


