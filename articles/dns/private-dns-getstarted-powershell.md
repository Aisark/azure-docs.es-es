---
title: 'Tutorial: Creación de una zona privada de Azure DNS mediante Azure PowerShell'
description: En este tutorial se crean y se prueban una zona DNS privada y un registro en Azure DNS. Esta es una guía paso a paso para crear y administrar la primera zona y el primer registro DNS privados con Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b4d75c7a6db89b19d88cddcc564fd4e6a9ad0f49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "65916877"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Tutorial: Creación de una zona privada de Azure DNS mediante Azure PowerShell

Este tutorial le guiará por los pasos necesarios para crear una zona y un registro DNS privados con Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Para publicar una zona DNS privada en la red virtual, especifique la lista de redes virtuales que pueden resolver registros en ella.  Se denominan *redes virtuales de resolución*. También puede especificar una red virtual para la que Azure DNS mantiene los registros de nombre de host siempre que se cree o se elimine una máquina virtual, o se cambie su dirección IP.  Se denomina *red virtual de registro*.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una zona DNS privada
> * Creación de máquinas virtuales de prueba
> * Creación de un registro de DNS adicional
> * Prueba de la zona privada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Si lo prefiere, puede realizar los pasos de este tutorial mediante la [CLI de Azure](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

En primer lugar, cree un grupo de recursos que contenga la zona DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Creación de una zona DNS privada

Para crear zona DNS se utiliza el cmdlet `New-AzDnsZone` con el valor *Private* en el parámetro **ZoneType**. En el ejemplo siguiente se crea una zona DNS denominada **private.contoso.com** en el grupo de recursos denominado **MyAzureResourceGroup** y pone la zona DNS a disposición de la red virtual denominada **MyAzureVnet**.

Si se omite el parámetro **ZoneType**, la zona se crea como zona pública, por lo que es necesario para crear una zona privada. 

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Si desea crear una zona solo para la resolución de nombres (sin creación automática de nombre de host), puede usar el parámetro *ResolutionVirtualNetworkId*, en lugar de *RegistrationVirtualNetworkId*.

> [!NOTE]
> No podrá ver los registros de nombre de host creados automáticamente. Pero más adelante, realizará las pruebas necesarias para asegurarse de que existen.

### <a name="list-dns-private-zones"></a>Listado de zonas privadas de DNS

Si se omite el nombre de la zona de `Get-AzDnsZone`, puede enumerar todas las zonas en un grupo de recursos. Esta operación devuelve una matriz de objetos de la zona.

```azurepowershell
Get-AzDnsZone -ResourceGroupName MyAzureResourceGroup
```

Si se omite tanto el nombre de zona como el nombre del grupo de recursos de `Get-AzDnsZone`, puede enumerar todas las zonas de la suscripción de Azure.

```azurepowershell
Get-AzDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Creación de las máquinas virtuales de prueba

Ahora, cree dos máquinas virtuales para poder probar su zona DNS privada:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Esta operación tardará algunos minutos en completarse.

## <a name="create-an-additional-dns-record"></a>Creación de un registro de DNS adicional

Los conjuntos de registros se crean mediante el cmdlet `New-AzDnsRecordSet`. En el ejemplo siguiente se crea un registro con el nombre relativo **db** en la zona DNS **private.contoso.com** del grupo de recursos **MyAzureResourceGroup**. El nombre completo del conjunto de registros es **db.private.contoso.com**. El tipo de registro es "D", con la dirección IP 10.2.0.4 y el valor de TTL es de 3600 segundos.

```azurepowershell
New-AzDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Visualización de registros DNS

Para enumerar los registros DNS de su zona, ejecute:

```azurepowershell
Get-AzDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```
Recuerde que no verá los registros D creados automáticamente de las dos máquinas virtuales de prueba.

## <a name="test-the-private-zone"></a>Prueba de la zona privada

Ya puede probar la resolución de nombres de la zona privada **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configuración de máquinas virtuales para permitir ICMP de entrada

Puede usar el comando ping para probar la resolución de nombres. Por tanto, configure el firewall en ambas máquinas virtuales para permitir paquetes ICMP entrantes.

1. Conéctese a myVM01 y abra una ventana de Windows PowerShell con privilegios de administrador.
2. Ejecute el siguiente comando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita la operación con myVM02.

### <a name="ping-the-vms-by-name"></a>Realización de ping en las máquinas virtuales por nombre

1. En el símbolo del sistema de Windows PowerShell de myVM02, haga ping a myVM01 con el nombre de host registrado automáticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   La salida es similar a esta:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ahora haga ping en el nombre de la **base de datos** que creó anteriormente:
   ```
   ping db.private.contoso.com
   ```
   La salida es similar a esta:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Cuando no lo necesite, elimine el grupo de recursos **MyAzureResourceGroup** para eliminar los recursos que ha creado en este tutorial.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado una zona DNS privada, ha creado un registro de DNS y ha probado la zona.
A continuación, puede obtener más información acerca de las zonas DNS privadas.

> [!div class="nextstepaction"]
> [Uso de Azure DNS en dominios privados](private-dns-overview.md)




