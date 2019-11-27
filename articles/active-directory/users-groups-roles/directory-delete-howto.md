---
title: Eliminación de un directorio de Azure AD - Azure Active Directory | Microsoft Docs
description: En este tema se explica cómo preparar un directorio de Azure AD para su eliminación, incluidos los directorios de autoservicio.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961813"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminación de un directorio en Azure Active Directory

Cuando se elimina un directorio de Azure AD, también se eliminan todos los recursos que se encuentran en el directorio. Prepare la organización reduciendo sus recursos asociados antes de eliminarla. Solo un administrador global de Azure Active Directory (Azure AD) puede eliminar a un directorio de Azure AD desde el portal.

## <a name="prepare-the-directory"></a>Preparación del directorio

No puede eliminar un directorio de Azure AD hasta que pase varias comprobaciones. Estas comprobaciones reducen el riesgo de que la eliminación de un directorio de Azure AD afecte negativamente a los usuarios, por ejemplo, en la capacidad de iniciar sesión en Office 365 o de tener acceso a los recursos de Azure. Por ejemplo, si se elimina de forma involuntaria un directorio con una suscripción, los usuarios no pueden acceder a los recursos de Azure de esa suscripción. Se comprueban las condiciones siguientes:

* No puede haber ningún usuario en el directorio, excepto un administrador global que eliminará al directorio. Se deben eliminar los otros usuarios antes de poder eliminar el directorio. Si los usuarios se sincronizan localmente, se deberá desactivar primero la sincronización y eliminar los usuarios del directorio en la nube mediante Azure Portal o cmdlets de Azure PowerShell.
* No puede haber aplicaciones en el directorio. Las aplicaciones se deben eliminar antes de poder eliminar el directorio.
* No puede haber proveedores de Multi-Factor Authentication vinculados al directorio.
* No puede haber suscripciones a ningún servicio de Microsoft Online Services, como Microsoft Azure, Office 365 o Azure AD Premium, asociadas al directorio. Por ejemplo, si se ha creado un directorio predeterminado en Azure en su nombre, no puede eliminar este directorio si la suscripción a Azure aún se basa en este directorio para la autenticación. De igual forma, no puede eliminar un directorio si otro usuario le ha asociado una suscripción.

## <a name="delete-the-directory"></a>Eliminación del directorio

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que sea la del administrador global de la organización.

2. Seleccione **Azure Active Directory**.

3. Cambie al directorio que desee eliminar.
  
   ![Confirmación de la organización antes de la eliminación](./media/directory-delete-howto/delete-directory-command.png)

4. Seleccione **Eliminar directorio**.
  
   ![Seleccione el comando para eliminar la organización.](./media/directory-delete-howto/delete-directory-list.png)

5. Si el directorio no supera una o varias comprobaciones, se le proporcionará un vínculo para obtener más información sobre cómo pasar. Una vez que pase todas las comprobaciones, seleccione **Eliminar** para completar el proceso.

## <a name="if-you-cant-delete-the-directory"></a>Imposibilidad de eliminación del directorio

Al configurar el directorio de Azure AD, es posible que también activara suscripciones basadas en licencias para su organización, como Azure AD Premium P2, Office 365 Business Premium o Enterprise Mobility + Security E5. Para evitar la pérdida accidental de datos, no puede eliminar un directorio hasta que se eliminen las suscripciones por completo. Las suscripciones deben estar en estado **Deprovisioned** (Desaprovisionado) para permitir la eliminación del directorio. Una suscripción en estado **Expirado** o **Cancelado** se pasa al estado **Deshabilitado** y la fase final es el estado **Desaprovisionado**.

Para saber qué se puede esperar cuando una suscripción de Office 365 de prueba expira (exceptuando el caso de licencias por volumen, el contrato para empresas o de CSP o asociados de pago), consulte la tabla siguiente. Para obtener más información sobre la retención de datos de Office 365 y el ciclo de vida de la suscripción, consulte [¿Qué pasa con mis datos y mi acceso cuando termina mi suscripción de Office 365 para empresas?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado de la suscripción | Datos | Acceso a datos
----- | ----- | -----
Activo (30 días para evaluación) | Datos accesibles a todos | Los usuarios tienen acceso normal a los archivos o aplicaciones de Office 365<br>Los administradores tienen acceso normal al centro de administración y los recursos de Microsoft 365 
Expirado (30 días) | Datos accesibles a todos| Los usuarios tienen acceso normal a los archivos o aplicaciones de Office 365<br>Los administradores tienen acceso normal al centro de administración y los recursos de Microsoft 365
Deshabilitado (30 días) | Datos accesibles solo para administradores | Los usuarios no pueden obtener acceso a archivos o aplicaciones de Office 365<br>Los administradores pueden acceder al centro de administración de Microsoft 365, pero no pueden asignar licencias a los usuarios ni actualizarlos
Desaprovisionado (30 días tras la deshabilitación) | Los datos se eliminan (automáticamente si no hay otros servicios en uso) | Los usuarios no pueden obtener acceso a archivos o aplicaciones de Office 365<br>Los administradores pueden tener acceso al centro de administración de Microsoft 365 para adquirir y administrar otras suscripciones

## <a name="delete-a-subscription"></a>Eliminación de una suscripción

Puede colocar una suscripción en un estado **Desaprovisionado** para que se elimine a los 3 días mediante el centro de administración de Microsoft 365.

1. Inicie sesión en el [Centro de administración de Microsoft 365](https://admin.microsoft.com) con una cuenta que sea la del administrador global de la organización. Si está intentando eliminar el directorio "Contoso" que tiene el dominio predeterminado inicial "contoso.onmicrosoft.com", inicie sesión con un UPN como admin@contoso.onmicrosoft.com.

2. Para obtener una vista previa del nuevo centro de administración de Microsoft 365, asegúrese de que el botón de alternancia **Try the new admin center** (Probar el nuevo centro de administración) está habilitado.

   ![Vista previa de la nueva experiencia del centro de administración de M365](./media/directory-delete-howto/preview-toggle.png)

3. Una vez habilitado el nuevo centro de administración, debe cancelar una suscripción para poder eliminarla. Seleccione **Billing** (Facturación) y **Products & services** (Productos y servicios), luego, seleccione **Cancel subscription** (Cancelar suscripción) en la suscripción que quiere cancelar. Se le dirigirá a una página de comentarios.

   ![Elegir una suscripción para cancelar](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Complete el formulario de comentarios y seleccione **Cancel suscripción**  (Cancelar suscripción) para cancelar la suscripción.

   ![Comando de cancelación en la vista previa de la suscripción](./media/directory-delete-howto/cancel-command.png)

5. Ahora puede eliminar la suscripción. Seleccione **Delete** (Eliminar) en la suscripción que quiere eliminar. Si no encuentra la suscripción en la página **Products & services** (Productos y servicios), asegúrese de que el **Estado de la suscripción** está establecido en **All** (Todo).

   ![Eliminación del vínculo para eliminar la suscripción](./media/directory-delete-howto/delete-command.png)

6. Seleccione **Delete subscription** (Eliminar suscripción) para eliminar la suscripción y acepte los términos y condiciones. Todos los datos se eliminarán permanentemente a los tres días. Puede [reactivar la suscripción](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante el período de tres días, si cambia de opinión.
  
   ![Lea detenidamente los términos y condiciones.](./media/directory-delete-howto/delete-terms.png)

7. Ahora que ha cambiado el estado de la suscripción, esta se marca para eliminarla. La suscripción entra en el estado **Desaprovisionado** 72 horas más tarde.

8. Una vez que haya eliminado una suscripción en el directorio y hayan transcurrido 72 horas, puede iniciar sesión de nuevo en el centro de administración de Azure AD y no debería requerirse ninguna acción ni haber ninguna suscripción que bloquee la eliminación de directorios. Debe ser capaz de eliminar correctamente el directorio de Azure AD.
  
   ![pasar la comprobación de suscripción en la pantalla de eliminación](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Suscripción de prueba que bloquea la eliminación

Hay [productos de registro de autoservicio](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como Microsoft Power BI, Rights Management Services, Microsoft Power Apps o Dynamics 365 en los que los usuarios individuales se pueden registrar mediante Office 365, que también crea un usuario invitado para la autenticación en su directorio de Azure AD. Para evitar la pérdida de datos, estos productos autoservicio bloquean las eliminaciones del directorio hasta que se eliminen por completo desde el directorio. Solo el administrador de Azure AD puede eliminarlos, si el usuario se ha registrado individualmente o se le asignó el producto.

Hay dos tipos de productos de registro de autoservicio en función de cómo se asignen: 

* Asignación de nivel de organización: Un administrador de Azure AD asigna el producto a toda la organización y el usuario puede estar usando activamente el servicio con esta asignación de nivel de organización incluso si no tiene licencia individual.
* Asignación de nivel de usuario: Básicamente, un usuario individual se asigna a sí mismo el producto sin un administrador durante el registro de autoservicio. Una vez que la organización esté gestionada por un administrador (consulte [Adquisición de administrador de un directorio no administrado](domains-admin-takeover.md)), el administrador podrá asignar directamente el producto a los usuarios sin registro de autoservicio.  

Cuando inicie la eliminación del producto de suscripción de autoservicio, la acción eliminará permanentemente los datos y quitará todos los accesos de usuario al servicio. Los usuarios a los que se asignó la oferta individualmente o en el nivel de organización, no podrán iniciar sesión u obtener acceso a los datos existentes. Si desea evitar la pérdida de datos con un producto de suscripción de autoservicio como [paneles de Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) o [configuración de directivas de Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), asegúrese de que se realiza una copia de seguridad de los datos y que se guarda en otro lugar.

Para obtener más información sobre los productos y servicios de registro de autoservicio disponibles, consulte [Programas de autoservicio disponibles](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para saber qué se puede esperar cuando una suscripción de Office 365 de prueba expira (exceptuando el caso de licencias por volumen, el contrato para empresas o de CSP o asociados de pago), consulte la tabla siguiente. Para obtener más información sobre la retención de datos de Office 365 y el ciclo de vida de la suscripción, consulte [¿Qué pasa con mis datos y mi acceso cuando termina mi suscripción de Office 365 para empresas?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado del producto | Datos | Acceso a datos
------------- | ---- | --------------
Activo (30 días para evaluación) | Datos accesibles a todos | Los usuarios disponen de acceso normal a aplicaciones, archivos y productos de registro de autoservicio.<br>Los administradores tienen acceso normal al centro de administración y los recursos de Microsoft 365
Deleted | Datos eliminados | Los usuarios no disponen de acceso a aplicaciones, archivos y productos de registro de autoservicio.<br>Los administradores pueden tener acceso al centro de administración de Microsoft 365 para adquirir y administrar otras suscripciones

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>¿Cómo puedo eliminar un producto de autorregistro en Azure Portal?

Puede establecer el estado de un producto de suscripción de autoservicio como Microsoft Power BI o Azure Rights Management Services en **Eliminar** para que se eliminen inmediatamente en el portal de Azure AD.

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con una cuenta que sea un administrador global de la organización. Si está intentando eliminar el directorio "Contoso" que tiene el dominio predeterminado inicial "contoso.onmicrosoft.com", inicie sesión con un UPN como admin@contoso.onmicrosoft.com.

2. Seleccione **Licencias** y, a continuación, seleccione **Productos de registro de autoservicio**. Puede ver todos los productos de autorregistro por separado desde las suscripciones basadas en puestos. Elija el producto que desee eliminar de forma permanente. Este es un ejemplo en Microsoft Power BI:

    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/directory-delete-howto/licenses-page.png)

3. Seleccione **Eliminar** para eliminar el producto y aceptar los términos en los que los datos se eliminarán de forma inmediata e irrevocable. Esta acción de eliminación quitará todos los usuarios y eliminará el acceso de la organización al producto. Haga clic en Sí para continuar con la eliminación.  

    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/directory-delete-howto/delete-product.png)

4. Si selecciona **Sí**, se iniciará la eliminación del producto de autoservicio. Aparecerá una notificación que le informará de que la eliminación está en curso.  

    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/directory-delete-howto/progress-message.png)

5. Ahora el estado del producto de autorregistro ha cambiado a **Eliminado**. Cuando actualice la página, el producto debe desaparecer de la página **Productos de autorregistro**.  

    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/directory-delete-howto/product-deleted.png)

6. Una vez que haya eliminado todos los productos, puede iniciar sesión de nuevo en el centro de administración de Azure AD y no debería requerirse ninguna acción ni haber ningún producto que bloquee la eliminación de directorios. Debe ser capaz de eliminar correctamente el directorio de Azure AD.

    ![el nombre de usuario se ha escrito incorrectamente o no se ha encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Pasos siguientes

[Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
