---
title: Agregar un artefacto a una máquina virtual en Azure DevTest Labs | Microsoft Docs
description: Obtenga información sobre cómo agregar un artefacto a una máquina virtual en un laboratorio en Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047031"
---
# <a name="add-an-artifact-to-a-vm"></a>Agregar un artefacto a una máquina virtual
Al crear una máquina virtual, puede agregar artefactos existentes en él. Estos artefactos pueden ser desde el [repositorio de Git de DevTest Labs público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) o desde su propio repositorio de Git. Este artículo muestra cómo agregar los artefactos en Azure portal y mediante el uso de Azure PowerShell. 

Los *artefactos* de Azure DevTest Labs permiten especificar las *acciones* que se realizan al aprovisionarse la máquina virtual, como, por ejemplo, la ejecución de scripts de Windows PowerShell, la ejecución de comandos Bash y la instalación de software. Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

Para obtener información acerca de cómo crear artefactos personalizados, consulte el artículo: [Creación de artefactos personalizados](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Usar Azure Portal 
1. Inicie sesión en el [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el que contiene la máquina virtual con la que desea trabajar.  
1. Seleccione **My virtual machines** (Mis máquinas virtuales).
1. Seleccione la máquina virtual deseada.
1. Seleccione **Administrar artefactos**. 
1. Seleccione **Apply artifacts** (Aplicar artefactos).
1. En el panel **Aplicar artefactos**, seleccione el artefacto que desea agregar a la máquina virtual.
1. En el panel **Agregar artefacto**, especifique los valores de parámetros necesarios y cualquier otro parámetro opcional que precise.  
1. Seleccione **Agregar** para agregar el artefacto y vuelva al panel **Aplicar artefactos**.
1. Siga agregando los artefactos que necesite para la máquina virtual.
1. Cuando haya agregado los artefactos, puede [cambiar el orden en que se ejecutan](#change-the-order-in-which-artifacts-are-run). También puede volver atrás para [ver o modificar un artefacto](#view-or-modify-an-artifact).
1. Cuando haya terminado de agregar artefactos, seleccione **Aplicar**.

### <a name="change-the-order-in-which-artifacts-are-run"></a>Cambio del orden en que se ejecutan los artefactos
De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Los siguientes pasos muestran cómo cambiar el orden en que se ejecutan los artefactos.

1. En la parte superior del panel **Aplicar artefactos**, seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. En el panel **Artefactos seleccionados**, arrastre los artefactos y colóquelos en el orden deseado. Si tiene problemas para arrastrar el artefacto, asegúrese de que lo arrastra desde el lado izquierdo del artefacto. 
1. Seleccione **Aceptar** cuando haya terminado.  

### <a name="view-or-modify-an-artifact"></a>ver o modificar un artefacto
Los siguientes pasos muestran cómo ver o modificar los parámetros de un artefacto:

1. En la parte superior del panel **Aplicar artefactos**, seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. En el panel **Artefactos seleccionados**, seleccione el artefacto que desea ver o modificar.  
1. En el panel **Agregar artefacto**, realice los cambios necesarios y seleccione **Aceptar** para cerrar el panel **Agregar artefacto**.
1. Seleccione **Aceptar** para cerrar el panel **Artefactos seleccionados**.

## <a name="use-powershell"></a>Uso de PowerShell
El siguiente script aplica el artefacto especificado a la máquina virtual especificada. El [Invoke AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) comando es lo que realiza la operación.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Pasos siguientes
En los artefactos, consulte los siguientes artículos:

- [Especifique los artefactos obligatorios para el laboratorio](devtest-lab-mandatory-artifacts.md)
- [Creación de artefactos personalizados](devtest-lab-artifact-author.md)
- [Agregar un repositorio de artefactos a un laboratorio](devtest-lab-artifact-author.md)
- [Diagnóstico de errores de artefactos](devtest-lab-troubleshoot-artifact-failure.md)
