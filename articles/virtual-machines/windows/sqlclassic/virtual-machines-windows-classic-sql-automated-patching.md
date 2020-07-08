---
title: Aplicación de revisiones automatizada para máquinas virtuales SQL Server (implementación clásica) | Microsoft Docs
description: Explica la característica Automated Patching para máquinas virtuales SQL Server que se ejecutan en Azure mediante el modelo de implementación clásica.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 555bd26ac894a3e06aa46eb0c51e20a35d21a9e7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078079"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automated Patching para SQL Server en Azure Virtual Machines (implementación clásica)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-patching.md)
> * [Clásico](../classic/sql-automated-patching.md)
> 
> 

Aplicación de revisión automatizada establece una ventana de mantenimiento para una máquina virtual de Azure con SQL Server. Actualizaciones automatizadas solo puede instalarse durante esta ventana de mantenimiento. Para SQL Server, esto garantiza que se actualiza el sistema y que cualquier reinicio asociado se producto en el mejor momento posible para la base de datos. 

> [!IMPORTANT]
> Solo se instalan las actualizaciones de Windows marcadas como **importantes**. Otras actualizaciones de SQL Server, como actualizaciones acumulativas, deben instalarse manualmente. 

Aplicación de revisión automatizada se basa en la [Extensión Agente de IaaS de SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/management/deployment-models.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para ver la versión de Resource Manager de este artículo, consulte [Aplicación de revisión automatizada para SQL Server en Azure Virtual Machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-patching.md).

## <a name="prerequisites"></a>Prerequisites
Para utilizar Aplicación de revisión automatizada, tenga en cuenta los siguientes requisitos previos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versión de SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Instale los cmdlets más recientes de Azure PowerShell](/powershell/azure/overview).

**Extensión IaaS de SQL Server**:

* [Instale la extensión IaaS de SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Configuración
En la siguiente tabla se describen las opciones que pueden configurarse para Aplicación de revisión automatizada. Para las máquinas virtuales clásicas, debe usar PowerShell para configurar estas opciones.

| Configuración | Valores posibles | Descripción |
| --- | --- | --- |
| **Aplicación de revisiones automatizada** |Habilitar/deshabilitar (deshabilitado) |Habilita o deshabilita Aplicación de revisión automatizada para una máquina virtual de Azure. |
| **Programación de mantenimiento** |Cada día, el lunes, el martes, el miércoles, el jueves, el viernes, el sábado, el domingo |La programación para descargar e instalar actualizaciones de Windows, SQL Server y Microsoft para la máquina virtual. |
| **Hora de inicio de mantenimiento** |0-24 |La hora de inicio local para actualizar la máquina virtual. |
| **Duración de la ventana de mantenimiento** |30-180 |El número de minutos permitido para completar la descarga y la instalación de actualizaciones. |
| **Categoría de la revisión** |Importante |La categoría de actualizaciones para descargar e instalar. |

## <a name="configuration-with-powershell"></a>Configuración con PowerShell
En el ejemplo siguiente, se usa PowerShell para configurar Aplicación de revisión automatizada en una máquina virtual de SQL Server existente. El comando **New-AzureVMSqlServerAutoPatchingConfig** configura una nueva ventana de mantenimiento para actualizaciones automáticas.

```azurepowershell
$aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM
```

Según este ejemplo, la siguiente tabla describe el efecto práctico en la máquina virtual de Azure de destino:

| Parámetro | Efecto |
| --- | --- |
| **DayOfWeek** |Las revisiones instaladas cada jueves. |
| **MaintenanceWindowStartingHour** |Inicia las actualizaciones a las 11:00 a.m. |
| **MaintenanceWindowDuration** |Las revisiones deben instalarse en un plazo de 120 minutos. Según la hora de inicio, deben haberse completado a las 1:00 p.m. |
| **PatchCategory** |La única configuración posible para este parámetro es “Importante”. |

La instalación y configuración del agente de Iaas de SQL Server puede tardar algunos minutos.

Para deshabilitar Aplicación de revisión automatizada, ejecute el script sin el parámetro -Enable en New-AzureVMSqlServerAutoPatchingConfig. Al igual que la instalación, la deshabilitación de Aplicación de revisión automatizada puede tardar algunos minutos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de otras tareas de automatización disponibles, consulte la [extensión Agente de IaaS de SQL Server](../classic/sql-server-agent-extension.md).

Para más información sobre cómo ejecutar SQL Server en Azure Virtual Machines, consulte [Introducción a SQL Server en Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

