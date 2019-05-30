---
title: Uso de rutas personalizadas de Azure para habilitar la activación de KMS con tunelización forzada | Microsoft Docs
description: Se muestra cómo usar rutas personalizadas de Azure para habilitar la activación de KMS al usar tunelización forzada en Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 6557649eb1b97ad4d88876906737f8249e18b958
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399806"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Error de activación de Windows en el escenario de tunelización forzada

En este artículo se describe cómo resolver el problema de activación de KMS que podría experimentar al habilitar la tunelización forzada en escenarios de conexión VPN de sitio a sitio o ExpressRoute.

## <a name="symptom"></a>Síntoma

La [tunelización forzada](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) se habilita en las subredes de red virtual de Azure para dirigir todo el tráfico vinculado a Internet de vuelta a la red local. En este escenario, las máquinas virtuales (VM) de Azure que ejecutan Windows Server 2012 R2 (o versiones posteriores de Windows) pueden activar Windows correctamente. Pero las máquinas virtuales que ejecutan una versión anterior de Windows no pueden activar Windows.

## <a name="cause"></a>Causa

Las máquinas virtuales Windows de Azure se deben conectar al servidor de KMS de Azure para la activación de Windows. La activación requiere que la solicitud de activación proceda de una dirección IP pública de Azure. En el escenario de tunelización forzada, se produce un error en la activación porque la solicitud de activación procede de la red local en lugar de una dirección IP pública de Azure.

## <a name="solution"></a>Solución

Para resolver este problema, use la ruta personalizada de Azure para dirigir el tráfico de activación al servidor de KMS de Azure.

La dirección IP del servidor de KMS para la nube global de Azure es 23.102.135.246. El nombre DNS es kms.core.windows.net. Si usa otras plataformas de Azure como Azure Alemania, tendrá que usar la dirección IP del servidor de KMS correspondiente. Para más información, vea la tabla siguiente:

|Plataforma| DNS DE KMS|DIRECCIÓN IP DE KMS|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Alemania|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Para agregar la ruta personalizada, siga estos pasos:

### <a name="for-resource-manager-vms"></a>Para máquinas virtuales de Resource Manager

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Abra Azure PowerShell y después [inicie sesión en la suscripción de Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Ejecute los comandos siguientes:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Vaya a la máquina virtual que tiene problemas de activación. Use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para probar si puede alcanzar el servidor de KMS:

        psping kms.core.windows.net:1688

4. Intente activar Windows y compruebe si el problema se ha resuelto.

### <a name="for-classic-vms"></a>Para máquinas virtuales clásicas

1. Abra Azure PowerShell y después [inicie sesión en la suscripción de Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Ejecute los comandos siguientes:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Vaya a la máquina virtual que tiene problemas de activación. Use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para probar si puede alcanzar el servidor de KMS:

        psping kms.core.windows.net:1688

4. Intente activar Windows y compruebe si el problema se ha resuelto.

## <a name="next-steps"></a>Pasos siguientes

- [Claves de configuración de cliente KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Review and Select Activation Methods](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
) (Revisión y selección de métodos de activación)
