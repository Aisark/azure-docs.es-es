---
title: 'Ejemplo de script de CLI de Azure: creación de un disco administrado a partir de un archivo de disco duro virtual en una cuenta de almacenamiento en la misma suscripción | Microsoft Docs'
description: 'Ejemplo de script de CLI de Azure: creación un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 3f68c4ccbcaad682303c9d67c95f8bf8a2d3041b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249758"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Creación un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción con CLI

Este script crea un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción. Use este script para importar un VHD especializado (no generalizado ni preparado con sysprep) en el disco de sistema operativo administrado para crear una máquina virtual. O bien úselo para importar un VHD de datos en el disco de datos administrado.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un disco administrado a partir de un VHD. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Crea un disco administrado usando el identificador URI de un VHD en una cuenta de almacenamiento en la misma suscripción. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Se pueden encontrar ejemplos de scripts adicionales de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
