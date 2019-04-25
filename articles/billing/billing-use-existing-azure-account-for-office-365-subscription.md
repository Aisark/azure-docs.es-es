---
title: Suscripción a Office 365 con la cuenta de Azure | Microsoft Docs
description: Obtenga información acerca de cómo crear una suscripción de Office 365 mediante una cuenta de Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: b67f3c590be290515329af390b4d3d79a9746112
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369882"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Suscripción a Office 365 con la cuenta de Azure
Si es suscriptor de Azure, puede usar su cuenta de Azure para suscribirse a Office 365. Si forma parte de una organización que tiene una suscripción de Azure, puede crear una suscripción de Office 365 para los usuarios de su cuenta actual de Azure Active Directory (Azure AD). Regístrese en Office 365 con una cuenta que tenga permisos de administrador global o de administrador de facturación en el inquilino de Azure Active Directory. Para más información, consulte [Comprobación de permisos de cuenta en Azure AD](#RoleInAzureAD) y [Asignación de roles de administrador en Azure Active Directory (Azure AD)](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Si ya tiene una cuenta de Office 365 y una suscripción de Azure, puede realizar la [asociación de un inquilino de Office 365 a una suscripción de Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Obtención de una suscripción a Office 365 con la cuenta de Azure

1. Vaya a la [página de productos de Office 365](https://products.office.com/business) y seleccione un plan.
2. Haga clic en **Iniciar sesión** en la esquina superior derecha de la página.

    ![Captura de pantalla de la página de prueba de Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Inicie sesión con las credenciales de su cuenta de Azure. Si va a crear una suscripción para una organización, use una cuenta de Azure que sea miembro del rol de directorio de administrador global o de administrador de facturación en el inquilino de Azure Active Directory.

    ![Captura de pantalla de inicio de sesión en Office 365](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Haga clic en **Probar ahora**.

    ![Captura de pantalla que confirma su pedido para Office 365](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. En la página de confirmación del pedido, haga clic en **Continuar**.

    ![Captura de pantalla del recibo del pedido de Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Ahora ya está listo.
Si ha creado la suscripción a Office 365 para su organización, siga los pasos siguientes para comprobar que los usuarios de Azure AD aparecen ahora en Office 365.

1. Abra el centro de administración de Microsoft 365.
2. Expanda **USUARIOS** y luego haga clic en **Usuarios activos**.

    ![Captura de pantalla de los usuarios del centro de administración de Microsoft 365](./media/billing-use-existing-azure-account-office-365-subscription/16-microsoft-365-admin-center-users.png)

Una vez completado el registro, la suscripción de Office 365 se agrega a la misma instancia de Azure Active Directory a la que pertenece su suscripción de Azure. Para más información, consulte [Más información acerca de las suscripciones de Azure y Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) y [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Comprobación de los permisos de mi cuenta en Azure AD
1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Haga clic en **Todos los servicios** y, a continuación, busque **Active Directory**.

    ![Captura de pantalla de Active Directory en Azure Portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Haga clic en **Usuarios y grupos** > **Todos los usuarios**.
4. Seleccione el nombre del usuario.

    ![Captura de pantalla que muestra los usuarios de Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Haga clic en **Rol del directorio**.

    ![Captura de pantalla que muestra el rol del directorio de Azure Portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  El rol **Administrador global** o **Administrador limitado** > **Administrador de facturación** es necesario para crear una suscripción a Office 365 para los usuarios de su cuenta actual de Azure Active Directory.

    ![Captura de pantalla que muestra el rol del directorio de administrador de facturación de Azure Portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [crear una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).
