---
title: Administración del acceso de Azure Site Recovery con el control de acceso basado en rol (RBAC) | Microsoft Docs
description: En este artículo se describe cómo aplicar control de acceso basado en rol (RBAC) para administrar el acceso de Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471441"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Administración del acceso de Site Recovery con el control de acceso basado en rol (RBAC)

El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Con el uso de RBAC, puede separar las responsabilidades entre los miembros del equipo y conceder únicamente permisos de acceso específicos a los usuarios según sea necesario para realizar tareas específicas.

Azure Site Recovery proporciona 3 roles integrados para controlar las operaciones de administración de Site Recovery. Más información sobre [roles integrados del control de acceso basado en rol de Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador de Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor): este rol tiene todos los permisos necesarios para administrar las operaciones de Azure Site Recovery en un almacén de Recovery Services. Sin embargo, un usuario con este rol no puede crear ni eliminar un almacén de Recovery Services, ni tampoco asignar derechos de acceso a otros usuarios. Este rol es ideal para administradores de recuperación ante desastres que pueden habilitar y administrar la recuperación ante desastres para aplicaciones u organizaciones completas, según sea necesario.
* [Operador de Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator): este rol tiene permisos para ejecutar y administrar operaciones de conmutación por error y por recuperación. Un usuario con este rol no puede habilitar ni deshabilitar la replicación, crear ni eliminar almacenes, registrar nueva infraestructura ni asignar derechos de acceso a otros usuarios. Este rol es ideal para un operador de recuperación ante desastres que puede conmutar por error desde máquinas virtuales o aplicaciones cuando se lo indican los propietarios de las aplicaciones y los administradores de TI en una situación desastrosa real o simulada, como un simulacro de recuperación ante desastres. Tras la resolución del desastre, el operador de recuperación ante desastres puede volver a proteger las máquinas virtuales y realizar la conmutación por recuperación.
* [Lector de Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader): este rol tiene permisos para ver todas las operaciones de administración de Site Recovery. Este rol es ideal para un ejecutivo de supervisión de TI que puede supervisar el estado actual de protección y crear vales de soporte si es necesario.

Si quiere definir sus propios roles para tener un mayor control, consulte cómo [crear roles personalizados](../role-based-access-control/custom-roles.md) en Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permisos necesarios para habilitar la replicación de nuevas máquinas virtuales
Cuando una nueva máquina virtual se replica en Azure con Azure Site Recovery, los niveles de acceso del usuario asociado se validan para asegurarse de que el usuario tiene los permisos necesarios para usar los recursos de Azure proporcionados en Site Recovery.

Para habilitar la replicación de una nueva máquina virtual, el usuario debe tener:
* Permiso para crear una máquina virtual en el grupo de recursos seleccionado
* Permiso para crear una máquina virtual en la red virtual seleccionada
* Permiso para escribir en la cuenta de Storage seleccionada

Un usuario necesita los siguientes permisos para completar la replicación de una nueva máquina virtual.

> [!IMPORTANT]
>Asegúrese de que los permisos pertinentes se agregan según el modelo de implementación (Resource Manager o clásico) utilizado para la implementación de recursos.

| **Tipo de recurso** | **Modelo de implementación** | **Permiso** |
| --- | --- | --- |
| Proceso | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clásico | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Red | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clásico | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Almacenamiento | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clásico | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere el uso de los [roles integrados](../role-based-access-control/built-in-roles.md) "Colaborador de la máquina virtual" y "Colaborador de la máquina virtual clásica" respectivamente para la implementación según el modelo de Resource Manager o el clásico.

## <a name="next-steps"></a>Pasos siguientes
* [Control de acceso basado en rol](../role-based-access-control/role-assignments-portal.md): Puede comenzar con RBAC en Azure Portal.
* Aprenda a administrar el acceso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI de Azure](../role-based-access-control/role-assignments-cli.md)
  * [API DE REST](../role-based-access-control/role-assignments-rest.md)
* [Solución de problemas del control de acceso basado en rol](../role-based-access-control/troubleshooting.md): Obtenga sugerencias acerca de cómo solucionar problemas comunes.
