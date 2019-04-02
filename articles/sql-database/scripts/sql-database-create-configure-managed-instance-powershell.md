---
title: Ejemplo de PowerShell para crear una instancia administrada en Azure SQL Database | Microsoft Docs
description: Script de ejemplo de Azure PowerShell para crear una instancia administrada en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 955f13376ac899f66b0ec4e1ed99166164508fbe
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449818"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Use PowerShell para crear una instancia administrada de Azure SQL Database

En este ejemplo de script de PowerShell se crea una instancia administrada de Azure SQL Database en una subred dedicada de una red virtual nueva. También se configura una tabla de rutas y un grupo de seguridad de red para la red virtual. Una vez que el script se ha ejecutado correctamente, se puede acceder a la instancia administrada desde dentro de la red virtual o desde un entorno local. Para conocer tutoriales de inicio rápido en los que se muestre cómo realizar esta tarea, consulte [Configuración de una máquina virtual de Azure para la conexión a una Instancia administrada de Azure SQL Database](../sql-database-managed-instance-configure-vm.md) y [Configuración de una conexión de punto a sitio en una Instancia administrada de Azure SQL Database desde un entorno local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para conocer las limitaciones, consulte las [regiones admitidas](../sql-database-managed-instance-resource-limits.md#supported-regions) y los [tipos de suscripción admitidos](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de manera local, en este tutorial se requiere la versión 1.4.0 de AZ PowerShell o posterior. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una red virtual |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Agrega una configuración de subred a una red virtual |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtiene una red virtual en un grupo de recursos |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Establece el estado objetivo de una red virtual |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtiene una subred en una red virtual |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura el estado objetivo de una configuración de subred en una red virtual |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Crea una tabla de rutas |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtiene tablas de rutas |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Establece el estado objetivo de una tabla de rutas |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Crea una instancia administrada de Azure SQL Database |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de PowerShell de SQL Database en los [scripts de PowerShell de Azure SQL Database](../sql-database-powershell-samples.md).
