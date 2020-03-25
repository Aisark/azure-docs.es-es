---
title: 'Ejemplo de script de la CLI de Azure: Montaje del disco de sistema operativo'
description: 'Ejemplo de script de la CLI de Azure: Montaje del disco de sistema operativo'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/24/2020
ms.locfileid: "74037591"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Solución de problemas de un disco de sistema operativo de máquina virtual

Este script monta el disco del sistema operativo de una máquina virtual problemática o que ha generado un error, como un disco de datos en una segunda máquina virtual. Esto puede ser útil para solucionar problemas de disco o de recuperación de datos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Devuelve una lista de máquinas virtuales. En este caso, la opción de consulta se utiliza para devolver el disco de sistema operativo de la máquina virtual. Este valor se agrega, posteriormente, a un nombre de variable "uri". |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Elimina una máquina virtual. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Crea una máquina virtual.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Conecta un disco a una máquina virtual. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Devuelve las direcciones IP de una máquina virtual. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
