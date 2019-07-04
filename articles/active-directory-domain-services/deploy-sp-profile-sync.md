---
title: 'Azure Active Directory Domain Services: Habilitar la compatibilidad con el servicio de perfil de usuario de SharePoint | Microsoft Docs'
description: Configuración de dominios administrados de Azure Active Directory Domain Services para admitir la sincronización de perfiles para SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4293052f19ad883c9df7f177456d55c0997072e1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473486"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configuración de un dominio administrado para admitir la sincronización de perfiles para SharePoint Server
SharePoint Server incluye un servicio de perfiles de usuario que se usa para la sincronización de perfiles de usuario. Para configurar el servicio de perfiles de usuario, es necesario conceder los permisos apropiados en un dominio de Active Directory. Para más información, vea [Concesión de permisos de Active Directory Domain Services para la sincronización de perfiles en SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

En este artículo se explica cómo configurar dominios administrados de Azure AD Domain Services para implementar el servicio de sincronización de perfiles de usuario de SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>El grupo “Cuentas de servicio de controlador de dominio de AAD”
Un grupo de seguridad denominado “**Cuentas de servicio de controlador de dominio de AAD**” está disponible en la unidad organizativa “Usuarios” en el dominio administrado. Puede ver este grupo en el complemento MMC **Usuarios y equipos de Active Directory**  del dominio administrado.

![Grupo de seguridad Cuentas de servicio de controlador de dominio de AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

A los miembros de este grupo de seguridad se les delegan los privilegios siguientes:
- El privilegio “Replicación de cambios de directorio” en el atributo DSE raíz del dominio administrado.
- El privilegio “Replicación de cambios de directorio” en el contexto de nomenclatura de configuración (cn = contenedor de configuración) del dominio administrado.

Este grupo de seguridad también es un miembro del grupo integrado **Acceso de compatible con versiones anteriores de Windows 2000**.

![Grupo de seguridad Cuentas de servicio de controlador de dominio de AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Permitir que el dominio administrado admita la sincronización de perfiles de usuario de SharePoint Server
Puede agregar la cuenta de servicio usada para la sincronización de perfiles de usuario de SharePoint para el grupo **Cuentas de servicio de controlador de dominio de AAD**. Como resultado, la cuenta de sincronización obtiene los privilegios adecuados para replicar los cambios en el directorio. Este paso de configuración permite que la sincronización de perfiles de usuario de SharePoint Server funcione correctamente.

![Cuentas de servicio de controlador de dominio de AAD - Agregar miembros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Cuentas de servicio de controlador de dominio de AAD - Agregar miembros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Contenido relacionado
* [Referencia técnica - Concesión de permisos de Active Directory Domain Services para la sincronización de perfiles en SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
