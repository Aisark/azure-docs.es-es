---
title: 'Ejemplo de script de CLI de Azure: copia (transferencia) de instantánea de un disco administrado en la misma suscripción o en otra con CLI | Microsoft Docs'
description: 'Ejemplo de script de CLI de Azure: copia (transferencia) de instantánea de un disco administrado en la misma suscripción o en otra con CLI'
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
ms.openlocfilehash: 744990b06b5585763615eb69909c29a27dee5a7f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249781"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copia de instantánea de un disco administrado en la misma suscripción o en otra con CLI

Este script copia una instantánea de un disco administrado en la misma suscripción o en otra. Use este script en los escenarios siguientes:

1. Migrar una instantánea de Premium Storage (Premium_LRS) a Standard Storage (Standard_LRS o Standard_ZRS) para reducir el costo.
1. Migrar una instantánea de almacenamiento con redundancia local (Premium_LRS, Standard_LRS) a almacenamiento con redundancia de zona (Standard_ZRS) para beneficiarse de la mayor confiabilidad del almacenamiento ZRS.
1. Mover una instantánea a otra suscripción de la misma región para alargar la retención.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear una instantánea en la suscripción de destino mediante el identificador de la instantánea de origen. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Obtiene todas las propiedades de una instantánea usando las propiedades de nombre y grupo de recursos de la instantánea. La propiedad del identificador se usa para copiar la instantánea en otra suscripción.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Copia una instantánea mediante la creación de una instantánea en otra suscripción usando el identificador y nombre de la instantánea primaria.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Se pueden encontrar ejemplos de scripts adicionales de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
