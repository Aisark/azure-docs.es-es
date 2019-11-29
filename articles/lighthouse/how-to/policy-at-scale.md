---
title: Implementación de Azure Policy en suscripciones delegadas a escala
description: Obtenga información sobre cómo la administración de recursos delegados de Azure permite implementar una definición de directivas y una asignación de directivas en varios inquilinos.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3853e8fc163dfc662adc675dd3df1d15958d329a
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463856"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Implementación de Azure Policy en suscripciones delegadas a escala

Como proveedor de servicios, es posible que haya incorporado varios inquilinos de cliente para la administración de recursos delegados de Azure. [Azure Lighthouse](../overview.md) permite a los proveedores de servicios realizar operaciones a escala a través de varios inquilinos a la vez, lo que hace que las tareas de administración sean más eficaces.

En este tema se muestra cómo usar [Azure Policy](https://docs.microsoft.com/azure/governance/policy/) para implementar una definición de directivas y una asignación de directivas en varios inquilinos mediante comandos de PowerShell. En este ejemplo, la definición de directivas garantiza que las cuentas de almacenamiento estén protegidas al permitir solo el tráfico HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Uso de Azure Resource Graph para consultar entre inquilinos de cliente

Puede usar [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/) para realizar consultas en todas las suscripciones de los inquilinos de cliente que administra. En este ejemplo, se identificarán todas las cuentas de almacenamiento de estas suscripciones que no requieran actualmente tráfico HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Implementación de una directiva en varios inquilinos de cliente

En el ejemplo siguiente se muestra cómo usar una [plantilla de Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) para implementar una definición de directivas y una asignación de directivas entre las suscripciones delegadas en varios inquilinos de cliente. Esta definición de directivas requiere que todas las cuentas de almacenamiento usen tráfico HTTPS, lo que impide la creación de nuevas cuentas de almacenamiento que no cumplan con la especificación, y hace que las cuentas de almacenamiento existentes que no tengan esta configuración se marquen como no conformes.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Validación de la implementación de directivas

Una vez que haya implementado la plantilla de Azure Resource Manager, puede confirmar que la definición de directivas se ha aplicado correctamente; para ello, intente crear una cuenta de almacenamiento con **EnableHttpsTrafficOnly** establecido en **false** en una de las suscripciones delegadas. Debido a la asignación de directivas, no debería ser capaz de crear esta cuenta de almacenamiento.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado, elimine la definición y la asignación de directivas que creó la implementación.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
