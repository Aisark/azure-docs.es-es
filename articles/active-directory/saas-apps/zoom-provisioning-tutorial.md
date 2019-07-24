---
title: 'Tutorial: Configuración de Zoom para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario en Zoom.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: ca79b3901e3933e25c5be92673e0c5bcc464782d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167128"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configuración de Zoom para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en Zoom y Azure Active Directory (Azure AD) para configurar Azure AD a fin de aprovisionar y cancelar automáticamente el aprovisionamiento de usuarios o grupos en Zoom.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD
* [Un inquilino de Zoom](https://zoom.us/pricing)
* Una cuenta de usuario de Zoom con permisos de administrador

## <a name="add-zoom-from-the-gallery"></a>Adición de Zoom desde la galería

Antes de configurar Zoom para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Zoom desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar Zoom desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Zoom**, seleccione **Zoom** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zoom en la lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Asignación de usuarios a Zoom

Azure Active Directory usa un concepto denominado *asignaciones* para determinar los usuarios que han de recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Zoom. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Zoom:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Sugerencias importantes para asignar usuarios a Zoom

* Se recomienda asignar un único usuario de Azure AD a Zoom para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Zoom, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configuración del aprovisionamiento automático de usuarios en Zoom 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en Zoom en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para Zoom siguiendo las instrucciones del [tutorial de inicio de sesión único de Zoom](zoom-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configuración del aprovisionamiento automático de usuarios para Zoom en Azure AD

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Zoom**.

    ![Vínculo a Zoom en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://api.zoom.us/scim` en **URL de inquilino**. Para recuperar el valor del **token secreto** de su cuenta de Zoom, siga el tutorial tal y como se describe en el paso 6.

6. Inicie sesión en la [consola de administración de Zoom](https://zoom.us/signin). Navegue a **Advanced > Zoom for Developers** (Avanzado > Zoom para desarrolladores) en el panel de navegación izquierdo.

    ![Integrations (Integraciones) de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Navegue a **Manage** (Administrar) en la esquina superior derecha de la página. 

    ![Instalación de Zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Navegue a la aplicación de Azure AD que ha creado. 
    
    ![Aplicación de Zoom](media/zoom-provisioning-tutorial/zoom03.png)

    Seleccione **Credenciales de la aplicación** en el panel de navegación izquierdo.

    ![Aplicación de Zoom](media/zoom-provisioning-tutorial/zoom04.png)

    Recupere el valor del token de JWT que se muestra a continuación e insértelo en el campo **Token secreto** de Azure AD. Si necesita un nuevo token que no expire, tendrá que volver a configurar la fecha de expiración, lo que generará automáticamente un nuevo token. 

    ![Instalación de Zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Zoom. Si la conexión no se establece, asegúrese de que la cuenta de Zoom tiene permisos de administrador y pruebe de nuevo.

    ![Se necesita el cifrado de tokens](common/provisioning-testconnection-tenanturltoken.png)

8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

9. Haga clic en **Save**(Guardar).

10. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Zoom** (Sincronizar usuarios de Azure Active Directory con Zoom).

    ![Asignaciones de usuario de Zoom](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Examine los atributos de usuario que se sincronizan entre Azure AD y Zoom en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades **Coincidentes** se usan para establecer correspondencia con las cuentas del usuario en Zoom a fin de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.
    
     ![Asignaciones de usuario de Zoom](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar el servicio de aprovisionamiento de Azure AD para Zoom, cambie la opción **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.
    
    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

14. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Zoom.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

15. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Almacenamiento de la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Zoom.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitaciones del conector

* Zoom no admite el aprovisionamiento de grupos.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)
