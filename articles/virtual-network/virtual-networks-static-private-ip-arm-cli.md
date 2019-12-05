---
title: 'Configuración de direcciones IP privadas para máquinas virtuales: CLI de Azure'
description: Aprenda a configurar direcciones IP privadas para máquinas virtuales mediante la interfaz de la línea de comandos (CLI) de Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: dfc56d86d2e516a7c7bb82ef7a5e84105e049188
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404460"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configuración de direcciones IP privadas para una máquina virtual mediante la CLI de Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> En los siguientes comandos de ejemplo de la CLI de Azure se espera un entorno simple existente. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Especificación de una dirección IP privada estática al crear una VM

Para crear una máquina virtual denominada *DNS01* en la subred *FrontEnd* de una red virtual denominada *TestVNet* con una dirección IP privada estática de *192.168.1.101*, complete estos pasos:

1. Si aún no lo ha hecho, instale y configure la última versión de la [CLI de Azure](/cli/azure/install-azure-cli) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).

2. Cree una dirección IP pública para la máquina virtual con el comando [az network public-ip create](/cli/azure/network/public-ip). En la lista que se muestra en la salida se explican los parámetros utilizados.

    > [!NOTE]
    > Puede que quiera o necesite usar valores diferentes para los argumentos de este paso y los siguientes, dependiendo de su entorno.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Resultado esperado:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: nombre del grupo de recursos en el que se creará la dirección IP pública.
   * `--name`: Nombre de la dirección IP pública.
   * `--location`: región de Azure donde se va a crear la dirección IP pública.

3. Ejecute el comando [azure network nic create](/cli/azure/network/nic) para crear una NIC con una dirección IP privada estática. En la lista que se muestra en la salida se explican los parámetros utilizados. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Resultado esperado:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parámetros:

    * `--private-ip-address`: Dirección IP privada estática para la NIC.
    * `--vnet-name`: nombre de la red virtual en la que se va a crear la NIC.
    * `--subnet`: nombre de la subred en la que se va a crear la NIC.

4. Ejecute el comando [azure vm create](/cli/azure/vm/nic) para crear la máquina virtual mediante la dirección IP pública y la NIC creadas anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Resultado esperado:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parámetros que no sean las opciones básicas de [az vm create](/cli/azure/vm).

   * `--nics`: nombre de la NIC a la que está conectada la máquina virtual.
   
Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-cli.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private).

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM

Ejecute el siguiente comando de la CLI de Azure para observar los valores de *Método de asignación de dirección IP privada* y *Dirección IP privada*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Resultado esperado:

```json
"192.168.1.101"
```

Para mostrar la información específica de la dirección IP de la NIC de esa máquina virtual, consulte la NIC específicamente:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

La salida es similar a esta:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM

No se puede eliminar una dirección IP privada estática de una NIC en la CLI de Azure para implementaciones de Azure Resource Manager. Debe:
- Crear una nueva NIC que use una dirección IP dinámica.
- Establecer que la NIC de la máquina virtual sea la NIC recién creada. 

Para cambiar el NIC de la máquina virtual que se utiliza en los comandos anteriores, realice los siguientes pasos:

1. Ejecute el comando **azure network nic create** para crear una nueva NIC mediante asignación de IP dinámica con una dirección IP nueva. Como no se especifica ninguna dirección IP, el método de asignación es **Dynamic**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Resultado esperado:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Ejecuta el comando **azure vm set** para cambiar la NIC que usó la VM.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Resultado esperado:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Si la máquina virtual es suficientemente grande para tener más de una NIC, ejecute el comando **azure network nic delete** para eliminar la NIC antigua.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la administración de la [configuración de la dirección IP privada](virtual-network-network-interface-addresses.md).
