---
title: Implementación de plantillas mediante PowerShell en Azure Stack | Microsoft Docs
description: Implementación de una plantilla en Azure Stack mediante PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d71df99096e58b3ac7adc920b91891b9a50cd6f7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479588"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Implementación de una plantilla en Azure Stack mediante PowerShell

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar PowerShell para implementar plantillas de Azure Resource Manager en Azure Stack. En este artículo, se describe cómo usar PowerShell para implementar una plantilla.

## <a name="run-azurerm-powershell-cmdlets"></a>Ejecución de cmdlets de AzureRM PowerShell

En este ejemplo, se utilizan cmdlets de **AzureRM PowerShell** y una plantilla almacenada en GitHub. La plantilla crea una máquina virtual del centro de datos para Windows Server 2012 R2.

>[!NOTE]
>Antes de probar este ejemplo, asegúrese de que se haya [configurado PowerShell](azure-stack-powershell-configure-user.md) para un usuario de Azure Stack.

1. Vaya a [https://aka.ms/AzureStackGitHub](https://aka.ms/AzureStackGitHub) y busque la plantilla **101-simple-windows-vm**. Guarde la plantilla en esta ubicación: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Abra un símbolo del sistema de PowerShell con privilegios elevados.
3. En el siguiente script, sustituya `username` y `password` por su nombre de usuario y contraseña, y ejecute el script:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >Incremente el valor del parámetro `$myNum` cada vez que ejecute este script para evitar sobrescribir la implementación.

4. Abra el portal de Azure Stack, seleccione **Examinar**, luego seleccione **Máquinas virtuales** y busque su nueva máquina virtual (**myDeployment001**).

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
