---
title: Carga de archivos VHD en Azure DevTest Labs mediante PowerShell | Microsoft Docs
description: Carga de archivos VHD en la cuenta de almacenamiento del laboratorio mediante PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622786"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Carga de archivos VHD en la cuenta de almacenamiento del laboratorio mediante PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

En Azure DevTest Labs, se pueden usar archivos VHD para crear imágenes personalizadas, que se usan para aprovisionar máquinas virtuales. Los siguientes pasos le guían en el uso de PowerShell para cargar un archivo VHD en una cuenta de almacenamiento del laboratorio. Cuando haya cargado el archivo VHD, en la sección de [pasos siguientes](#next-steps) se muestran algunos artículos que ilustran cómo crear una imagen personalizada a partir del archivo VHD cargado. Para más información sobre los discos y los discos duros virtuales en Azure, consulte [Introducción a Azure Managed Disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrucciones paso a paso

Los siguientes pasos le guían en la carga de un archivo VHD en Azure DevTest Labs mediante PowerShell. 

1. Inicie sesión en el [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.  

1. En la hoja del laboratorio, seleccione **Configuración**. 

1. En la hoja **Configuración** del laboratorio, seleccione **Custom images (VHDs)** (Imágenes personalizadas [VHD]).

1. En la hoja **Custom images** (Imágenes personalizadas), seleccione **+Agregar**. 

1. En la hoja **Custom images** (Imágenes personalizadas), seleccione **+Agregar**.

1. En la hoja **VHD**, seleccione **Upload a VHD using PowerShell** (Cargar un VHD mediante PowerShell).

    ![Carga del VHD mediante PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. En la hoja **Upload an image using PowerShell** (Cargar una imagen mediante PowerShell), copie el script de PowerShell generado en un editor de texto.

1. Modifique el parámetro **LocalFilePath** del cmdlet **Add-AzureVhd** para que apunte a la ubicación del archivo VHD que quiere cargar.

1. En un símbolo del sistema de PowerShell, ejecute el cmdlet **Add-AzureVhd** (con el parámetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> El proceso de cargar un archivo VHD puede ser largo en función de su tamaño y de la velocidad de conexión.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante el portal de Azure](devtest-lab-create-template.md)
- [Creación de una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
