---
title: Sustitución del certificado en un clúster de Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre cómo sustituir el certificado en un clúster de Service Fabric identificado por el nombre común del certificado.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: dd4b6026772a20c522532e1ba65c6846addfa161
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159895"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Sustitución manual del certificado en un clúster de Service Fabric
Cuando el certificado de un clúster de Service Fabric esté a punto de expirar, deberá actualizarlo.  La sustitución del certificado es sencilla si el clúster está [configurado para usar certificados en función del nombre común](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (en lugar de la huella digital).  Obtenga un nuevo certificado de una entidad de certificación con una nueva fecha de expiración.  Los certificados autofirmados no son compatibles con los clústeres de Service Fabric de producción para incluir certificados generados durante el flujo de trabajo de creación del clúster de Azure Portal. El nuevo certificado debe tener el mismo nombre común que el certificado anterior. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

El clúster de Service Fabric usará automáticamente el certificado declarado con una fecha de expiración futura, cuando haya más de un certificado de validación instalado en el host. Un procedimiento recomendado es usar una plantilla de Resource Manager para aprovisionar recursos de Azure. En un entorno no de producción, se puede usar el script siguiente para cargar un certificado nuevo en un almacén de claves y, luego, instalar el certificado en el conjunto de escalado de máquinas virtuales: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Los secretos de un conjunto de escalado de máquinas virtuales no admiten el mismo identificador de recurso para dos secretos independientes, porque cada secreto es una versión única de un recurso. 

Para más información, lea lo siguiente:
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* [Actualizar y administrar certificados del clúster](service-fabric-cluster-security-update-certs-azure.md)

