---
title: 'PowerShell: Eliminación de una copia de seguridad de aplicación'
description: Aprenda a usar Azure PowerShell para automatizar la implementación y administración de App Service. En este ejemplo se muestra cómo eliminar una copia de seguridad de una aplicación.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b8d3175b4266e132d94f6f2dde163cb60fb11396
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685795"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Eliminación de una copia de seguridad de una Web mediante Azure PowerShell

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, crea una copia de seguridad única para ella. 

Para ejecutar este script, necesita una copia de seguridad existente de una aplicación web. Para crearla, consulte [Copia de seguridad de una aplicación web](powershell-backup-onetime.md) o [Create a scheduled backup for a web app](powershell-backup-scheduled.md) (Creación de una copia de seguridad programada de una aplicación web).

## <a name="sample-script"></a>Script de ejemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación web y todos los recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtiene una lista de copias de seguridad de una aplicación web. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Quita la copia de seguridad especificada de una aplicación web. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de Azure PowerShell para Azure App Service Web Apps en los [ejemplos de PowerShell](../samples-powershell.md).
