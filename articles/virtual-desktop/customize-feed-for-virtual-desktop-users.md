---
title: Personalización de fuente para usuarios de Windows Virtual Desktop - Azure
description: Personalización de la fuente para los usuarios de Windows Virtual Desktop con cmdlets de PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: helohr
ms.openlocfilehash: 40fd863d9ad92b91b8e691fbb7be233e16b82c17
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950746"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalización de fuente para usuarios de Windows Virtual Desktop

Puede personalizar la fuente para que RemoteApp y los recursos de escritorio remoto se muestren de manera reconocible para los usuarios.

En primer lugar y, si aún no lo ha hecho, [descargue e importe el módulo de PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) que se usará en la sesión de PowerShell.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar el nombre para mostrar de RemoteApp

Para cambiar el nombre para mostrar de una instancia de RemoteApp publicada, defina un nombre descriptivo. De forma predeterminada, el nombre descriptivo es el mismo que el nombre del programa RemoteApp.

Para recuperar una lista de instancias de RemoteApp publicadas para un grupo de aplicaciones, ejecute el siguiente cmdlet de PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Captura de pantalla del cmdlet de PowerShell Get-RDSRemoteApp con el nombre y el nombre descriptivo resaltado.](media/get-rdsremoteapp.png)

Para asignar un nombre descriptivo a RemoteApp, ejecute el siguiente cmdlet de PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Captura de pantalla del cmdlet de PowerShell Set-RDSRemoteApp con el nombre y el nuevo nombre descriptivo resaltado.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar el nombre para mostrar de Escritorio remoto

Para cambiar el nombre para mostrar de un escritorio remoto publicado, defina un nombre descriptivo. Si ha creado manualmente un grupo de hosts y un grupo de aplicaciones de escritorio mediante PowerShell, el nombre descriptivo predeterminado es "Session Desktop". Si ha creado un grupo de hosts y un grupo de aplicaciones de escritorio mediante la plantilla de Azure Resource Manager de GitHub o la oferta de Azure Marketplace, el nombre descriptivo predeterminado coincide con el nombre del grupo de hosts.

Para recuperar el recurso de escritorio remoto, ejecute el siguiente cmdlet de PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Captura de pantalla del cmdlet de PowerShell Get-RDSRemoteApp con el nombre y el nombre descriptivo resaltado.](media/get-rdsremotedesktop.png)

Para asignar un nombre descriptivo al recurso de escritorio remoto, ejecute el siguiente cmdlet de PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Captura de pantalla del cmdlet de PowerShell Set-RDSRemoteApp con el nombre y el nuevo nombre descriptivo resaltado.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha personalizado la fuente para los usuarios, puede iniciar sesión en un cliente de Windows Virtual Desktop para probarlo. Para ello, continúe para conectarse a los procedimientos para conectarse a Windows Virtual Desktop:
    
 * [Conexión desde Windows 10 o Windows 7](connect-windows-7-and-10.md)
 * [Conexión desde un explorador web](connect-web.md) 
