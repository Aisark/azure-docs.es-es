---
title: 'Tutorial: Configuración de Box RingCentral para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar Azure Active Directory para aprovisionar y desaprovisionar automáticamente las cuentas de usuario para RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: d30195cb9aceac0e785f01ce8c2ce78d7a9d4da7
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805780"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Tutorial: Configuración de RingCentral para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en RingCentral y Azure Active Directory (Azure AD) para configurar Azure AD a fin de aprovisionar y cancelar automáticamente el aprovisionamiento de usuarios o grupos en RingCentral.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está actualmente en versión preliminar pública. Para más información sobre los términos de uso generales de Microsoft Azure para las características en versión preliminar, consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tutorial se supone que ya cuenta con los requisitos previos siguientes:

* Un inquilino de Azure AD
* [Un inquilino de RingCentral](https://www.ringcentral.com/office/plansandpricing.html)
* Una cuenta de usuario de RingCentral con permisos de administrador.

## <a name="assigning-users-to-ringcentral"></a>Asignación de usuarios a RingCentral

Azure Active Directory usa un concepto denominado *asignaciones* para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a RingCentral. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a RingCentral:
* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ringcentral"></a>Sugerencias importantes para asignar usuarios a RingCentral

* Se recomienda asignar un único usuario de Azure AD a RingCentral para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a RingCentral, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="set-up-ringcentral-for-provisioning"></a>Configuración de RingCentral para el aprovisionamiento

1. Inicie sesión en [RingCentral Admin Console](https://login.ringcentral.com/sw.html) (Consola de administración de RingCentral). Vaya a **Herramientas > Integración de directorios**.

    ![Consola de administración de RingCentral](media/ringcentral-provisioning-tutorial/admin.png)

2.  Elija **SCIM** en **Select Directory Provider** (Seleccionar proveedor de directorios). (En el futuro, habrá una opción denominada Azure Active Directory). Haga clic en **Enable SCIM Service** (Habilitar el servicio SCIM).

    ![Agregar SCIM a RingCentral](media/ringcentral-provisioning-tutorial/scim.png)

3.  Póngase en contacto con el equipo de soporte técnico de RingCentral en matthew.hunt@ringcentral.com para obtener un **token de autenticación de SCIM**. Este valor se escribirá en el campo Token secreto de la pestaña Aprovisionamiento de la aplicación RingCentral en Azure Portal.

> [!NOTE]
> Para asignar licencias a los usuarios, consulte el vínculo del vídeo [aquí](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="add-ringcentral-from-the-gallery"></a>Agregar RingCentral desde la galería

Antes de configurar RingCentral para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar RingCentral desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas.

**Para agregar RingCentral desde la galería de aplicaciones de Azure AD, siga estos pasos:**

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **RingCentral**, seleccione **RingCentral** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![RingCentral en la lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ringcentral"></a>Configuración del aprovisionamiento automático de usuarios en RingCentral 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en RingCentral en función de las asignaciones de grupos o usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para RingCentral siguiendo las instrucciones del [tutorial de inicio de sesión único de RingCentral](ringcentral-tutorial.md). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

> [!NOTE]
> Para más información sobre el punto de conexión SCIM de RingCentral, consulte [Referencia de la API de RingCentral](https://developers.ringcentral.com/api-reference).

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para RingCentral en Azure AD:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **RingCentral**.

    ![Vínculo de RingCentral en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En la sección **Credenciales de administrador**, escriba `https://platform.ringcentral.com/scim/v2` en la **URL de inquilino**. Escriba el valor **SCIM Authentication Token** (Token de autenticación de SCIM) recuperado anteriormente en **Token secreto**. Haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a RingCentral. Si la conexión no se establece, asegúrese de que la cuenta de RingCentral tiene permisos de administrador y pruebe otra vez.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

7. Haga clic en **Save**(Guardar).

8. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Pingboard** (Sincronizar usuarios de Azure Active Directory con RingCentral).

    ![Asignaciones de usuario de RingCentral](media/ringcentral-provisioning-tutorial/usermappings.png)

9. Revise los atributos de usuario que se sincronizan entre Azure AD y RingCentral en la sección **Attribute Mappings** (Asignaciones de atributos). Los atributos seleccionados como propiedades de **Matching** (Coincidencia) se usan para buscar coincidencias con las cuentas de usuario de RingCentral con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Atributos de usuario de RingCentral](media/ringcentral-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar el servicio de aprovisionamiento de Azure AD para RingCentral, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

12. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en RingCentral.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

13. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos al informe de la actividad de aprovisionamiento, que describe todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en RingCentral.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)

