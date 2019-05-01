---
title: Control de enrutamiento en Azure Virtual Network - PowerShell - Clásico | Microsoft Docs
description: Aprenda a controlar el enrutamiento en redes virtuales mediante PowerShell | Clásico
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 1441ee9a3d4a563ab35cd9b01e8347d8f51b827a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743406"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Control del enrutamiento y uso de aplicaciones virtuales (clásico) mediante PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI de Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (clásico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (clásico)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](../azure-resource-manager/resource-manager-deployment-model.md) antes de trabajar con recursos de Azure. Seleccione una opción en la parte superior de este artículo para ver la documentación de las distintas herramientas. Este artículo trata sobre el modelo de implementación clásico.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de Azure PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos según aparecen en este documento, cree primero el entorno mostrado en la [creación de una red virtual (clásico) mediante PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

1. Ejecute el comando siguiente para crear una tabla de rutas para la subred front-end:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Ejecute el siguiente comando para asociar la tabla de rutas a la subred **FrontEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesarias para la subred back-end según el escenario, siga estos pasos:

1. Ejecute el comando siguiente para crear una tabla de rutas para la subred back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Ejecute el comando siguiente para crear una ruta en la tabla de rutas para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Ejecute el comando siguiente para asociar la tabla de rutas a la subred **BackEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Habilitar el reenvío IP en la VM FW1

Para habilitar el reenvío IP en la máquina virtual FW1, complete los pasos siguientes:

1. Ejecute el siguiente comando para comprobar el estado del reenvío IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Ejecute el siguiente comando para habilitar el reenvío IP para la máquina virtual *FW1*:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
