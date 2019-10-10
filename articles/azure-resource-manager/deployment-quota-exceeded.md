---
title: Cuota de implementaciones de Azure superada
description: En este artículo se describe cómo resolver el error de tener más de 800 implementaciones en el historial del grupo de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 755383c9d40c104d50ad9bb7a31b3a00f8348313
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827007"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolución de error cuando el recuento de implementaciones es superior a 800

Cada grupo de recursos está limitado a 800 implementaciones en su historial de implementaciones. En este artículo se describe el error que se recibe cuando se produce un error en una implementación porque se superarían las 800 implementaciones permitidas. Para resolver este error, elimine implementaciones del historial del grupo de recursos. La eliminación de una implementación del historial no afecta a ninguno de los recursos implementados.

## <a name="symptom"></a>Síntoma

Durante una implementación, se recibe un error que indica que la implementación actual superará la cuota de 800 implementaciones.

## <a name="solution"></a>Solución

### <a name="azure-cli"></a>CLI de Azure

Utilice el comando [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) para eliminar implementaciones del historial:

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Puede obtener el recuento actual del historial de implementaciones con el siguiente comando:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilice el comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) para eliminar implementaciones del historial.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Puede obtener el recuento actual del historial de implementaciones con el siguiente comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluciones de terceros

Las siguientes soluciones externas abordan escenarios específicos:

* [Soluciones de Azure Logic Apps y PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extensión AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
