---
title: 'Tutorial: Configuración de Dropbox para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d53d10b036a37489be0b7aae6208880044b766a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280033"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configuración de Dropbox for Business para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es explicar los pasos que hay que realizar en Dropbox for Business y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD en Dropbox for Business.

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Una suscripción a Dropbox for Business con inicio de sesión único habilitado.
*   Una cuenta de usuario de Dropbox for Business con permisos de administrador de equipo.

## <a name="assigning-users-to-dropbox-for-business"></a>Asignación de usuarios a Dropbox for Business

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación Dropbox for Business. Una vez decidido, puede asignar estos usuarios a la aplicación Dropbox for Business siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Sugerencias importantes para asignar usuarios a Dropbox for Business

*   Se recomienda asignar un único usuario de Azure AD a Dropbox for Business para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar a un usuario a Dropbox for Business, debe seleccionar un rol de usuario válido. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de cuentas de usuario de Dropbox for Business, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas en Dropbox for Business en función de la asignación de grupos y usuarios de Azure AD.

>[!Tip]
>Puede elegir también habilitar el inicio de sesión único basado en SAML para Dropbox for Business, mediante las instrucciones que se proporcionan en [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar el aprovisionamiento automático de cuentas de usuario, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado Dropbox for Business para el inicio de sesión único, busque su instancia de Dropbox for Business mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Dropbox for Business** en la galería de aplicaciones. Seleccione Dropbox for Business en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de Dropbox for Business y luego, la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Aprovisionamiento](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. En **Credenciales de administrador**, haga clic en **Autorizar**. Se abre un cuadro de diálogo de inicio de sesión de Dropbox for Business en una nueva ventana del explorador.

6. En el cuadro de diálogo **Sign in to Dropbox to link with Azure AD** (Iniciar sesión en Dropbox para vincularlo a Azure AD), inicie sesión en su inquilino de Dropbox for Business.

     ![Aprovisionamiento de usuarios](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "Aprovisionamiento de usuarios")

7. Confirme que desea conceder permiso de Azure Active Directory para realizar cambios en el inquilino de Dropbox for Business. Haga clic en **Permitir**.
    
      ![Aprovisionamiento de usuarios](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "Aprovisionamiento de usuarios")

8. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Dropbox for Business. Si se produce un error de conexión, asegúrese de que su cuenta de Dropbox for Business tiene permisos de administrador de equipo y vuelva a intentar el paso de **Autorizar**.

9. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

10. Haga clic en **Guardar**.

11. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to Dropbox for Business** (Sincronizar usuarios de Azure Active Directory con Dropbox for Business).

12. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y Dropbox for Business. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para buscar coincidencias con las cuentas de usuario de Dropbox for Business con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Dropbox for Business, cambie el **Estado de aprovisionamiento** a **Activado** en la sección Configuración.

14. Haga clic en **Guardar**.

Comienza la sincronización inicial de todos los usuarios y grupos asignados a Dropbox for Business en la sección Usuarios y grupos. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos a los registros de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Dropbox for Business.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configuración del inicio de sesión único](dropboxforbusiness-tutorial.md)
