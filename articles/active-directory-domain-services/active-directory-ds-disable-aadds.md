---
title: Deshabilitación de Azure Active Directory Domain Services | Microsoft Docs
description: Deshabilitación de Azure Active Directory Domain Services mediante Azure Portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: ergreenl
ms.openlocfilehash: a2abdbf1409564f94356279332d253627c5b447a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111929"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Deshabilitación de Azure Active Directory Domain Services mediante Azure Portal
En este artículo se muestra cómo usar Azure Portal para deshabilitar Azure Active Directory (AD) Domain Services para el directorio de Azure AD.

> [!WARNING]
> **La eliminación es permanente y no se puede deshacer.**
> Proceda con precaución. Al eliminar el dominio administrado:
>   * Los controladores del dominio administrado se desabastecerán y se quitarán de la red virtual.
>   * Los datos del dominio administrado se eliminarán permanentemente. Esto incluye unidades organizativas personalizadas, GPO, registros DNS personalizados, entidades de servicio, cuentas de servicio administradas de grupo, etc. que se hayan creado en el dominio administrado.
>   * Las máquinas unidas al dominio administrado perderán la relación de confianza con el dominio y deberán separarse de este.
>   * No podrá iniciar sesión en estas máquinas con credenciales corporativas de AD. En su lugar, utilice las credenciales de administrador local para la máquina.
> Al eliminar el dominio administrado no se elimina el directorio de Azure AD, este no se ve afectado en modo alguno.

Realice los pasos siguientes para eliminar el dominio administrado de la instancia de Azure AD Domain Services:
1. Vaya a la [extensión Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) en Azure Portal.
2. Haga clic en el nombre del dominio administrado.

    ![Seleccione el dominio para eliminar](./media/getting-started/domain-services-delete-select-domain.png)

3. En la página **Introducción**, haga clic en el botón **Eliminar**.

    ![Eliminación del dominio](./media/getting-started/domain-services-delete-domain.png)

4. Para confirmar la eliminación, escriba el nombre de dominio DNS del dominio administrado. Haga clic en el botón **Eliminar** cuando termine.

    ![Confirmación de la eliminación del dominio](./media/getting-started/domain-services-delete-domain-confirm.png)

El dominio administrado se eliminará en aproximadamente 15-20 minutos.

Considere la posibilidad de [enviarnos comentarios](active-directory-ds-contact-us.md) para ayudarnos a comprender qué características lo ayudarán a decantarse por Azure AD Domain Services en un futuro. Estos comentarios nos ayudan a mejorar el servicio para que se ajuste mejor a sus necesidades de implementación y casos de uso.
