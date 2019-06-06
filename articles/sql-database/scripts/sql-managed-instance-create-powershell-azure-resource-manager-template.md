---
title: Ejemplo de PowerShell para crear una instancia administrada en Azure SQL Database | Microsoft Docs
description: Script de ejemplo de Azure PowerShell para crear una instancia administrada en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2d5ecb3035aad657485916a4c472f6f4dc9eb530
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729155"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Uso de PowerShell junto con una plantilla de Azure Resource Manager para crear una instancia administrada en Azure SQL Database

La instancia administrada de Azure SQL Database se puede crear mediante una biblioteca de Azure PowerShell y plantillas de Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar PowerShell de manera local, en este tutorial se requiere la versión 1.4.0 de AZ PowerShell o posterior. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

Los comandos de Azure PowerShell pueden iniciar la implementación mediante la plantilla de Azure Resource Manager predefinida. Se pueden especificar las siguientes propiedades en la plantilla:

- Nombre de instancia
- Nombre de usuario y contraseña de administrador SQL.
- Tamaño de la instancia de (número de núcleos y tamaño de almacenamiento máximo).
- Red virtual y subred en la que se colocará la instancia.
- Intercalación en el nivel de servidor de la instancia (versión preliminar).

El nombre de la instancia, el nombre de usuario del administrador SQL, la red virtual o la subred, y la intercalación no se podrán modificar posteriormente. Se podrán cambiar otras propiedades de la instancia.

## <a name="prerequisites"></a>Requisitos previos

En este ejemplo se supone que ha [creado un entorno de red válido](../sql-database-managed-instance-create-vnet-subnet.md) o que ha [modificado la red virtual existente](../sql-database-managed-instance-configure-vnet-subnet.md) para la Instancia administrada. El ejemplo usa los cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) y [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork), así que asegúrese de que ha instalado los módulos de PowerShell siguientes:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure

El contenido siguiente debería situarse en un archivo que representa una plantilla que se usará para crear la instancia:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Se supone que ya existe una red virtual de Azure con la subred configurada correctamente. Si no tiene una subred configurada correctamente, prepare el entorno de red mediante una [plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) aparte que se pueda ejecutar de manera independiente o que ya esté incluida en esta plantilla.

Guarde el contenido de este archivo como un archivo .json, ponga la ruta de acceso del archivo en el siguiente script de PowerShell y cambie los nombres de los objetos del script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Después de ejecutar el script correctamente, SQL Database es accesible desde todos los servicios de Azure y la dirección IP configurada.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de PowerShell de SQL Database en los [scripts de PowerShell de Azure SQL Database](../sql-database-powershell-samples.md).
