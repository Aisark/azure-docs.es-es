---
title: Ejemplos de PowerShell de máquina virtual de Azure | Microsoft Docs
description: Ejemplos de PowerShell de máquina virtual de Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 2d954bc068693a34ef1d69e4296e972979d4f61b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671031"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ejemplos de PowerShell de máquina virtual de Azure

En la tabla siguiente se incluyen vínculos a ejemplos de scripts de PowerShell que crean y administran máquinas virtuales Linux.

| | |
|---|---|
|**Creación de máquinas virtuales**||
| [Creación una máquina virtual completamente configurada](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un grupo de recursos, una máquina virtual y todos los recursos relacionados.|
| [Creación de una máquina virtual con Docker habilitado](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual, la configura como un host Docker y ejecuta un contenedor NGINX. |
| [Creación de una máquina virtual y ejecución de un script de configuración](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar NGINX. |
| [Creación de una máquina virtual con WordPress instalado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar WordPress. |
| [Creación de una máquina virtual desde un disco administrado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual conectando un disco administrado como disco del SO. |
| [Creación de una VM a partir de una instantánea](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual desde una instantánea creando primero un disco administrado de la instantánea y conectando luego el nuevo disco administrado como disco del sistema operativo. |
|**Administrar el almacenamiento**||
| [Creación de un disco administrado desde un VHD en la misma suscripción o en otra](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un disco administrado a partir de un VHD especializado como un disco del sistema operativo, o a partir de un VHD de datos como un disco de datos en la misma suscripción o en otra.  |
| [Crear un disco administrado a partir de una instantánea](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un disco administrado a partir de una instantánea. |
| [Exportación de una instantánea como VHD a una cuenta de almacenamiento](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta una instantánea administrada como VHD a una cuenta de almacenamiento en otra región. |
| [Exportar el disco duro virtual de un disco administrado a una cuenta de almacenamiento](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta el VHD subyacente de un disco administrado a una cuenta de almacenamiento en otra región. |
| [Creación de una instantánea a partir de un disco duro virtual](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una instantánea a partir de un VHD y luego usa esa instantánea para crear varios discos administrados idénticos rápidamente.  |
| [Copia de una instantánea en la misma suscripción o en otra](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia la instantánea en la misma suscripción o en otra, que está en la misma región que la instantánea primaria. |
|**Supervisión de máquinas virtuales**||
| [Supervisión de una máquina virtual con registros de Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea una máquina virtual, instala el agente de Log Analytics e inscribe la máquina virtual en un área de trabajo de Log Analytics.  |
| [Copia de un disco administrado en la misma suscripción o en otra](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia un disco administrado en la misma suscripción o en otra, que está en la misma región que el disco administrado primario.
| [Recopilar detalles sobre todas las máquinas virtuales de una suscripción con PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crea un csv que contiene el nombre de la máquina virtual, el nombre del grupo de recursos, la región, la red virtual, la subred, la dirección IP privada, el tipo de sistema operativo y la dirección IP pública de las máquinas virtuales de la suscripción proporcionada.
| | |
