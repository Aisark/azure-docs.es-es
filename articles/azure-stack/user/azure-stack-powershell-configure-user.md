---
title: Conexión a Azure Stack con PowerShell como usuario | Microsoft Docs
description: Pasos para conectarse a la instancia de Azure Stack del usuario.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: b8f2e3ebfa7187b6695fbd291c7baf0a9ba3b712
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485788"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Conexión a Azure Stack con PowerShell como usuario

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Este artículo proporciona los pasos necesarios para conectarse a la instancia de Azure Stack. Debe conectarse para administrar los recursos de Azure Stack con PowerShell. Por ejemplo, puede usar PowerShell para suscribirse a ofertas, crear máquinas virtuales e implementar plantillas de Azure Resource Manager. Para ejecutar los cmdlets de PowerShell.

Para conseguir la configuración:
  - Asegúrese de que cumple los requisitos.
  - Conéctese a Azure Active Directory (Azure AD) o a Servicios de federación de Active Directory (AD FS). 
  - Registre los proveedores de recursos.
  - Pruebe la conectividad.

## <a name="prerequisites"></a>Requisitos previos

Puede configurar estos requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).
* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).

Asegúrese de reemplazar las siguientes variables de script con los valores de la configuración de Azure Stack:

- **Nombre del inquilino de Azure AD**  
  El nombre del inquilino de Azure AD que se usa para administrar Azure Stack, por ejemplo, sudirectorio.onmicrosoft.com.
- **Punto de conexión de Azure Resource Manager**  
  En el kit de desarrollo de Azure Stack, este valor se establece en https://management.local.azurestack.external. Para obtener este valor para los sistemas integrados de Azure Stack, póngase en contacto con su proveedor de servicios.

## <a name="connect-with-azure-ad"></a>Conexión con Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Conexión con AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Registro de proveedores de recursos

Los proveedores de recursos no se registran automáticamente para nuevas suscripciones de usuario que no tienen ningún recurso implementado a través del portal. Puede registrar explícitamente un proveedor de recursos ejecutando el script siguiente:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Cuando tenga todo configurado, pruebe la conectividad con PowerShell para crear recursos en Azure Stack. Como prueba, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Ejecute el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
- [Referencia del módulo de PowerShell de Azure Stack](https://docs.microsoft.com/en-us/powershell/azure/azure-stack/overview)
- Si desea configurar PowerShell para el entorno de operadores en la nube, consulte el artículo [Configuración del entorno de PowerShell del operador de Azure Stack](../azure-stack-powershell-configure-admin.md).
