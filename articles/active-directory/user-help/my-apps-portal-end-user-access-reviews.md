---
title: 'Administración del acceso de su organización a aplicaciones y grupos: Azure AD'
description: Obtenga información sobre cómo realizar una revisión de acceso para administrar el acceso de seguridad para las aplicaciones y grupos de su organización desde el portal Aplicaciones.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062392"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Realización de una revisión de acceso desde el portal Aplicaciones

Puede usar su cuenta profesional o educativa con el portal **Aplicaciones** basado en web para realizar revisiones de acceso para sus aplicaciones y grupos. Las revisiones de acceso le ayudan a administrar el acceso obsoleto o a cambiar los requisitos de acceso y a asegurarse de que se revisan y actualizan.

Si no tiene acceso al portal **Aplicaciones**, póngase en contacto con su departamento de soporte técnico para obtener permiso.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este contenido está pensado para usuarios de **Aplicaciones**. Si es administrador, puede encontrar más información acerca de cómo configurar y administrar aplicaciones basadas en la nube en la [Documentación sobre la administración de aplicaciones](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Administración de revisiones del acceso

Si el administrador le ha dado permiso para realizar sus propias revisiones de acceso, puede administrar el acceso de grupos o aplicaciones desde el icono **Revisiones de acceso** de la página del portal **Aplicaciones**.

>[!Note]
>Si no ve el icono **Revisiones de acceso** significa que no tiene permiso para realizar las revisiones de acceso o que no tiene ninguna revisión pendiente que esté esperando su aprobación. Si cree que debería tener acceso al icono, póngase en contacto con el departamento de soporte técnico para obtener ayuda.

## <a name="to-perform-your-access-reviews"></a>Para realizar sus revisiones de acceso

1. Inicie sesión en su cuenta profesional o educativa.

2. Abra el explorador web y vaya a https://myapps.microsoft.com o use el vínculo proporcionado por su organización. Por ejemplo, quizá se le redirija a una página personalizada para la organización, como https://myapps.microsoft.com/contoso.com.

    Aparecerá la página **Aplicaciones**, que muestra todas las aplicaciones basadas en la nube disponibles para su uso y propiedad de su organización.

    ![Página de aplicaciones en el portal Mis aplicaciones](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Seleccione el icono **Revisiones de acceso** para ver una lista de revisiones de acceso que esperan su aprobación.

    ![Página Revisiones de acceso con revisiones de acceso pendientes para la organización](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Seleccione **Comenzar revisión** para iniciar la revisión de acceso.

5. Revise el acceso y determine si sigue siendo necesario.

    ![Página Revisión de acceso que muestra los detalles de revisión](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Si es administrador y tiene permiso para revisar el acceso de su organización a grupos y aplicaciones, verá una página diferente. Para más información sobre la revisión de grupos o aplicaciones para su organización, consulte [Revisión del acceso a grupos o aplicaciones en las revisiones de acceso de Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Seleccione **Sí** para mantener el acceso o **No** para quitarlo.

    Si selecciona **Sí**, es posible que necesite especificar una justificación en el cuadro **Motivo**.

    ![Página Revisión de acceso que se muestra el cuadro Motivo con texto de ejemplo](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Seleccione **Submit** (Enviar).

    La revisión de acceso se ha completado y vuelve al portal **Aplicaciones**.

    >[!Note]
    >Puede cambiar el acceso en cualquier momento hasta que el período de revisión de acceso termine. Si quita el acceso a una aplicación o un grupo, no se quita inmediatamente. La eliminación se produce cuando el período de revisión de acceso termina o cuando un administrador cierra la revisión.

## <a name="next-steps"></a>Pasos siguientes

- [Acceder y usar las aplicaciones del portal Aplicaciones](my-apps-portal-end-user-access.md)
- [Cambiar la información de perfil](my-apps-portal-end-user-update-profile.md)
- [Ver y actualizar la información relacionada con grupos](my-apps-portal-end-user-groups.md)
