---
title: Conversión de una máquina virtual Linux en Azure con discos no administrados a discos administrados - Azure Managed Disks | Microsoft Docs
description: Conversión de una máquina virtual Linux con discos no administrados en discos administrados mediante la CLI de Azure en el modelo de implementación de Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d1783890ba1b304ab658e827bfd24091e208fda4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699251"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Conversión de una máquina virtual Linux con discos no administrados en discos administrados

Si ya dispone de máquinas virtuales (VM) con Linux que usan Unmanaged Disks, puede convertirlas para usar [Azure Managed Disks](../linux/managed-disks-overview.md). Este proceso convierte el disco del SO y los discos de datos conectados.

En este artículo se muestra cómo convertir máquinas virtuales con la CLI de Azure. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de empezar
* Revise las [preguntas frecuentes sobre la migración a Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Conversión de máquinas virtuales de instancia única
En esta sección se explica cómo convertir máquinas virtuales de Azure de instancia única de Unmanaged Disks a Managed Disks. (Si las máquinas virtuales se encuentran en un conjunto de disponibilidad, consulte la sección siguiente). Puede usar este proceso para convertir las máquinas virtuales de discos no administrados premium (SDD) a discos administrados premium, o bien de discos no administrados estándar (HDD) a discos administrados estándar.

1. Desasigne la máquina virtual mediante [az vm deallocate](/cli/azure/vm). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convierta la máquina virtual en discos administrados con [az vm convert](/cli/azure/vm). El proceso siguiente convierte la máquina virtual denominada `myVM`, incluidos el disco del SO y todos los discos de datos:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie la máquina virtual después de la conversión a discos administrados con [az vm start](/cli/azure/vm). En el ejemplo siguiente se inicia la VM llamada `myVM` en el grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Conversión de VM de un conjunto de disponibilidad

Si las VM que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

Todas las VM del conjunto de disponibilidad deben desasignarse antes de convertir el conjunto de disponibilidad. Planee la conversión de todas las máquinas virtuales a discos administrados después de haber convertido el propio conjunto de disponibilidad en un conjunto de disponibilidad administrado. Después, inicie todas las máquinas virtuales y siga trabajando con normalidad.

1. Enumere todas las máquinas virtuales de un conjunto de disponibilidad con [az vm availability-set list](/cli/azure/vm/availability-set). En el ejemplo siguiente se enumeran todas las VM del conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desasigne todas las máquinas virtuales con [az vm deallocate](/cli/azure/vm). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convierta el conjunto de disponibilidad con [az vm availability-set convert](/cli/azure/vm/availability-set). En el ejemplo siguiente se convierte el conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convierta todas las máquinas virtuales a discos administrados con [az vm convert](/cli/azure/vm). El proceso siguiente convierte la máquina virtual denominada `myVM`, incluidos el disco del SO y todos los discos de datos:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas las máquinas virtuales después de la conversión a discos administrados con [az vm start](/cli/azure/vm). En el ejemplo siguiente se inicia la máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las opciones de almacenamiento, vea [Introducción a Azure Managed Disks](../windows/managed-disks-overview.md).
