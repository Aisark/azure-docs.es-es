---
title: Enumeración de las asignaciones de denegación para recursos de Azure mediante Azure PowerShell | Microsoft Docs
description: Obtenga información sobre cómo enumerar los usuarios, grupos, entidades de servicio e identidades administradas a los que se les ha denegado el acceso a acciones específicas de recursos de Azure en ámbitos determinados mediante Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113594"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Enumeración de las asignaciones de denegación para recursos de Azure mediante Azure PowerShell

Las [asignaciones de denegación](deny-assignments.md) impiden que los usuarios realicen acciones concretas en recursos de Azure, aunque una asignación de roles les conceda acceso. En este artículo se describe cómo enumerar las asignaciones de denegación mediante Azure PowerShell.

> [!NOTE]
> No se pueden crear directamente asignaciones de denegación propias. Para obtener información sobre cómo se crean las asignaciones de denegación, vea [Asignaciones de denegación](deny-assignments.md).

## <a name="prerequisites"></a>Requisitos previos

Para obtener información sobre una asignación de denegación, debe tener lo siguiente:

- El permiso `Microsoft.Authorization/denyAssignments/read`, que se incluye en la mayoría de [roles integrados para los recursos de Azure](built-in-roles.md).
- [PowerShell en Azure Cloud Shell](/azure/cloud-shell/overview) o [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="list-deny-assignments"></a>Enumeración de asignaciones de denegación

### <a name="list-all-deny-assignments"></a>Enumeración de todas las asignaciones de denegación

Para enumerar todas las asignaciones de denegación para la suscripción actual, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Enumeración de asignaciones de denegación en un ámbito de grupo de recursos

Para enumerar todas las asignaciones en un ámbito de grupo de recursos, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Enumeración de asignaciones de denegación en un ámbito de suscripción

Para enumerar todas las asignaciones en un ámbito de suscripción, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Para obtener el identificador de suscripción, lo puede encontrar en la hoja **Suscripciones** de Azure Portal o bien puede usar [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Pasos siguientes

- [Descripción de las asignaciones de denegación para recursos de Azure](deny-assignments.md)
- [Enumeración de las asignaciones de denegación para recursos de Azure mediante Azure Portal](deny-assignments-portal.md)
- [Enumeración de las asignaciones de denegación para recursos de Azure mediante la API de REST](deny-assignments-rest.md)