---
title: Copia de una máquina virtual Linux mediante la CLI de Azure | Microsoft Docs
description: Aprenda a crear una copia de la máquina virtual Linux de Azure mediante la CLI de Azure y Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328755"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Creación de una copia de una máquina virtual Linux mediante la CLI de Azure y Managed Disks

En este artículo se muestra cómo crear una copia de una máquina virtual de Azure con Linux mediante el modelo de implementación de Azure Resource Manager y la CLI de Azure. 

También puede [cargar y crear una máquina virtual a partir de un VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Requisitos previos

-   Instale la [CLI de Azure](/cli/azure/install-az-cli2).

-   Iniciar sesión en una cuenta de Azure con [az login](/cli/azure/reference-index#az-login).

-   Tener una máquina virtual de Azure como el origen de la copia.

## <a name="stop-the-source-vm"></a>Detención de la máquina virtual de origen

Desasigne la máquina virtual de origen mediante [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
En el ejemplo siguiente se desasigna la máquina virtual denominada "*myVM*" en el grupo de recursos *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copia de la máquina virtual de origen

Para copiar una VM, se crea una copia del disco duro virtual subyacente. Mediante este proceso, se crea un disco duro virtual (VHD) especializado como disco administrado que contiene la misma configuración que la máquina virtual de origen.

Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../windows/managed-disks-overview.md) (Introducción a Azure Managed Disks). 

1.  Enumere cada máquina virtual y el nombre de su disco del SO con [az vm list](/cli/azure/vm#az-vm-list). En el ejemplo siguiente se enumeran todas las VM del grupo de recursos denominado *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copie el disco mediante la creación de un disco administrado con [az disk create](/cli/azure/disk#az-disk-create). En el ejemplo siguiente se crea un disco llamado "*myCopiedDisk*" a partir del disco administrado *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Compruebe ahora los discos administrados del grupo de recursos mediante [az disk list](/cli/azure/disk#az-disk-list). En el ejemplo siguiente se enumeran todos los discos administrados del grupo de recursos *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Configuración de una red virtual

Con los siguientes pasos opcionales se crea una nueva red virtual, una subred, una dirección IP pública y una tarjeta de interfaz de red virtual (NIC).

Si va a copiar una máquina virtual para solucionar problemas o para realizar implementaciones adicionales, puede que no desee usar una máquina virtual de una red virtual existente.

Si desea crear una infraestructura de red virtual para las máquinas virtuales copiadas, siga estos pasos. Si no desea crear una red virtual, vaya a [Creación de una máquina virtual](#create-a-vm).

1.  Cree la red virtual mediante [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* y una subred *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Cree una IP pública mediante [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). En el ejemplo siguiente se crea una IP pública denominada "*myPublicIP*" con el nombre DNS *mypublicdns*. (El nombre DNS debe ser único, así que proporcione un nombre único).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Cree la NIC mediante [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    En el ejemplo siguiente se crea una NIC *myNic* conectada a la subred *mySubnet*:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Crear una VM

Cree una máquina virtual mediante [az vm create](/cli/azure/vm#az-vm-create).

Especifique el disco administrado copiado que usará como disco del SO (`--attach-os-disk`), como se indica a continuación:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Pasos siguientes

Para aprender a usar la CLI de Azure para administrar la nueva máquina virtual, consulte [Comandos de la CLI de Azure en el modo de Azure Resource Manager](../azure-cli-arm-commands.md).
