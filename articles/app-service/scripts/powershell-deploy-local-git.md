---
title: 'Ejemplo de script de Azure PowerShell: Creación de una aplicación e implementación desde el repositorio local de Git | Microsoft Docs'
description: 'Ejemplo de script de Azure PowerShell: creación de una aplicación web e implementación de código desde un repositorio local de Git'
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 670660c3618124339c6d6ab416aa9baa4d5ca2ca
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098336"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Creación de una aplicación web e implementación de código desde un repositorio local de Git

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, implementa el código de la aplicación web desde un repositorio local de Git.

Si es necesario, actualice a la versión más reciente de Azure PowerShell con la ayuda de la [Guía de Azure PowerShell](/powershell/azure/overview) y, luego, ejecute `Connect-AzAccount` para crear una conexión con Azure. Además, el código de aplicación debe confirmarse en un repositorio de Git local.

## <a name="sample-script"></a>Script de ejemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación web y todos los recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea una aplicación web con el grupo de recursos y el grupo de App Service necesarios. Cuando el directorio actual contenga un repositorio Git, agregue un `azure` remoto. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de Azure PowerShell para Azure App Service Web Apps en los [ejemplos de PowerShell](../samples-powershell.md).
