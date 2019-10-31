---
title: Cambios en la documentación de SQL Server en Azure Virtual Machines | Microsoft Docs
description: Conozca las nuevas características y mejoras de SQL Server en una máquina virtual de Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: 599dd2b44f934bb864acfa9ffe8d03fa0e8bb850
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757463"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Cambios en la documentación de SQL Server en Azure Virtual Machines

Azure permite implementar una máquina virtual (VM) con una imagen de SQL Server integrada. En este artículo se resumen los cambios en la documentación asociados a las nuevas características y mejoras de las versiones recientes de [SQL Server en Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>Octubre de 2019

| Cambios | Detalles |
| --- | --- |
| **Configuración del almacenamiento optimizado para el rendimiento** | Ya se puede [personalizar totalmente la configuración del almacenamiento](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) al crear una nueva máquina virtual con SQL Server. |
| **Recursos compartidos de archivos premium para FCI** | Ahora puede crear una instancia de clúster de conmutación por error mediante un [recurso compartido de archivos Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) en lugar del método original de [Espacios de almacenamiento directo](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Agosto de 2019

| Cambios | Detalles |
| --- | --- |
| **Host dedicado de Azure** | Puede ejecutar VM con SQL Server en un [host dedicado de Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Julio de 2019


| Cambios | Detalles |
| --- | --- |
| **Traslado de una máquina virtual de SQL a otra región** | Use Azure Site Recovery para [migrar las máquinas virtuales con SQL Server de una región de Azure a otra](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Junio de 2019


| Cambios | Detalles |
| --- | --- |
| **Nuevos modos de instalación de IaaS de SQL** | Ahora es posible instalar la extensión IaaS de SQL Server en [modo ligero](virtual-machines-windows-sql-server-agent-extension.md) para evitar tener que reiniciar el servicio SQL Server.  |
| **Modificación de la edición de SQL Server** | Ahora puede cambiar la [propiedad edition](virtual-machines-windows-sql-change-edition.md) de la VM con SQL Server. |
| **Cambios con el proveedor de recursos de máquina virtual con SQL** | Puede [registrar la máquina virtual con SQL Server con el proveedor de recursos de máquina virtual con SQL](virtual-machines-windows-sql-register-with-resource-provider.md) mediante los nuevos modos de IaaS de SQL. Esta funcionalidad incluye [imágenes de Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).|
| **Imágenes del tipo "traiga su propia licencia" mediante la Ventaja híbrida de Azure** | Ahora es posible cambiar al [tipo de licencia de pago por uso](virtual-machines-windows-sql-ahb.md#remarks) las imágenes del tipo "traiga su propia licencia" implementadas desde Azure Marketplace.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Mayo de 2019

| Cambios | Detalles |
| --- | --- |
| **Nueva administración de máquinas virtuales con SQL Server en Azure Portal** | Ahora existe una nueva forma de administrar una máquina virtual con SQL Server en Azure Portal. Para más información, consulte [Administración de máquinas virtuales con SQL Server en Azure Portal](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>Abril de 2019

| Cambios | Detalles |
| --- | --- |
| **Compatibilidad ampliada con SQL Server 2008 y SQL Server 2008 R2** | [Amplíe la compatibilidad](virtual-machines-windows-sql-server-2008-eos-extend-support.md) con SQL Server 2008 y SQL Server 2008 R2 mediante la migración *tal cual* a una máquina virtual de Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Marzo de 2019

| Cambios | Detalles |
| --- | --- |
| **Compatibilidad de imágenes personalizada** | Ahora puede instalar la [extensión IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) en imágenes de SQL y sistemas operativos personalizadas, lo que ofrece una funcionalidad limitada de [licencias flexibles](virtual-machines-windows-sql-ahb.md). Al registrar una imagen personalizada con el proveedor de recursos SQL, especifique el tipo de licencia como "AHUB". De lo contrario, el registro no podrá realizarse. | 
| **Compatibilidad de instancias con nombre** | Ahora puede usar la [extensión IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) con una instancia con nombre, si la instancia predeterminada se ha desinstalado correctamente. | 
| **Renovación del portal** | La experiencia de Azure Portal para implementar una VM con SQL Server se ha renovado para mejorar la facilidad de uso. Para más información, vea el breve [inicio rápido](quickstart-sql-vm-create-portal.md) y la guía [paso a paso](virtual-machines-windows-portal-sql-server-provision.md) detallada para implementar una máquina virtual con SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Febrero de 2019

| Cambios | Detalles |
| --- | --- |
| **Mejora del portal** | Ahora es posible cambiar el modelo de licencias para una máquina virtual con SQL Server del tipo "pago por uso" a "traiga su propia licencia" mediante [Azure Portal](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Simplificación de la implementación del grupo de disponibilidad en máquinas virtuales con SQL Server con la CLI de Azure** | Ahora es más fácil que nunca implementar un grupo de disponibilidad en una máquina virtual con SQL Server en Azure. Puede usar la [CLI de Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para crear el clúster de conmutación por error de Windows, el equilibrador de carga interno y los clientes de escucha de grupo de disponibilidad, todo ello desde la línea de comandos. Para más información, vea cómo [usar la CLI de Azure para configurar un grupo de disponibilidad Always On para SQL Server en una máquina virtual de Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>Diciembre de 2018

| Cambios | Detalles |
| --- | --- |
| **Nuevo proveedor de recursos para un clúster de SQL Server** | Un nuevo proveedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) que define los metadatos del clúster de conmutación por error de Windows. Al unir una máquina virtual con SQL Server a *SqlVirtualMachineGroups*, se arranca el servicio Clúster de conmutación por error de Windows Server (WSFC) y la máquina virtual se une al clúster.  |
|**Configuración automatizada de la implementación de un grupo de disponibilidad con plantillas de inicio rápido de Azure** |Ahora es posible crear el clúster de conmutación por error de Windows, unirlo a las máquinas virtuales con SQL Server, crear el cliente de escucha y configurar el equilibrador de carga interno con dos plantillas de inicio rápido de Azure. Para más información, consulte [Uso de plantillas de inicio rápido de Azure para configurar un grupo de disponibilidad Always On para SQL Server en una máquina virtual de Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registro automático del proveedor de recursos de máquina virtual con SQL Server** | Las VM con SQL Server implementadas después de este mes se registran automáticamente con el nuevo proveedor de recursos de SQL Server. Las máquinas virtuales con SQL Server implementadas antes de este mes todavía deben registrarse manualmente. Para más información, consulte [Registro de una máquina virtual con SQL Server en Azure con el proveedor de recursos de máquina virtual con SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>Noviembre de 2018

| Cambios | Detalles |
| --- | --- |
| **Nuevo proveedor de recursos de VM con SQL** |  Un nuevo proveedor de recursos (Microsoft.SqlVirtualMachine) proporciona una mejor administración de las máquinas virtuales con SQL Server. Para más información sobre cómo registrar las máquinas virtuales, consulte [Registro de una máquina virtual con SQL Server en Azure con el proveedor de recursos de máquina virtual con SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Cambiar el modelo de licencia** | Ahora puede cambiar entre los modelos de "pago por uso" y "traiga su propia licencia" para la máquina virtual con SQL Server mediante PowerShell o la CLI de Azure. Para más información, consulte [Modificación del modelo de licencia para una máquina virtual con SQL Server en Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Recursos adicionales

**Máquinas virtuales Windows**:

* [Introducción a SQL Server en máquinas virtuales Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionamiento de una máquina virtual Windows con SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Directrices de rendimiento para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Estrategias de desarrollo y patrones de aplicación de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Máquinas virtuales Linux**:

* [Introducción a SQL Server en máquinas virtuales Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionamiento de una máquina virtual Linux con SQL Server en Azure Portal](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Preguntas más frecuentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
