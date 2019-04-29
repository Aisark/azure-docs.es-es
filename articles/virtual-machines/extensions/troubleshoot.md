---
title: Solución de problemas de las extensiones de máquina virtual Windows | Microsoft Docs
description: Más información sobre la solución de problemas de la extensión de máquina virtual de Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107251"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solución de problemas de la extensión de máquina virtual de Microsoft Azure.
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Consulta del estado de la extensión
Las plantillas de Azure Resource Manager se pueden ejecutar desde Azure PowerShell. Cuando se ejecute la plantilla, el estado de extensión se puede ver desde el Explorador de recursos de Azure o las herramientas de la línea de comandos.

Este es un ejemplo:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Este es la salida de ejemplo:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Solución de problemas de errores de extensión
### <a name="rerun-the-extension-on-the-vm"></a>Volver a ejecutar la extensión en la máquina virtual
Si está ejecutando los scripts en la máquina virtual mediante la extensión de script personalizada, a veces podría recibir un error en el que la máquina virtual se creó correctamente, pero en el que el script ha generado un error. En estas condiciones, la manera que se recomienda para recuperarse de este error es quitar la extensión y volver a ejecutar la plantilla.
Nota: En el futuro, esta funcionalidad se mejoraría para eliminar la necesidad de desinstalar la extensión.

#### <a name="remove-the-extension-from-azure-powershell"></a>Eliminación de la extensión de Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Cuando se ha quitado la extensión, la plantilla puede volver a ejecutarse para ejecutar los scripts en la máquina virtual.

