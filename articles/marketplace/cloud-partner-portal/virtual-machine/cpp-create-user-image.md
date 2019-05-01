---
title: Creación de una imagen de VM de usuario para Azure Marketplace | Microsoft Docs
description: Se enumeran los pasos y las referencias necesarios para crear una imagen de VM de usuario.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744115"
---
# <a name="create-a-user-vm-image"></a>Crear una imagen de máquina virtual de usuario

En este artículo se explican los dos pasos generales necesarios para crear una imagen no administrada desde un VHD generalizado.  Se proporcionan referencias para guiarle a través de cada paso: capturar la imagen y generalizar la imagen.


## <a name="capture-the-vm-image"></a>Capturar la imagen de máquina virtual

Siga las instrucciones del artículo siguiente sobre cómo capturar la máquina virtual que correspondan con el método de acceso:

-  PowerShell: [Captura de una imagen administrada de una máquina virtual generalizada en Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI de Azure: [Creación de una imagen de una máquina virtual o un disco duro virtual](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Máquinas virtuales: Capture)


## <a name="generalize-the-vm-image"></a>Generalizar la imagen de máquina virtual

Dado que ha generado la imagen de usuario a partir de un VHD generalizado anteriormente, también debería estar generalizada.  Una vez más, seleccione el siguiente artículo que corresponda con el mecanismo de acceso.  (Es posible que ya hubiera generalizado el disco cuando lo capturó).

-  PowerShell: [Generalización de la máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI de Azure: [Paso 2: Crear la imagen de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Máquinas virtuales: Generalize)


## <a name="next-steps"></a>Pasos siguientes

A continuación podrá [crear un certificado](cpp-create-key-vault-cert.md) y almacenarlo en un nuevo Azure Key Vault.  Este certificado es necesario para establecer una conexión WinRM segura a la VM.
