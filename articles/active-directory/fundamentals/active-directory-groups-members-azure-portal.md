---
title: 'Incorporación o eliminación de los miembros de un grupo: Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo agregar o quitar miembros de un grupo con Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dafdf4c67e8d3d74109b3879fb0deacd79b1774
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60249075"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Incorporación o eliminación de los miembros de un grupo mediante Azure Active Directory
Con Azure Active Directory, puede seguir agregando y quitando miembros del grupo.

## <a name="to-add-group-members"></a>Para agregar miembros al grupo

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta de administrador global del directorio.

2. Seleccione **Azure Active Directory** y después seleccione **Grupos**.

3. En la página **Grupos - Todos los grupos**, busque y seleccione el grupo al que quiere agregar el miembro. En este caso, use nuestro grupo creado anteriormente, **MDM policy - West**.

    ![Página Grupos - Todos los grupos, con el nombre del grupo resaltado](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. En la página **Información general de MDM policy - West**, seleccione **Miembros** en el área **Administrar**.

    ![Página de información general de MDM policy - West, con la opción Miembros resaltada](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Seleccione **Agregar miembros** y, a continuación, busque y seleccione a cada uno de los miembros que desea agregar al grupo y elija **Seleccionar**.

    Obtendrá un mensaje que indica que los miembros se han agregado correctamente.

    ![Página Agregar miembros, se muestra el miembro que se ha buscado](media/active-directory-groups-members-azure-portal/update-members.png)

6. Actualice la pantalla para ver el nombre de todos los miembros agregados al grupo.

## <a name="to-remove-group-members"></a>Para eliminar miembros del grupo

1. En la página **Grupos - Todos los grupos**, busque y seleccione el grupo al que quiere quitar un miembro. De nuevo, usaremos como ejemplo a **MDM policy - West**.

2. Seleccione **Miembros** desde el área **Administrar**, busque y seleccione el nombre del miembro que desea quitar y, a continuación, seleccione **Quitar**.

    ![Página de información de miembro, con la opción Quitar](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Pasos siguientes

- [Visualización de grupos y miembros](active-directory-groups-view-azure-portal.md)

- [Editar la configuración del grupo](active-directory-groups-settings-azure-portal.md)

- [Administrar el acceso a los recursos mediante grupos](active-directory-manage-groups.md)

- [Administrar reglas dinámicas de los usuarios de un grupo](../users-groups-roles/groups-create-rule.md)

- [Asociar o agregar una suscripción de Azure a Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
