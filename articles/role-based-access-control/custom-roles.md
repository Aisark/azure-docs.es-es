---
title: Roles personalizados en los recursos de Azure | Microsoft Docs
description: Obtenga información sobre cómo crear roles personalizados con el control de acceso basado en roles (RBAC) para la administración de acceso específico de recursos de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24c8dd49667a359bb0fe7051dd801062f37f3db9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718435"
---
# <a name="custom-roles-for-azure-resources"></a>Roles personalizados en los recursos de Azure

Si los [roles integrados para los recursos de Azure](built-in-roles.md) no cumplen las necesidades específicas de su organización, puede crear sus propios roles personalizados. Igual que los roles integrados, puede asignar roles personalizados a usuarios, grupos y entidades de servicio en los ámbitos de suscripción, grupo de recursos y recurso. Los roles personalizados se almacenan en un directorio de Azure Active Directory (Azure AD) y se pueden compartir entre suscripciones. Cada directorio puede tener hasta 2000 roles personalizados. Se pueden crear roles personalizados con Azure PowerShell, la CLI de Azure o la API REST.

## <a name="custom-role-example"></a>Ejemplo de rol personalizado

A continuación se muestra el aspecto de un rol personalizado en formato JSON. Este rol personalizado puede usarse para supervisar y reiniciar máquinas virtuales.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Después de crear un rol personalizado, aparece en Azure Portal con un icono de recurso naranja.

![Icono de rol personalizado](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Pasos para crear un rol personalizado

1. Decida cómo desea crear el rol personalizado

    Puede crear roles personalizados con [Azure PowerShell](custom-roles-powershell.md), [CLI de Azure](custom-roles-cli.md), o el [API de REST](custom-roles-rest.md).

1. Determinar los permisos que necesita

    Cuando crea un rol personalizado, debe conocer las operaciones del proveedor de recursos que están disponibles para definir los permisos. Para ver la lista de operaciones, consulte el [operaciones del proveedor de recursos de Azure Resource Manager](resource-provider-operations.md). Va a agregar las operaciones a la `Actions` o `NotActions` propiedades de la [definición de función](role-definitions.md). Si dispone de las operaciones de datos, agregará los de la `DataActions` o `NotDataActions` propiedades.

1. Creación del rol personalizado

    Por lo general, empiece con un rol integrado existente y, a continuación, modifíquelo según sus necesidades. A continuación, use los comandos [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) o [az role definition create](/cli/azure/role/definition#az-role-definition-create) para crear el rol personalizado. Para crear un rol personalizado, debe tener el permiso `Microsoft.Authorization/roleDefinitions/write` en todos los `AssignableScopes`, como [Propietario](built-in-roles.md#owner) o [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator).

1. Probar el rol personalizado

    Una vez que tenga el rol personalizado, tiene que probarlo para comprobar que funciona según lo esperado. Si tiene que realizar ajustes más adelante, puede actualizar el rol personalizado.

Para ver un tutorial paso a paso sobre cómo crear un rol personalizado, consulte [Tutorial: Creación de un rol personalizado con Azure PowerShell](tutorial-custom-role-powershell.md) o [Tutorial: Creación de un rol personalizado con la CLI de Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propiedades del rol personalizado

Un rol personalizado tiene las siguientes propiedades.

| Propiedad | Obligatorio | Type | DESCRIPCIÓN |
| --- | --- | --- | --- |
| `Name` | Sí | string | Nombre para mostrar del rol personalizado. Aunque una definición de roles es un recurso de nivel de suscripción, una definición de roles se puede usar en varias suscripciones que compartan el mismo directorio de Azure AD. Este nombre para mostrar debe ser único en el ámbito del directorio de Azure AD. Puede incluir letras, números, espacios y caracteres especiales. El número máximo de caracteres es 128. |
| `Id` | Sí | string | El identificador exclusivo del rol personalizado. Para Azure PowerShell y la CLI de Azure, este identificador se genera automáticamente cuando se crea un rol. |
| `IsCustom` | Sí | string | Indica si es un rol personalizado. Establecido en `true` para los roles personalizados. |
| `Description` | Sí | string | Descripción del rol personalizado. Puede incluir letras, números, espacios y caracteres especiales. El número máximo de caracteres es 1024. |
| `Actions` | Sí | String[] | Matriz de cadenas que especifica las operaciones de administración que el rol permite realizar. Para obtener más información, consulte [Actions](role-definitions.md#actions). |
| `NotActions` | Sin  | String[] | Matriz de cadenas que especifica las operaciones de administración que se excluyen de las `Actions` permitidas. Para obtener más información, consulte [notActions](role-definitions.md#notactions). |
| `DataActions` | Sin  | String[] | Matriz de cadenas que especifica las operaciones de datos que el rol permite realizar en los datos dentro de ese objeto. Para obtener más información, consulte [dataActions (versión preliminar)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Sin  | String[] | Matriz de cadenas que especifica las operaciones de datos que se excluyen de las `DataActions` permitidas. Para obtener más información, consulte [notDataActions (versión preliminar)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Sí | String[] | Matriz de cadenas que especifica los ámbitos en los que el rol personalizado está disponible para la asignación. Actualmente, no puede establecerse en el ámbito raíz (`"/"`) o un ámbito de grupo de administración. Para más información, consulte [AssignableScopes](role-definitions.md#assignablescopes) y [Organización de los recursos con grupos de administración de Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quién puede crear, eliminar, actualizar o ver un rol personalizado

Al igual que los roles integrados, la propiedad `AssignableScopes` especifica los ámbitos en los que el rol está disponible para la asignación. La propiedad `AssignableScopes` de un rol personalizado también controla quién puede crear, eliminar, actualizar o ver dicho rol.

| Tarea | Operación | DESCRIPCIÓN |
| --- | --- | --- |
| Creación o eliminación de un rol personalizado | `Microsoft.Authorization/ roleDefinitions/write` | Los usuarios que tienen acceso a esta operación en todos los ámbitos `AssignableScopes` del rol personalizado pueden crear (o eliminar) roles personalizados para usar en esos ámbitos. Por ejemplo, los [propietarios](built-in-roles.md#owner) y [administradores del acceso de los usuarios](built-in-roles.md#user-access-administrator) de las suscripciones, los grupos de recursos y los recursos. |
| Actualización de un rol personalizado | `Microsoft.Authorization/ roleDefinitions/write` | Los usuarios que tienen acceso a esta operación en todos los ámbitos `AssignableScopes` del rol personalizado pueden actualizar roles personalizados en esos ámbitos. Por ejemplo, los [propietarios](built-in-roles.md#owner) y [administradores del acceso de los usuarios](built-in-roles.md#user-access-administrator) de las suscripciones, los grupos de recursos y los recursos. |
| Visualización de un rol personalizado | `Microsoft.Authorization/ roleDefinitions/read` | Los usuarios que tienen acceso a esta operación en un ámbito pueden ver los roles personalizados que están disponibles para su asignación en ese ámbito. Todos los roles integrados permiten que los roles personalizados estén disponibles para la asignación. |

## <a name="next-steps"></a>Pasos siguientes
- [Creación de roles personalizados para los recursos de Azure con Azure PowerShell](custom-roles-powershell.md)
- [Creación de roles personalizados para los recursos de Azure con la CLI de Azure](custom-roles-cli.md)
- [Descripción de definiciones de roles para los recursos de Azure](role-definitions.md)
- [Solución de problemas del control de acceso basado en rol para recursos de Azure](troubleshooting.md)
