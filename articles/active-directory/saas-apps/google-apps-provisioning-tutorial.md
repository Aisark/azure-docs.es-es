---
title: 'Tutorial: Configuración de G Suite para el aprovisionamiento automático de usuarios con Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4c08802b9a19398e7968901974cad86d9d946a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120320"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Configuración de G Suite para el aprovisionamiento automático de usuarios

El objetivo de este tutorial es mostrar los pasos que se realizan en G Suite y Azure Active Directory (Azure AD) para configurar Azure AD a fin de aprovisionar y desaprovisionar usuarios o grupos en G Suite.

> [!NOTE]
> Este tutorial describe un conector que se crea sobre el servicio de aprovisionamiento de usuarios de Azure AD. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> El conector de G Suite se actualizó recientemente, en octubre de 2019. Entre los cambios realizados en el conector de G Suite se incluyen los siguientes:
- Se ha agregado compatibilidad con atributos adicionales de usuario y grupo de G Suite. 
- Se han actualizado los nombres de atributo de destino de G Suite para que coincidan con lo que se define [aquí](/azure/active-directory/manage-apps/customize-application-attributes).
- Se han actualizado las asignaciones de atributos predeterminadas.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con G Suite, se necesitan los siguientes elementos:

- Un inquilino de Azure AD
- [Un inquilino de G Suite](https://gsuite.google.com/pricing.html)
- Una cuenta de usuario en G Suite con permisos de administrador

## <a name="assign-users-to-g-suite"></a>Asignación de usuarios a G Suite

Azure Active Directory usa el concepto de asignación para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto del aprovisionamiento automático de usuarios, solo se sincronizan los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el aprovisionamiento automático de usuarios, debe decidir qué usuarios o grupos de Azure AD necesitan acceder a G Suite. Una vez que lo decida, puede seguir estas instrucciones para asignar dichos usuarios o grupos a G Suite:

* [Asignar un usuario o grupo a una aplicación empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Sugerencias importantes para asignar usuarios a G Suite

* Se recomienda asignar un único usuario de Azure AD a G Suite para probar la configuración de aprovisionamiento automático de usuarios. Más tarde, se pueden asignar otros usuarios o grupos.

* Al asignar un usuario a G Suite, debe seleccionar un rol válido específico de la aplicación (si está disponible) en el cuadro de diálogo de asignación. Los usuarios con el rol de **Acceso predeterminado** quedan excluidos del aprovisionamiento.

## <a name="setup-g-suite-for-provisioning"></a>Configuración de G Suite para el aprovisionamiento

Antes de configurar G Suite para el aprovisionamiento automático de usuarios con Azure AD, deberá habilitar el aprovisionamiento SCIM en G Suite.

1. Inicie sesión en la [consola de administración de G Suite](https://admin.google.com/) con su cuenta de administrador y haga clic en **Security** (Seguridad). Si no ve el vínculo, puede estar oculto debajo del menú **More Controls** (Más controles) en la parte inferior de la pantalla.

    ![Selección de seguridad.][10]

2. En la página **Security** (Seguridad), haga clic en **API Reference** (Referencia de API).

    ![Selección de Referencia de API.][15]

3. Seleccione **Enable API access**.

    ![Selección de Referencia de API.][16]

   > [!IMPORTANT]
   > El nombre en Azure AD de cada uno de los usuarios que quiera aprovisionar en G Suite **tiene que** estar vinculado a un dominio personalizado. Por ejemplo, G Suite no acepta los nombres de usuario parecidos a bob@contoso.onmicrosoft.com. Por otro lado, bob@contoso.com se acepta. Puede cambiar el dominio de un usuario existente siguiendo las instrucciones indicadas [aquí](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4.  Una vez que haya agregado y comprobado los dominios personalizados que desee con Azure AD, deberá comprobarlos de nuevo con G Suite. Para comprobar los dominios en G Suite, consulte los pasos siguientes:

    a. En la [consola de administración de G Suite](https://admin.google.com/), seleccione **Domains** (Dominios).

    ![Selección de dominios][20]

    b. Haga clic en **Add a domain or a domain alias** (Agregar un dominio o un alias de dominio).

    ![Adición de un nuevo dominio][21]

    c. Seleccione **Add another domain** (Añadir otro dominio) y escriba el nombre del dominio que desee agregar.

    ![Especificación de un nombre de dominio][22]

    d. Haga clic en **Continue and verify domain ownership** (Continuar y comprobar la propiedad del dominio). A continuación, siga los pasos para comprobar que posee el nombre de dominio. Para obtener instrucciones completas sobre cómo comprobar un dominio con Google, consulte [Verificar que eres el propietario de un sitio web](https://support.google.com/webmasters/answer/35179).

    e. Repita los pasos anteriores para todos los dominios adicionales que vaya a agregar a G Suite.

5. A continuación, determine qué cuenta de administrador quiere usar para administrar el aprovisionamiento de usuarios en G Suite. Vaya a **Admin Roles** (Roles de administrador).

    ![Selección de Google Apps][26]
    
6. En el **rol administrativo** de esa cuenta, edite los valores de **Privileges** (Privilegios) para ese rol. Asegúrese de que habilita todos los **privilegios de la API de administración** para que esta cuenta pueda usarse para el aprovisionamiento.

    ![Selección de Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Incorporación de G Suite desde la galería

Para configurar G Suite para el aprovisionamiento automático de usuarios con Azure AD, es preciso agregar G Suite desde la galería de aplicaciones de Azure AD a la lista de aplicaciones SaaS administradas. 

1. En **[Azure Portal](https://portal.azure.com)** , en el panel de navegación izquierdo, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del panel.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **G Suite**, seleccione **G Suite** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![G Suite en la lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configuración del aprovisionamiento automático de usuarios en G Suite 

Esta sección le guía por los pasos necesarios para configurar el servicio de aprovisionamiento de Azure AD para crear, actualizar y deshabilitar usuarios o grupos en G Suite en función de las asignaciones de grupos y usuarios de Azure AD.

> [!TIP]
> También puede optar por habilitar el inicio de sesión único basado en SAML para G Suite siguiendo las instrucciones del [tutorial de inicio de sesión único de G Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático de usuarios, aunque estas dos características se complementan entre sí.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar el aprovisionamiento automático de usuarios para G Suite en Azure AD:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Seleccione **Aplicaciones empresariales** y luego **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **G Suite**.

    ![Vínculo a G Suite en la lista de aplicaciones](common/all-applications.png)

3. Seleccione la pestaña **Aprovisionamiento**.

    ![Pestaña Aprovisionamiento](common/provisioning.png)

4. Establezca el **modo de aprovisionamiento** en **Automático**.

    ![Pestaña Aprovisionamiento](common/provisioning-automatic.png)

5. En **Credenciales de administrador**, haga clic en **Autorizar**. Se abrirá un cuadro de diálogo de autorización de Google en una nueva ventana del explorador.

    ![Autorización de G Suite](media/google-apps-provisioning-tutorial/authorize.png)

6. Confirme que quiere conceder permisos de Azure AD para realizar cambios en el inquilino de G Suite. Seleccione **Aceptar**.

    ![Confirme los permisos.][28]

7. En Azure Portal, seleccione **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación. Si la conexión no se establece, asegúrese de que la cuenta de G Suite tiene permisos de administrador de equipo. Luego vuelva a intentar el paso **Autorizar**.

8. En el campo **Correo electrónico de notificación**, escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento y active la casilla **Enviar una notificación por correo electrónico cuando se produzca un error**.

    ![Correo electrónico de notificación](common/provisioning-notification-email.png)

8. Haga clic en **Save**(Guardar).

9. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to G Suite** (Sincronizar usuarios de Azure Active Directory con G Suite).

    ![Asignaciones de usuario de G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

10. Revise los atributos de usuario que se sincronizan entre Azure AD y G Suite en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer coincidencias con las cuentas de usuario de G Suite con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Atributos de usuario de G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

11. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Groups to G Suite** (Sincronizar grupos de Azure Active Directory con G Suite).

    ![Asignaciones de grupo de G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

12. Revise los atributos de grupo que se sincronizan entre Azure AD y G Suite en la sección **Asignación de atributos**. Los atributos seleccionados como propiedades de **Coincidencia** se usan para establecer coincidencias con los grupos de G Suite con el objetivo de realizar operaciones de actualización. Seleccione el botón **Guardar** para confirmar los cambios.

    ![Atributos de grupo de G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Para configurar filtros de ámbito, consulte las siguientes instrucciones, que se proporcionan en el artículo [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar el servicio de aprovisionamiento de Azure AD para G Suite, cambie el **Estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

    ![Estado de aprovisionamiento activado](common/provisioning-toggle-on.png)

15. Elija los valores deseados en **Ámbito**, en la sección **Configuración**, para definir los usuarios o grupos que desea que se aprovisionen en G Suite.

    ![Ámbito del aprovisionamiento](common/provisioning-scope.png)

16. Cuando esté listo para realizar el aprovisionamiento, haga clic en **Guardar**.

    ![Guardar la configuración de aprovisionamiento](common/provisioning-configuration-save.png)

Esta operación inicia la sincronización inicial de todos los usuarios o grupos definidos en **Ámbito** en la sección **Configuración**. La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si el servicio de aprovisionamiento de Azure AD está ejecutándose. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y seguir los vínculos al informe de actividad de aprovisionamiento, donde se describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento de Azure AD en G Suite.

Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](../manage-apps/check-status-user-account-provisioning.md).

> [!NOTE]
> Otra opción viable para automatizar el aprovisionamiento de usuarios en G Suite consiste en usar [Google Cloud Directory Sync](https://support.google.com/a/answer/106368?hl=en). Esta opción aprovisiona las identidades de Active Directory locales en G Suite.

## <a name="common-issues"></a>Problemas comunes
* G Suite requiere que todos los usuarios aprovisionados provengan de dominios comprobados. Asegúrese de que cualquier usuario que desee aprovisionar tiene un UPN de un dominio comprobado en G Suite. Si un usuario de un dominio no comprobado está en el ámbito del aprovisionamiento, verá un error en los [registros de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) como "GoogleAppsInvalidDomain". Puede evitar estos errores y asegurarse de que los usuarios de dominios no comprobados están fuera del ámbito mediante el uso de un [filtro de ámbito](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
    * Atributo de destino: userPrincipalName
    * Operador: REGEX MATCH o NOT REGEX MATCH
    * Valor: .*@domain.com

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
