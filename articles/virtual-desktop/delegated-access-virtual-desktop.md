---
title: 'Acceso delegado en Windows Virtual Desktop (versión preliminar): Azure'
description: Se describe cómo delegar funcionalidades administrativas en una implementación de Windows Virtual Desktop en versión preliminar, y se incluyen ejemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 41cf5f8bcc69e181350a63d215fb0d78d43dcfdf
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272818"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Acceso delegado en Windows Virtual Desktop en versión preliminar

La versión preliminar de Windows Virtual Desktop presenta un modelo de acceso delegado que permite definir la cantidad de acceso que puede tener un usuario determinado mediante la asignación de un rol. Una asignación de roles consta de tres componentes: entidad de seguridad, definición de rol y ámbito. El modelo de acceso delegado de Windows Virtual Desktop se basa en el modelo de Azure RBAC. Para más información sobre asignaciones de roles específicas y sus componentes, consulte la [información general sobre el control de acceso basado en roles de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

El acceso delegado de Windows Virtual Desktop admite los siguientes valores para cada elemento de la asignación de roles:

* Entidad de seguridad
    * Usuarios
    * Entidades de servicio
* Definición de roles
    * Roles integrados
* Ámbito
    * Grupos de inquilinos
    * Inquilinos
    * Grupos de host
    * Grupos de aplicaciones

## <a name="built-in-roles"></a>Roles integrados

El acceso delegado en Windows Virtual Desktop tiene varias definiciones de roles integradas que se pueden asignar a los usuarios y a las entidades de servicio.

* Un propietario de RDS puede administrarlo todo, incluido el acceso a los recursos.
* El colaborador de RDS puede administrarlo todo, excepto el acceso a los recursos.
* Un lector de RDS puede verlo todo, pero no puede realizar cambios.
* Un operador de RDS puede ver las actividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets de PowerShell para asignaciones de roles

Puede ejecutar los siguientes cmdlets para crear, ver y eliminar asignaciones de roles:

* **Get-RdsRoleAssignment** muestra una lista de asignaciones de roles.
* **New-RdsRoleAssignment** crea una asignación de roles.
* **Remove-RdsRoleAssignment** elimina las asignaciones de roles.

### <a name="accepted-parameters"></a>Parámetros aceptados

Puede modificar los tres cmdlets básicos con los siguientes parámetros:

* **AadTenantId**: especifica el identificador del inquilino de Azure Active Directory del que la entidad de servicio es miembro.
* **AppGroupName**: nombre del grupo de aplicaciones de Escritorio remoto.
* **Diagnostics**: indica el ámbito de diagnóstico. (Debe estar emparejado con los parámetros **Infrastructure** o **Tenant**).
* **HostPoolName**: nombre del grupo de host de Escritorio remoto.
* **Infrastructure**: indica el ámbito de la infraestructura.
* **RoleDefinitionName**: nombre del rol de control de acceso basado en roles de los Servicios de Escritorio remoto asignado al usuario, el grupo o la aplicación. (Por ejemplo, propietario de los Servicios de Escritorio remoto, lector de los Servicios de Escritorio remoto, etc.)
* **ServerPrincipleName**: nombre de la aplicación de Azure Active Directory.
* **SignInName**: dirección de correo electrónico del usuario o nombre principal de usuario.
* **TenantName**: nombre del inquilino de Escritorio remoto.

## <a name="next-steps"></a>Pasos siguientes

Para una lista más completa de cmdlets de PowerShell que puede usar cada rol, consulte la [referencia de PowerShell](/powershell/windows-virtual-desktop/overview).

Para instrucciones sobre cómo configurar un entorno de Windows Virtual Desktop, consulte [Entorno de Windows Virtual Desktop en versión preliminar](environment-setup.md).
