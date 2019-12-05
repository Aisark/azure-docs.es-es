---
title: 'Tutorial: Aprovisionamiento de usuarios para LucidChart: Azure AD'
description: Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de LucidChart.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3791992586edbdc5188c3078b1f1bb108ce580d7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276865"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configuración de LucidChart para aprovisionar usuarios automáticamente

El objetivo de este tutorial es explicar los pasos que hay que realizar en LucidChart y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD en LucidChart. 

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Un inquilino de Azure Active Directory
* Un inquilino de LucidChart con el [plan Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) o uno superior habilitado
* Una cuenta de usuario de LucidChart con permisos de administrador

## <a name="assigning-users-to-lucidchart"></a>Asignación de usuarios a LucidChart

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación LucidChart. Una vez decidido, puede asignar estos usuarios a la aplicación LucidChart siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Sugerencias importantes para asignar usuarios a LucidChart

* Se recomienda asignar un único usuario de Azure AD a LucidChart para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a LucidChart, debe seleccionar el rol **Usuario** u otro válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. El rol **Acceso predeterminado** no funciona para realizar el aprovisionamiento y estos usuarios se omiten.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Configuración del aprovisionamiento de usuarios en LucidChart

Esta sección le guía en la conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de LucidChart, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de LucidChart en función de la asignación de grupos y usuarios Azure AD.

> [!TIP]
> También puede habilitar el inicio de sesión único basado en SAML para LucidChart siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configuración del aprovisionamiento de cuentas de usuario automático para LucidChart en Azure AD

1. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2. Si ya ha configurado LucidChart para el inicio de sesión único, busque la instancia de LucidChart mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **LucidChart** en la galería de aplicaciones. Seleccione LucidChart en los resultados de búsqueda y agréguelo a la lista de aplicaciones.

3. Seleccione la instancia de LucidChart y luego, la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. En la sección **Credenciales de administrador**, escriba el **Token secreto** generado por la cuenta de LucidChart (puede encontrar el token en su cuenta: **Equipo** > **Integración de la aplicación** > **SCIM**).

    ![Aprovisionamiento de LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de LucidChart. Si la conexión no se establece, asegúrese de que la cuenta de LucidChart tiene permisos de administrador e intente el paso 5 de nuevo.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

8. Haga clic en **Save**(Guardar).

9. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to LucidChart** (Sincronizar usuarios de Azure Active Directory con LucidChart).

10. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y LucidChart. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de LucidChart con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para LucidChart, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**

12. Haga clic en **Save**(Guardar).

Esta operación inicia la sincronización inicial de todos los usuarios y grupos asignados a LucidChart en la sección Usuarios y grupos. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los registros de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)
