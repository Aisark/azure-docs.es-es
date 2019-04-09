---
title: 'Tutorial: Configuración de Zendesk para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056595"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configuración de Zendesk para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en Zendesk y Azure Active Directory (Azure AD) para configurar Azure AD con el objetivo de aprovisionar y desaprovisionar automáticamente usuarios o grupos en Zendesk.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD
* Un inquilino de Zendesk con el plan [Enterprise](https://www.zendesk.com/product/pricing/) o uno superior habilitado
* Una cuenta de usuario de Zendesk con permisos de administrador

> [!NOTE]
> La integración del aprovisionamiento de Azure AD se basa en la [API REST de Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), que está disponible para los equipos de Zendesk con el plan Enterprise o superiores.

## <a name="adding-zendesk-from-the-gallery"></a>Adición de Zendesk desde la galería

Antes de configurar Zendesk para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar Zendesk desde la galería de aplicaciones de Azure AD hasta la lista de aplicaciones SaaS administradas.

**Para agregar Zendesk desde la Galería de aplicaciones de Azure AD, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione la opción **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Zendesk**, seleccione **Zendesk** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Zendesk en la lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Asignación de usuarios a Zendesk

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a Zendesk. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a Zendesk:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Sugerencias importantes para asignar usuarios a Zendesk

* Los roles de Zendesk se rellenan automática y dinámicamente en la interfaz de usuario de Azure Portal actualmente. Antes de asignar roles de Zendesk a los usuarios, asegúrese de que se ha completado una sincronización inicial con Zendesk para recuperar los roles más recientes en el inquilino de Zendesk.

* Se recomienda asignar un único usuario de Azure AD a Zendesk para probar la configuración inicial de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos una vez realizadas las pruebas correctamente.
  
* Se recomienda asignar un único usuario de Azure AD a Zendesk para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a Zendesk, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configuración del aprovisionamiento automático de usuarios en Zendesk 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de AD Azure para crear, actualizar y deshabilitar usuarios o grupos en Zendesk en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para Zendesk siguiendo las instrucciones del [tutorial de inicio de sesión único de Zendesk](zendesk-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para Zendesk en Azure AD:

1. Inicie sesión en el [portal Azure](https://portal.azure.com) y seleccione **aplicaciones empresariales**, seleccione **todas las aplicaciones**, a continuación, seleccione **Zendesk**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Zendesk**.

    ![Vínculo de Zendesk en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. En la sección **Credenciales del administrador**, escriba el **Nombre del usuario administrador**, **Token secreto** y **Dominio** de la cuenta de Zendesk. Algunos ejemplos de estos valores son:

   * En el campo **Nombre de usuario administrador**, rellene el nombre de usuario de la cuenta de administrador del inquilino de Zendesk. Ejemplo: admin@contoso.com.

   * En el campo **Token secreto**, rellene el token secreto tal y como se describe en el paso 6.

   * En el campo **Dominio**, rellene el subdominio del inquilino de Zendesk.
     Ejemplo: para una cuenta con una dirección URL de inquilino con el valor `https://my-tenant.zendesk.com`, el subdominio sería **my-tenant**.

6. El **Token secreto** para su cuenta de Zendesk está ubicado en **Administración > API > Configuración**.
   Asegúrese de que **Token de acceso** está establecido en **Habilitado**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Tras rellenar los campos que se muestran en el paso 5, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a Zendesk. Si la conexión no se establece, asegúrese de que la cuenta de Zendesk tiene permisos de administrador y pruebe de nuevo.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Haga clic en **Save**(Guardar).

10. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Users to Zendesk** (Sincronizar usuarios de Azure Active Directory con Zendesk).

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Revise los atributos de usuario que se sincronizan entre Azure AD y Zendesk en la sección **Attribute Mapping** (Asignación de atributos). Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de Zendesk para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. En la sección **Mappings** (Asignaciones), seleccione **Synchronize Azure Active Directory Groups to Zendesk** (Sincronizar grupos de Azure Active Directory con Zendesk).

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Revise los atributos de grupo que se sincronizan entre Azure AD y Zendesk en la sección **Attribute Mapping** (Asignación de atributos). Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con los grupos de Zendesk para las operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar el servicio de aprovisionamiento de Azure AD para Zendesk, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en Zendesk.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Aprovisionamiento de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en Zendesk.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitaciones del conector

* Zendesk admite el uso de grupos para usuarios con los roles de agente únicamente. Consulte la [documentación de Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups) para más información.

* Cuando un rol personalizado se asigna a un usuario o grupo, el servicio automático de aprovisionamiento de usuarios de Azure AD también asignará el rol predeterminado **Agente**. Solo los **Agentes** pueden asignar un rol personalizado. Para más información, consulte la [documentación de Zendesk API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Recursos adicionales

* [Administración de aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga información sobre cómo revisar los registros y obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
