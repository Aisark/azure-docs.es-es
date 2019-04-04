---
title: Concesión de acceso para crear suscripciones de Azure Enterprise | Microsoft Docs
description: Obtenga información sobre cómo conceder a un usuario o a una entidad de servicio la capacidad de crear mediante programación las suscripciones de Azure Enterprise.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 7a2397328f715dbf63246e8d4aaa789b5986b3b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112570"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concesión de acceso para crear suscripciones de Azure Enterprise (versión preliminar)

Como cliente de Azure del [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), puede conceder a otro usuario u otra entidad de servicio permisos para crear suscripciones facturadas en su cuenta. En este artículo, aprenderá a usar el [control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-configure.md) para compartir la capacidad de crear suscripciones y cómo auditar creaciones de suscripciones. Debe tener el rol de propietario en la cuenta que desea compartir.

Para crear una suscripción, vea [Creación de suscripciones de Azure Enterprise mediante programación (versión preliminar)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegación de acceso a una cuenta de inscripción mediante RBAC

Para conceder a otro usuario o entidad de servicio la capacidad para crear suscripciones con una cuenta específica, [asígneles un rol de propietario de RBAC en el ámbito de la cuenta de inscripción](../active-directory/role-based-access-control-manage-access-rest.md). En el ejemplo siguiente se asigna a un usuario del inquilino con `principalId` de `<userObjectId>` (para SignUpEngineering@contoso.com) un rol de propietario en la cuenta de inscripción. Para buscar el identificador de entidad y el identificador de la cuenta de inscripción, vea [Creación de suscripciones de Azure Enterprise mediante programación (versión preliminar)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

Cuando el rol de propietario se asigna correctamente en el ámbito de la cuenta de inscripción, Azure responde con información de la asignación de roles:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilice [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) para proporcionar a otro usuario acceso de propietario a la cuenta de inscripción.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) para conceder a otro usuario acceso de propietario a su cuenta de inscripción.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Cuando un usuario se convierte en propietario de RBAC de su cuenta de inscripción, puede crear suscripciones en ella mediante programación. Una suscripción creada por un usuario delegado aún tiene el propietario de cuenta original como administrador de servicios, pero también tiene el usuario delegado como propietario de forma predeterminada. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditoría que ha creado suscripciones con registros de actividad

Para realizar el seguimiento de las suscripciones creadas a través de esta API, use la [API de registro de actividad de inquilinos](/rest/api/monitor/tenantactivitylogs). Actualmente no es posible usar PowerShell, la CLI ni Azure Portal para realizar el seguimiento de la creación de suscripciones.

1. Como administrador de inquilinos del inquilino de Azure AD, [eleve los privilegios de acceso](../active-directory/role-based-access-control-tenant-admin-access.md) y asigne un rol de lector al usuario de auditoría con el ámbito `/providers/microsoft.insights/eventtypes/management`.
1. Como usuario de auditoría, llame a la [API de registro de actividad de inquilinos](/rest/api/monitor/tenantactivitylogs) para ver las actividades de creación de suscripciones. Ejemplo:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Para llamar cómodamente a esta API desde la línea de comandos, pruebe [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Pasos siguientes

* Ahora que el usuario o la entidad de servicio tienen permiso para crear una suscripción, puede usar dicha identidad para [crear suscripciones de Azure Enterprise mediante programación](programmatically-create-subscription.md).
* Para obtener un ejemplo sobre cómo crear suscripciones con. NET, vea [ejemplo de código en GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para obtener más información sobre Azure Resource Manager y sus API, vea [Información general de Azure Resource Manager](resource-group-overview.md).
* Para obtener más información sobre cómo administrar grandes cantidades de suscripciones mediante grupos de administración, consulte [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md).
* Para consultar una guía completa de procedimientos recomendados para grandes organizaciones sobre la gobernanza de suscripciones, vea [Scaffolding empresarial de Azure: gobernanza de suscripciones preceptiva](/azure/architecture/cloud-adoption-guide/subscription-governance)
