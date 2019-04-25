---
title: Configuración de DNS para acceder a un dominio administrado mediante LDAPS a través de Internet | Microsoft Docs
description: Configuración de DNS para acceder a un dominio administrado de Azure AD Domain Services mediante LDAPS a través de Internet
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 122282d168246e34aaa4a6369f7433b167355887
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416745"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Configuración de DNS para acceder a un dominio administrado de Azure AD Domain Services mediante LDAP seguro (LDAPS)

## <a name="before-you-begin"></a>Antes de empezar
Complete [Tarea 3: Habilitación de LDAP seguro para el dominio administrado mediante Azure Portal](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarea 4: Configuración de DNS para obtener acceso al dominio administrado desde Internet
> [!TIP]
> **Tarea opcional** : omita esta tarea de configuración si no piensa acceder al dominio administrado con LDAPS a través de Internet.
>
>

Antes de comenzar esta tarea, complete los pasos que se describen en la [tarea 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Después de habilitar el acceso LDAP seguro a través de Internet, debe actualizar el DNS para que los equipos cliente puedan encontrar este dominio administrado. Verá una dirección IP externa en la pestaña **Propiedades** en **DIRECCIÓN IP EXTERNA PARA EL ACCESO LDAPS**.

Configure el proveedor de DNS externo para que el nombre DNS del dominio administrado (p. ej., ldaps.contoso100.com) señale a esta dirección IP externa. En este ejemplo, cree la entrada DNS siguiente:

    ldaps.contoso100.com  -> 52.165.38.113

Eso es todo. Ya está listo para conectarse al dominio administrado mediante LDAP seguro a través de Internet.

> [!WARNING]
> Recuerde que los equipos cliente deben confiar en el emisor del certificado LDAPS para poder conectarse correctamente al dominio administrado mediante LDAPS. Si usa una entidad de certificación de confianza pública, no tendrá que realizar ninguna acción, ya que los equipos cliente confiarán en los emisores de certificados. Si utiliza un certificado autofirmado, instale la parte pública del certificado autofirmado en el almacén de certificados de confianza del equipo cliente.
>
>

## <a name="next-step"></a>Paso siguiente
[Tarea 5: Vinculación al dominio administrado y bloqueo del acceso mediante LDAP seguro](active-directory-ds-ldaps-bind-lockdown.md)
