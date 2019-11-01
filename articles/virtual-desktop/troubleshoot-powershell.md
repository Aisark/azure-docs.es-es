---
title: 'PowerShell para Windows Virtual Desktop: Azure'
description: Cómo solucionar problemas de PowerShell al configurar un entorno de inquilinos de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 38d9a2dda945f3a9459aa8e3360012c6ef422608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163304"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell para Windows Virtual Desktop

Use este artículo para resolver los problemas y errores al usar PowerShell con Windows Virtual Desktop. Para más información sobre PowerShell para Servicios de Escritorio remoto, consulte [PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Envío de comentarios

Visite la [Comunidad técnica de Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para hablar sobre Windows Virtual Desktop con el equipo de producto y los miembros activos de la comunidad.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos de PowerShell usados durante la configuración de Windows Virtual Desktop

En esta sección se enumeran los comandos de PowerShell que se usan normalmente durante la configuración de Windows Virtual Desktop, y proporciona maneras de resolver los problemas que pueden surgir al usarlos.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Error: Comando Add-RdsAppGroupUser: The specified UserPrincipalName is already assigned to a RemoteApp app group in the specified Host Pool (El UserPrincipalName especificado ya está asignado a un grupo de aplicaciones de RemoteApp en el grupo host especificado)

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** El nombre de usuario usado ya se ha asignado a un grupo de aplicaciones de un tipo diferente. Los usuarios no pueden asignarse a un grupo de aplicaciones tanto remoto como de escritorio en el mismo grupo host de sesión.

**Corrección:** Si el usuario necesita tanto aplicaciones remotas como escritorio remoto, cree grupos host diferentes o conceda al usuario acceso al escritorio remoto, lo que permitirá el uso de cualquier aplicación en la VM del host de sesión.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Error: Comando Add-RdsAppGroupUser: The specified UserPrincipalName doesn't exist in the Azure Active Directory associated with the Remote Desktop tenant (El UserPrincipalName especificado no existe en la instancia de Azure Active Directory asociada con el inquilino de Escritorio remoto)

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Causa:** El usuario especificado por el valor de UserPrincipalName no se encuentra en la instancia de Azure Active Directory vinculada al inquilino de Windows Virtual Desktop.

**Corrección:** Confirme los elementos de la lista siguiente.

- El usuario está sincronizado con Azure Active Directory.
- El usuario no está vinculado a comercio de negocio a consumidor (B2C) ni de negocio a negocio (B2B).
- El inquilino de Windows Virtual Desktop está vinculado a la instancia correcta de Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities: User isn't authorized to query the management service (El usuario no está autorizado para consultar el servicio de administración)

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** Parámetro TenantName

**Solución:** Emita Get-RdsDiagnosticActivities con -TenantName \<TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities: User isn't authorized to query the management service (El usuario no está autorizado para consultar el servicio de administración)

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Uso del conmutador -Deployment.

**Corrección:** Solo los administradores de implementación pueden usar el conmutador Deployment. Estos administradores suelen ser miembros del equipo de Servicios de Escritorio remoto/Windows Virtual Desktop. Reemplace el conmutador -Deployment por -TenantName \<TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Error: New-RdsRoleAssignment: The user isn't authorized to query the management service (El usuario no está autorizado para consultar el servicio de administración)

**Causa 1:** La cuenta usada no tiene permisos de propietario de Servicios de Escritorio remoto en el inquilino.

**Corrección 1:** Un usuario con permisos de propietario de Servicios de Escritorio remoto debe ejecutar la asignación de roles.

**Causa 2:** La cuenta usada tiene permisos de propietario de Servicios de Escritorio remoto, pero no forma parte de la instancia de Azure Active Directory del inquilino o no tiene permisos para consultas en Azure Active Directory donde se encuentra el usuario.

**Corrección 2:** Un usuario con permisos de Active Directory debe ejecutar la asignación de roles.

>[!Note]
>New-RdsRoleAssignment no puede conceder permisos a un usuario que no existe en Azure Active Directory (AD).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre cómo solucionar problemas de Windows Virtual Desktop y las pistas de escalación, consulte [Introducción, comentarios y soporte técnico para solucionar problemas](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante la creación de un grupo de inquilinos y de hosts en un entorno de Windows Virtual Desktop, consulte [Creación de los grupos de inquilinos y de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas al configurar una máquina virtual (VM) en Windows Virtual Desktop, consulte [Configuración de la máquina virtual del host de sesión](troubleshoot-vm-configuration.md).
- Para solucionar problemas con conexiones de cliente de Windows Virtual Desktop, consulte [Conexiones de cliente de Escritorio remoto](troubleshoot-client-connection.md).
- Para más información sobre el servicio, consulte [Entorno de Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para realizar un tutorial de solución de problemas, consulte [Tutorial: Solución de problemas de las implementaciones de plantillas de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
