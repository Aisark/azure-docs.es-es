---
title: Implementación de una plantilla de Azure Resource Manager en un runbook de Azure Automation
description: Procedimiento para implementar una plantilla de Azure Resource Manager almacenada en Azure Storage desde un runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446964"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Implementación de una plantilla de Azure Resource Manager en un runbook de Azure Automation PowerShell

Puede escribir un [runbook de Azure Automation PowerShell](automation-first-runbook-textual-powershell.md) que implemente un recurso de Azure mediante una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-create-first-template.md).

Al hacerlo, puede automatizar la implementación de los recursos de Azure. Puede mantener las plantillas de Resource Manager en una ubicación central segura como Azure Storage.

En este artículo, crearemos un runbook de PowerShell que use una plantilla de Resource Manager almacenada en [Azure Storage](../storage/common/storage-introduction.md) para implementar una nueva cuenta de Azure Storage.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará los siguientes elementos:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o [suscribirse para obtener una cuenta gratuita](https://azure.microsoft.com/free/).
* [Cuenta de Automation](automation-sec-configure-azure-runas-account.md) para contener el Runbook y autenticarse en recursos de Azure.  Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* [Cuenta de Azure Storage](../storage/common/storage-create-storage-account.md) donde se va a almacenar la plantilla de Resource Manager
* Azure Powershell instalado en una máquina local. Consulte [Instalación y configuración de Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) para información sobre cómo obtener Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Creación de la plantilla de Resource Manager

En este ejemplo, utilizamos una plantilla de Resource Manager que implementa una nueva cuenta de Azure Storage.

En un editor de texto, copie el texto siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Guarde el archivo localmente como `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Guarde la plantilla de Resource Manager en Azure Storage.

Ahora, usaremos PowerShell para crear un recurso compartido de archivos de Azure Storage y cargaremos el archivo `TemplateTest.json`.
Para obtener instrucciones sobre cómo crear un recurso compartido de archivos y cargar un archivo en Azure Portal, consulte [Introducción a Azure File Storage en Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Inicie PowerShell en la máquina local y ejecute los comandos siguientes para crear un recurso compartido de archivos y cargar la plantilla de Resource Manager en él.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Creación del script del runbook de PowerShell

Ahora crearemos un script de PowerShell que obtenga el archivo `TemplateTest.json` desde Azure Storage e implemente la plantilla para crear una nueva cuenta de Azure Storage.

En un editor de texto, pegue el texto siguiente:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Guarde el archivo localmente como `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importación y publicación del runbook en su cuenta de Azure Automation

Ahora usaremos PowerShell para importar el runbook en su cuenta de Azure Automation y, a continuación, publicaremos el runbook.
Para información sobre cómo importar y publicar un runbook en Azure Portal, consulte [Administración de runbooks en Azure Automation](manage-runbooks.md).

Para importar `DeployTemplate.ps1` en su cuenta de Automation como un runbook de PowerShell, ejecute los siguientes comandos de PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Inicio del runbook

Ahora iniciaremos el runbook llamando al cmdlet [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook).

Para obtener información acerca de cómo iniciar un runbook en Azure Portal, consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md).

Ejecute los siguientes comandos en la consola de PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Se ejecuta el runbook y puede comprobar su estado ejecutando `$job.Status`.

El runbook obtiene la plantilla de Resource Manager y la utiliza para implementar una nueva cuenta de Azure Storage.
Puede ver que se ha creado la nueva cuenta de almacenamiento ejecutando el siguiente comando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Resumen

Eso es todo. Ahora puede usar las plantillas de Resource Manager, Azure Automation y Azure Storage para implementar todos los recursos de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre las plantillas de Resource Manager, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para empezar a trabajar con Azure Storage, consulte [Introducción a Azure Storage](../storage/common/storage-introduction.md).
* Para encontrar otros runbooks útiles de Azure Automation, consulte [Galerías de módulos y runbooks de Azure Automation](automation-runbook-gallery.md).
* Para encontrar otras plantillas útiles de Resource Manager, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/).


