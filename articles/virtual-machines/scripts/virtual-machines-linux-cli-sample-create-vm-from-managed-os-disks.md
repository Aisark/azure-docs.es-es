---
title: 'Creación de una máquina virtual conectando un disco administrado como disco del SO: Ejemplo de la CLI'
description: 'Script de CLI de Azure de ejemplo: creación de una máquina virtual conectando un disco administrado como disco del SO'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 83f0ea094c26a1ff664ef27729731b77d987e7ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501507"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Creación de una máquina virtual con un disco del SO administrado mediante la CLI

Este script crea una máquina virtual conectando un disco administrado como disco del SO. Use este script en los anteriores escenarios:
* Creación de una máquina virtual desde un disco del SO administrado que se copió desde un disco administrado de otra suscripción
* Creación de una máquina virtual desde un disco administrado que se creó a partir de un archivo de disco duro virtual especializado 
* Creación de una máquina virtual desde un disco del SO administrado que se creó a partir de una instantánea 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para obtener las propiedades del disco administrado, conectar un disco administrado a una nueva máquina virtual y crear una máquina virtual. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az disk show](/cli/azure/disk) | Obtiene propiedades del disco administrado mediante el nombre del disco y el nombre del grupo de recursos. La propiedad de identificador se utiliza para conectar un disco administrado a una nueva máquina virtual |
| [az vm create](/cli/azure/vm) | Crea una máquina virtual con un disco del SO administrado. |
## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
