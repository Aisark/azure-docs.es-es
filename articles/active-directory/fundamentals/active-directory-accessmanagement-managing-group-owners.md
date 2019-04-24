---
title: Adición o eliminación de propietarios de grupos en Azure Active Directory | Microsoft Docs
description: Obtenga información acerca de cómo agregar o quitar propietarios del grupo con Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2541a1d76b56f92b250fb422951769db7877213e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249884"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adición o eliminación de propietarios del grupo en Azure Active Directory
Los grupos de Azure Active Directory (Azure AD) pertenecen a propietarios del grupo, quienes también lo administran. El propietario de un recurso (administrador) asigna propietarios del grupo para que administren un grupo y sus miembros. Los propietarios del grupo no deben ser miembros del grupo. Una vez se ha asignado un propietario del grupo, solo puede agregar o quitar propietarios el propietario de un recurso.

En algunos casos, como administrador, usted podría optar por no asignar a un propietario del grupo. En este caso, usted se convertiría en el propietario del grupo. Además, los propietarios pueden asignar a otros propietarios a su grupo, a menos que haya restringido esta opción en la configuración del grupo.

## <a name="add-an-owner-to-a-group"></a>Adición de un propietario a un grupo
Agregue propietarios del grupo adicionales a un grupo con Azure AD.

### <a name="to-add-a-group-owner"></a>Para agregar un propietario del grupo
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador global para el directorio.

2. Seleccione **Azure Active Directory**, **Grupos** y, a continuación, el grupo para el que quiera agregar un propietario (en este ejemplo, *MDM policy - West* (Directiva MDM - Oeste)).

3. En la página **MDM policy - West Overview** (Información general de la directiva de MDM - Oeste), seleccione **Propietarios**.

    ![Página MDM policy - West Overview (Información general de la directiva MDM - Oeste), con la opción Propietarios resaltada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. En la página **MDM policy - West - Owners** (Directiva MDM - Oeste - Propietarios), seleccione **Agregar propietarios**, busque y seleccione el usuario que será el nuevo propietario del grupo y, a continuación, elija **Seleccionar**.

    ![Página MDM policy - West - Owners (Directiva MDM - Oeste - Propietarios), con la opción Propietarios resaltada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Después de seleccionar el nuevo propietario, puede actualizar la página **Propietarios** y ver el nombre agregado a la lista de propietarios.

## <a name="remove-an-owner-from-a-group"></a>Eliminación de un propietario de un grupo
Elimine un propietario de un grupo mediante Azure AD.

### <a name="to-remove-an-owner"></a>Para quitar un propietario
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador global para el directorio.

2. Seleccione **Azure Active Directory**, **Grupos** y a continuación el grupo en el que quiera eliminar un propietario (en este ejemplo, *MDM policy - West*).

3. En la página **MDM policy - West Overview** (Información general de la directiva de MDM - Oeste), seleccione **Propietarios**.

    ![Página MDM policy - West Overview (Información general de la directiva MDM - Oeste), con la opción Propietarios resaltada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. En la página **MDM policy - West - Owners** (Directiva MDM - Oeste - Propietarios), seleccione el usuario que quiera quitar como propietario del grupo, elija **Quitar** en la página de información del usuario y seleccione **Sí** para confirmar su decisión.

    ![Página de información del usuario con la opción Quitar resaltada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Después de quitar el propietario, puede volver a la página **Propietarios** y ver que se ha quitado el nombre de la lista de propietarios.

## <a name="next-steps"></a>Pasos siguientes
- [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets de Azure Active Directory para configurar las opciones de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Uso de grupos para asignar acceso a una aplicación SaaS integrada](../users-groups-roles/groups-saasapps.md)

- [Integración de las identidades locales con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets de Azure Active Directory para configurar las opciones de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)
