---
title: Cambio del modelo de licencia de una VM con SQL Server en Azure
description: Aprenda a cambiar de licencia en una máquina virtual con SQL Server en Azure de la modalidad de "pago por uso" a "traiga su propia licencia" con la Ventaja híbrida de Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 00262b48b8fa2fd1292554155e8ec8e933d886e6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690908"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Cambio del modelo de licencia en una máquina virtual con SQL Server en Azure
En este artículo se describe cómo cambiar el modelo de licencia de una máquina virtual con SQL Server en Azure mediante el nuevo proveedor de recursos de máquina virtual con SQL, **Microsoft.SqlVirtualMachine**.

Existen dos modelos de licencia para una máquina virtual que hospeda SQL Server: pago por uso y Ventaja híbrida de Azure. Puede modificar el modelo de licencia de la VM con SQL Server mediante Azure Portal, la CLI de Azure o PowerShell. 

El modelo de pago por uso significa que el costo por segundo de ejecutar la máquina virtual de Azure incluye el costo de la licencia de SQL Server.
La [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) le permite usar su propia licencia de SQL Server con una máquina virtual que ejecuta SQL Server. 

La Ventaja híbrida de Azure permite el uso de licencias de SQL Server con Software Assurance ("Licencia calificada") en Azure Virtual Machines. Con la Ventaja híbrida de Azure, los clientes no pagan por el uso de una licencia de SQL Server en una máquina virtual. Sin embargo, deben pagar por el costo del proceso en la nube subyacente (es decir, la tasa base), el almacenamiento y las copias de seguridad. También deben pagar por la E/S asociada a su uso de los servicios (según corresponda).

Según los términos de los productos de Microsoft: "Los clientes deben indicar que usan Azure SQL Database (Instancia administrada, Grupo elástico y Base de datos única), Azure Data Factory, SQL Server Integration Services o Máquinas virtuales de SQL Server en la Ventaja híbrida de Azure para SQL Server al configurar cargas de trabajo en Azure".

Existen tres opciones para indicar el uso de la Ventaja híbrida de Azure para SQL Server en una máquina virtual de Azure y que es compatible:

- Aprovisionar una máquina virtual mediante una imagen de SQL Server de "traiga su propia licencia" desde Azure Marketplace. Esta opción solo está disponible para los clientes con un Contrato Enterprise.
- Aprovisionar una máquina virtual con una imagen de SQL Server de "pago por uso" desde Azure Marketplace y activar la Ventaja híbrida de Azure
- Instalar automáticamente SQL Server en una máquina virtual de Azure, [registrar la VM con el proveedor de recursos de SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) manualmente y activar la Ventaja híbrida de Azure.

El tipo de licencia de SQL Server se establece cuando se aprovisiona la máquina virtual. Puede cambiarlo en cualquier momento con posterioridad. El cambio entre los modelos de licencia no genera ningún tiempo de inactividad, no reinicia la máquina virtual ni el servicio SQL Server, no agrega ningún costo adicional y es efectivo inmediatamente. De hecho, activar la Ventaja híbrida de Azure *reduce* el costo.

## <a name="prerequisites"></a>Prerequisites

El cambio del modelo de licencia de la máquina virtual con SQL Server tiene los siguientes requisitos: 

- Una [suscripción de Azure](https://azure.microsoft.com/free/).
- Una [máquina virtual con SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrada con el [proveedor de recursos de máquina virtual con SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) es un requisito para usar [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Cambio de la licencia para máquinas virtuales ya registradas con el proveedor de recursos 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

El modelo de licencia se puede modificar directamente desde el portal: 

1. Abra [Azure Portal](https://portal.azure.com) y el [recurso de las máquinas virtuales de SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) para su VM con SQL Server. 
1. Seleccione **Configurar** en **Configuración**. 
1. Seleccione la opción **Ventaja híbrida de Azure** y active la casilla para confirmar que tiene una licencia de SQL Server con Software Assurance. 
1. Seleccione **Aplicar** en la parte inferior de la página **Configurar**. 

![Ventaja híbrida de Azure en el portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar la CLI de Azure para cambiar el modelo de licencia.  

El siguiente fragmento de código cambia el modelo de licencia de pago por uso a traiga su propia licencia (o mediante la Ventaja híbrida de Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

El fragmento de código siguiente cambia el modelo de traiga su propia licencia a pago por uso: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Puede usar PowerShell para cambiar el modelo de licencia.

El siguiente fragmento de código cambia el modelo de licencia de pago por uso a traiga su propia licencia (o mediante la Ventaja híbrida de Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

El fragmento de código siguiente cambia el modelo de traiga su propia licencia a pago por uso:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Cambio de la licencia para máquinas virtuales no registradas con el proveedor de recursos

Si aprovisionó una VM con SQL Server a partir de imágenes de Azure Marketplace de "pago por uso", el tipo de licencia de SQL Server será de pago por uso. Si aprovisionó una VM con SQL Server con una imagen de "traiga su propia licencia" desde Azure Marketplace, el tipo de licencia será AHUB. Todas las máquinas virtuales con SQL Server aprovisionadas a partir de las imágenes predeterminadas (pago por uso) o de imágenes de Azure Marketplace de "traiga su propia licencia" se registrarán automáticamente con el proveedor de recursos de máquina virtual con SQL, para que puedan cambiar el [tipo de licencia](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Solo puede instalar automáticamente SQL Server en una máquina virtual de Azure mediante la Ventaja híbrida de Azure. [Estas máquinas virtuales se deben registrar con el proveedor de recursos de máquina virtual de SQL](virtual-machines-windows-sql-register-with-resource-provider.md) mediante el establecimiento de la licencia de SQL Server como Ventaja híbrida de Azure, para indicar el uso de este modo de acuerdo con los términos de los productos de Microsoft.

Solo puede cambiar el tipo de licencia de una máquina virtual con SQL Server que sea de pago por uso o Ventaja híbrida de Azure si dicha máquina virtual está registrada con el proveedor de recursos de máquina virtual con SQL.

## <a name="remarks"></a>Observaciones

- Para que los clientes del Proveedor de soluciones en la nube de Azure (CSP) puedan usar la Ventaja híbrida de Azure, deben implementar primero una máquina virtual de "pago por uso" y, después, convertirla al modo "traiga su propia licencia" si tienen una instancia activa de Software Assurance.
- Si coloca el recurso de VM con SQL Server, volverá al valor de la licencia codificada de forma rígida de la imagen. 
- La posibilidad de cambiar el modelo de licencia es una característica del proveedor de recursos de máquina virtual de SQL. Al implementar una imagen de Azure Marketplace desde Azure Portal, se registra automáticamente una VM con SQL Server en el proveedor de recursos. Sin embargo, los clientes que instalen automáticamente SQL Server deberán [registrar su VM con SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) de forma manual. 
- Para agregar una VM con SQL Server a un conjunto de disponibilidad, es necesario volver a crear la máquina virtual. Por tanto, todas las máquinas virtuales que se agreguen a un conjunto de disponibilidad volverán a tener el tipo de licencia predeterminado de pago por uso. También será necesario habilitar de nuevo la Ventaja híbrida de Azure. 


## <a name="limitations"></a>Limitaciones

El cambio del modelo de licencia:
   - Solo está disponible para los clientes que cuentan con [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Solo se admite en las ediciones Standard Edition y Enterprise Edition de SQL Server. No se admiten los cambios de licencia para Express, Web ni Developer. 
   - Solo se admite en máquinas virtuales implementadas mediante el modelo de Azure Resource Manager. No se admiten las máquinas virtuales implementadas con el modelo clásico. 
   - Solo está disponible para las nubes públicas o Azure Government. 
   - Solo se admite en máquinas virtuales que tienen una sola interfaz de red (NIC). 


## <a name="known-errors"></a>Errores conocidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>No se encontró el recurso "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>" en el grupo de recursos "\<resource-group>".

Este error se produce al intentar cambiar el modelo de licencia en una máquina virtual con SQL Server que no se ha registrado con el proveedor de recursos de máquina virtual con SQL.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Tendrá que registrar la suscripción con el proveedor de recursos y, después, [registrar la máquina virtual de SQL Server con el proveedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>La máquina virtual "\<vmname\>" tiene más de una NIC asociada

Este error se produce en máquinas virtuales que tienen más de una NIC. Quite una de las NIC antes de cambiar el modelo de licencia. Aunque puede volver a agregar la NIC a la máquina virtual después de cambiar el modelo de licencia, ya no se admitirán operaciones en Azure Portal como la copia de seguridad y la revisión automáticas. 


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos. 

* [Introducción a SQL Server en máquinas virtuales Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Preguntas más frecuentes de SQL Server en máquinas virtuales Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientación de precios de SQL Server para máquinas virtuales de Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de la versión de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-release-notes.md)


