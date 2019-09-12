---
title: 'Creación de una revisión de acceso de roles de recursos de Azure en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a crear una revisión de acceso para los roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba674c143cd8d85b9764c8ade776b52dd1d3598d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804143"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Creación de una revisión de acceso de los roles de recursos de Azure en PIM

El acceso a los roles de recursos de Azure con privilegios para los empleados cambia con el tiempo. Para reducir el riesgo asociado con las asignaciones de roles obsoletas, debe revisar el acceso periódicamente. Puede usar Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para crear revisiones de acceso para roles de recursos de Azure con privilegios. También puede configurar revisiones de acceso periódicas que se produzcan automáticamente.

En este artículo se describe cómo crear una o varias revisiones de acceso para los roles de recursos de Azure con privilegios.

## <a name="prerequisites"></a>Requisitos previos

- [Administrador de roles con privilegios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Abrir las revisiones de acceso

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que sea miembro del rol Administrador de roles con privilegios.

1. Abra **Azure AD Privileged Identity Management**.

1. En el menú izquierdo, haga clic en **Recursos de Azure**.

1. Haga clic en el recurso que quiera administrar, por ejemplo, una suscripción o un grupo de administración.

1. En Administrar, haga clic en **Revisiones de acceso**.

    ![Recursos de Azure: lista de revisiones de acceso que muestra el estado de todas las revisiones](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Inicio de la revisión de acceso

Una vez que haya especificado la configuración para una revisión de acceso, haga clic en **Iniciar**. La revisión de acceso aparecerá en la lista con un indicador de su estado.

![Lista de revisiones de acceso que muestra el estado de la revisión iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso a los roles de recursos de Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Administración de la revisión de acceso

En la página **Información general** de la revisión de acceso, puede seguir el progreso de los revisores a medida que completan las revisiones. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](pim-resource-roles-complete-access-review.md).

![Página de información general de revisiones de acceso que muestra los detalles de la revisión](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Si se trata de una revisión puntual, una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos sobre [cómo realizar una revisión de acceso de los roles de recursos de Azure](pim-resource-roles-complete-access-review.md) para ver los resultados y aplicarlos.  

Para administrar una serie de revisiones de acceso, vaya a la revisión de acceso y verá los próximos eventos en Revisiones programadas; ahí podrá editar la fecha de finalización o agregar o quitar revisores según corresponda.

Según las selecciones de la **Configuración de finalización**, la aplicación automática se ejecutará después de la fecha de finalización de la revisión o cuando se detenga manualmente la revisión. El estado de la revisión cambiará de **Completado** a estados intermedios como **Aplicando** y, por último, a **Aplicado**. Debería ver que los usuarios denegados, si es que los hay, se eliminan de los roles en unos minutos.

## <a name="next-steps"></a>Pasos siguientes

- [Revisar el acceso a los roles de recursos de Azure](pim-resource-roles-perform-access-review.md)
- [Revisión de acceso de roles de recursos de Azure](pim-resource-roles-complete-access-review.md)
- [Creación de una revisión de acceso de roles de Azure AD](pim-how-to-start-security-review.md)
