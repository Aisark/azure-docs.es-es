---
title: 'Ejemplo de script de Azure PowerShell: obtención de un grupo de recursos administrados y cambio de tamaño de las máquinas virtuales | Microsoft Docs'
description: Proporciona un script de ejemplo de Azure PowerShell que obtiene un grupo de recursos administrado para una aplicación administrada de Azure. El script cambia el tamaño de las máquinas virtuales.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f36ed42e0b7f8e4d1e5b092d2a14fd0cd8178ee0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330118"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obtención de recursos de un grupo de recursos administrados y cambio de tamaño de las máquinas virtuales con PowerShell

Este script recupera los recursos de un grupo de recursos administrados y cambia el tamaño de las máquinas virtuales de ese grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para implementar la aplicación administrada. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Enumera las aplicaciones administradas. Proporcione el nombre del grupo de recursos para enfocar los resultados. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Enumera los recursos. Proporcione un grupo de recursos y el tipo de recurso para enfocar el resultado. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Actualiza el tamaño de una máquina virtual. |


## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](../overview.md).
* Para más información sobre PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
