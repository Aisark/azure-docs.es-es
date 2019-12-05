---
title: 'Visualización de la entidad de servicio de una identidad administrada en Azure Portal: Azure AD'
description: Instrucciones detalladas para ver la entidad de servicio de una identidad administrada en Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b51aeabaa4bb7fa9884c95cf0da2dc68ba5b64b0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184100"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visualización de la entidad de servicio de una identidad administrada en Azure Portal

Las identidades administradas para los recursos de Azure proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, obtendrá información sobre cómo visualizar la entidad de servicio de una identidad administrada mediante Azure Portal.

 > [!NOTE] 
 > Las entidades de servicio son aplicaciones empresariales. 

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md).
- Si aún no tiene una cuenta de Azure, [regístrese para obtener una cuenta gratuita](https://azure.microsoft.com/free/).
- Habilite la [identidad asignada por el sistema en una máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) o [aplicación](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visualización de la entidad de servicio

Este procedimiento muestra cómo ver la entidad de servicio de una máquina virtual con la identidad asignada por el sistema habilitada (se aplican los mismos pasos para una aplicación).

1. Haga clic en **Azure Active Directory** y, a continuación, haga clic en **Aplicaciones empresariales**.
2. En **Tipo de aplicación**, seleccione **Todas las aplicaciones**.
3. En el cuadro de filtro de búsqueda, escriba el nombre de la máquina virtual o aplicación que tiene habilitada la identidad administrada o elíjala en la lista presentada.

   ![Visualización de entidades de servicio de identidades administradas en Azure Portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Pasos siguientes

[Identidades administradas para recursos de Azure](/azure/active-directory/managed-identities-azure-resources/overview)

