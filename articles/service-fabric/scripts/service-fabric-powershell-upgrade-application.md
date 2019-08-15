---
title: 'Script de ejemplo de Azure PowerShell: actualización de una aplicación de Service Fabric | Microsoft Docs'
description: 'Script de ejemplo de Azure PowerShell: actualización de una aplicación de Service Fabric.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 45186f497371b533451ff374e68b38f9a7eebe51
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035496"
---
# <a name="upgrade-a-service-fabric-application"></a>Actualización de una aplicación de Service Fabric

Este script de ejemplo actualiza una instancia de la aplicación de Service Fabric en ejecución a la versión 1.3.0. El script copia el nuevo paquete de aplicación en el almacén de imágenes de clúster, registra el tipo de aplicación y quita el paquete de aplicación innecesario.  El script inicia una actualización supervisada y comprueba continuamente el estado de actualización hasta que esta se complete o se revierta. Personalice los parámetros según sea necesario. 

Si es necesario, instale el módulo Service Fabric PowerShell con el [SDK de Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Obtiene todas las aplicaciones del clúster de Service Fabric o una aplicación específica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Obtiene el estado de actualización de una aplicación de Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Obtiene los tipos de aplicaciones de Service Fabric registrados en el clúster de Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Anula el registro de un tipo de aplicación de Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia un paquete de aplicación de Service Fabric en el almacén de imágenes.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registra un tipo de aplicación de Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Actualiza una aplicación de Service Fabric a la versión especificada de ese tipo de aplicación. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Quita un paquete de aplicación de Service Fabric del almacén de imágenes.|


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el módulo Service Fabric PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Puede encontrar ejemplos de PowerShell para Azure Service Fabric en los [ejemplos de Azure PowerShell](../service-fabric-powershell-samples.md).
