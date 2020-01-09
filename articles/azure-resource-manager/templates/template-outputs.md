---
title: Salidas en plantillas
description: Se describe cómo definir valores de salida en una plantilla de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 7244e1ac0eff973d550a2bae8a70fa5055ca2248
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476198"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Salidas en una plantilla de Azure Resource Manager

En este artículo se describe cómo definir valores de salida en una plantilla de Azure Resource Manager. Las salidas se usan cuando es necesario devolver valores de los recursos implementados.

## <a name="define-output-values"></a>Definición de valores de salida

En el ejemplo siguiente se muestra cómo devolver el identificador de recurso para una dirección IP pública:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Salida condicional

En la sección de salidas, puede devolver un valor condicionalmente. Normalmente, la condición de las salidas se usa cuando se [implementa condicionalmente](conditional-resource-deployment.md) un recurso. En el ejemplo siguiente, se muestra cómo se devuelve condicionalmente el identificador de recurso de una dirección IP pública en función de si se ha implementado una nueva:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obtener un ejemplo sencillo de salida condicional, consulte la [plantilla de salida condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Plantillas vinculadas

Para recuperar el valor de salida de una plantilla vinculada, use la función [reference](template-functions-resource.md#reference) en la plantilla principal. La sintaxis de la plantilla principal es la siguiente:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Al obtener una propiedad de salida a partir de una plantilla vinculada, el nombre de propiedad no puede incluir un guión.

En el ejemplo siguiente, se muestra cómo establecer la dirección IP en un equilibrador de carga mediante la recuperación de un valor desde una plantilla vinculada.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

No se puede usar la función `reference` en la sección de salidas de una [plantilla anidada](linked-templates.md#nested-template). Para devolver los valores de un recurso implementado en una plantilla anidada, convierta la plantilla anidada en una plantilla vinculada.

## <a name="get-output-values"></a>Obtención de valores de salida

Cuando la implementación se realiza correctamente, los valores de salida se devuelven automáticamente en los resultados de la implementación.

Para obtener valores de salida del historial de implementación, puede usar un script.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Plantillas de ejemplo

En los siguientes ejemplos se muestran escenarios para usar salidas.

|Plantilla  |Descripción  |
|---------|---------|
|[Variables de copia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variables complejas y genera esos valores. No implementa ningún recurso. |
|[Dirección IP pública](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea una dirección IP pública y genera el identificador de recurso. |
|[Equilibrador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Crea vínculos a la plantilla anterior. Usa el identificador de recurso de la salida al crear el equilibrador de carga. |

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las propiedades disponibles para salidas, consulte [Información sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](template-syntax.md).
