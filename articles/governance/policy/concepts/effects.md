---
title: Descripción del funcionamiento de los efectos
description: La definición de Azure Policy tiene varios efectos que determinan cómo se administra y notifica el cumplimiento.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 67a195932ad1afc3c93a94dfcbda8ab8a47760b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498823"
---
# <a name="understand-azure-policy-effects"></a>Comprender los efectos de Azure Policy

Cada definición de directiva en Azure Policy tiene un único efecto. Dicho efecto determina lo que ocurre cuando la regla de directivas se evalúa con la coincidencia. Los efectos se comportan de manera diferente si son para un nuevo recurso, un recurso actualizado o un recurso existente.

Actualmente, se admiten seis efectos en una definición de directiva:

- Append
- Auditoría
- AuditIfNotExists
- Denegar
- DeployIfNotExists
- Disabled

## <a name="order-of-evaluation"></a>Orden de evaluación

En primer lugar, Policy evalúa las solicitudes para crear o actualizar un recurso a través de Azure Resource Manager. Policy crea una lista de todas las asignaciones que se aplican al recurso y, a continuación, evalúa el recurso de acuerdo con cada definición. Policy procesa algunos de los efectos antes de entregar la solicitud al proveedor de recursos adecuado. De este modo, se evita que un proveedor de recursos realice un procesamiento innecesario cuando un recurso no cumple con los controles de gobernanza diseñados de Policy.

- Primero se selecciona **Deshabilitado** para determinar si se debe evaluar la regla de directivas.
- Luego se evalúa **Append**. Dado que append ya podría alterar la solicitud, un cambio realizado por append podría evitar la activación de un efecto audit o deny.
- Luego se evalúa **deny**. La evaluación de deny antes de audit impide el doble registro de un recurso no deseado.
- A continuación, se evalúa **audit** antes de que la solicitud vaya al proveedor de recursos.

Después de que el proveedor de recursos devuelva un código correcto, **AuditIfNotExists** y **DeployIfNotExists** se evalúan para determinar si es necesario realizar una acción o un registro de cumplimiento adicionales.

## <a name="disabled"></a>Disabled

Este efecto es útil para probar situaciones o cuando la definición de directiva ha parametrizado el efecto. Esta flexibilidad permite deshabilitar una única asignación en lugar de deshabilitar todas las asignaciones de la directiva.

## <a name="append"></a>Append

Append se utiliza para agregar campos adicionales al recurso solicitado durante la creación o actualización. Un ejemplo común consiste en agregar etiquetas en recursos como costCenter o especificar direcciones IP para un recurso de almacenamiento.

### <a name="append-evaluation"></a>Evaluación de append

Append realiza la evaluación antes de que un proveedor de recursos procese la solicitud durante la creación o actualización de un recurso. Append agrega campos al recurso cuando se cumple la condición **if** de la regla de directiva. En caso de que el efecto append reemplace un valor de la solicitud original por otro, actúa como un efecto deny y rechaza la solicitud. Para anexar un nuevo valor a una matriz existente, use la versión **[\*]** del alias.

Cuando una definición de directiva que utiliza el efecto append se ejecuta como parte de un ciclo de evaluación, no realiza cambios en los recursos que ya existen. En su lugar, marca cualquier recurso que cumple la condición **if** como no conforme.

### <a name="append-properties"></a>Propiedades de append

Un efecto append solo tiene una matriz **details**, que es necesaria. Como **details** es una matriz, puede tomar un único par **campo-valor** o varios. Consulte [estructura de definición](definition-structure.md#fields) para obtener la lista de campos aceptables.

### <a name="append-examples"></a>Ejemplos de append

Ejemplo 1: un único par **campo/valor** para anexar una etiqueta.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Ejemplo 2: varios pares **campo/valor** para anexar un conjunto de etiquetas.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Ejemplo 3: un único par **campo/valor** que usa un 
[alias](definition-structure.md#aliases) distinto de-**[\*]** con un **valor** de matriz para establecer reglas de IP en una cuenta de almacenamiento. Cuando el alias que no es **[\*]** es una matriz, el efecto anexa el **valor** como toda la matriz. Si la matriz ya existe, el conflicto ocasiona un evento de rechazo.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Ejemplo 4: un único par **campo/valor** que usa un [alias](definition-structure.md#aliases) **[\*]** con un **valor** de matriz para establecer reglas IP en una cuenta de almacenamiento. Mediante el uso del alias **[\*]**, el efecto anexa el **valor** a una matriz que es posible que ya exista. Si la matriz no existe aún, se creará.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Denegar

Deny se utiliza para evitar una solicitud de recursos que no coincida con los estándares definidos a través de una definición de directiva, así como para impedir que se produzca un error en la solicitud.

### <a name="deny-evaluation"></a>Evaluación de deny

Al crear o actualizar un recurso coincidente, deny evita la solicitud antes de que se envíe al proveedor de recursos. La solicitud se devuelve como un código `403 (Forbidden)`. En el portal, este estado de prohibido puede verse como un estado en la implementación que evitó la asignación de directiva.

Durante la evaluación de los recursos existentes, los recursos que coinciden con una definición de directiva deny se marcan como no compatibles.

### <a name="deny-properties"></a>Propiedades de deny

El efecto deny no tiene propiedades adicionales para su uso en la condición **then** de la definición de directiva.

### <a name="deny-example"></a>Ejemplo de deny

Ejemplo: uso del efecto deny.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditoría

Audit se utiliza para crear un evento de advertencia en el registro de actividad cuando se evalúa un recurso no compatible, pero no se detiene la solicitud.

### <a name="audit-evaluation"></a>Evaluación de audit

Audit es el último efecto que Policy comprueba durante la creación o actualización de un recurso. A continuación, Policy envía el recurso al proveedor de recursos. Audit funciona igual para una solicitud de recurso y un ciclo de evaluación. Policy agrega una operación `Microsoft.Authorization/policies/audit/action` al registro de actividad y marca el recurso como no compatible.

### <a name="audit-properties"></a>Propiedades de audit

El efecto audit no tiene propiedades adicionales para su uso en la condición **then** de la definición de directiva.

### <a name="audit-example"></a>Ejemplo de audit

Ejemplo: uso del efecto audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists habilita la auditoría en recursos que coinciden con la condición **if**, pero no tiene los componentes especificados en la propiedad **details** de la condición **then**.

### <a name="auditifnotexists-evaluation"></a>Evaluación de AuditIfNotExists

AuditIfNotExists se ejecuta después de que un proveedor de recursos haya operado con una solicitud de creación o actualización de recursos y haya devuelto un código de estado correcto. La auditoría se produce si no hay recursos relacionados o si los recursos definidos por **ExistenceCondition** no se evalúan como true. Policy agrega una operación `Microsoft.Authorization/policies/audit/action` al registro de actividad del mismo modo que el efecto audit. Cuando se desencadena, el recurso que cumple la condición **if** es el recurso que está marcado como no conforme.

### <a name="auditifnotexists-properties"></a>Propiedades de AuditIfNotExists

La propiedad **details** de los efectos de AuditIfNotExists tiene todas las subpropiedades que definen los recursos relacionados para coincidir.

- **Type** [obligatorio]
  - Especifica el tipo del recurso relacionado para coincidir.
  - Si **details.type** es un tipo de recurso debajo el **si** condición recursos, la directiva de consulta para los recursos de este **tipo** dentro del ámbito del recurso evaluado. Consultas de la directiva en caso contrario, dentro del mismo grupo de recursos que el recurso evaluado.
- **Nombre** (opcional)
  - Especifica el nombre exacto del recurso para coincidir y hace que la directiva recupere un recurso específico en lugar de todos los recursos del tipo especificado.
  - Cuando la condición de valores para **if.field.type** y **then.details.type** coinciden, a continuación, **nombre** se convierte en _requiere_ y debe ser `[field('name')]`. Sin embargo, un [auditar](#audit) efecto debe tenerse en cuenta en su lugar.
- **ResourceGroupName** (opcional)
  - Permite que la coincidencia del recurso relacionado provenga de un grupo de recursos diferente.
  - No se aplica si **type** es un recurso que estaría debajo del recurso de condición **if**.
  - El valor predeterminado es el grupo de recursos del recurso de la condición **if**.
- **ExistenceScope** (opcional)
  - Los valores permitidos son _Subscription_ y _ResourceGroup_.
  - Establece el ámbito de donde obtener el recurso relacionado para que coincida.
  - No se aplica si **type** es un recurso que estaría debajo del recurso de condición **if**.
  - Para _ResourceGroup_, se limitaría al grupo de recursos del recurso de la condición **if** o al grupo de recursos especificado en **ResourceGroupName**.
  - Para _Subscription_, se consulta toda la suscripción para el recurso relacionado.
  - El valor predeterminado es _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Si no se especifica, todo recurso relacionado de **type** cumple con el efecto y no desencadena la auditoría.
  - Utiliza el mismo lenguaje que la regla de directiva para la condición **if**, pero se evalúa individualmente en relación con cada recurso relacionado.
  - Si algún recurso relacionado coincidente se evalúa como true, se cumple la condición del efecto y no se desencadena la auditoría.
  - Puede utilizar [field()] para comprobar la equivalencia con los valores en la condición **if**.
  - Por ejemplo, podría usarse para validar que el recurso primario (en la condición **if**) está en la misma ubicación de recursos que el recurso relacionado coincidente.

### <a name="auditifnotexists-example"></a>Ejemplo de AuditIfNotExists

Ejemplo: evalúa Virtual Machines para determinar si existe la extensión Antimalware y luego audita cuando faltan.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Similar a AuditIfNotExists, DeployIfNotExists ejecuta una implementación de plantilla cuando se cumple la condición.

> [!NOTE]
> Las [plantillas anidadas](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) son compatibles con **deployIfNotExists**, pero las [plantillas vinculadas](../../../azure-resource-manager/resource-group-linked-templates.md) no son compatibles actualmente.

### <a name="deployifnotexists-evaluation"></a>Evaluación de DeployIfNotExists

DeployIfNotExists se ejecuta después de que un proveedor de recursos haya operado con una solicitud de creación o actualización de recursos y haya devuelto un código de estado correcto. La implementación de una plantilla se produce si no hay recursos relacionados o si los recursos definidos por **ExistenceCondition** no se evalúan como true.

Durante un ciclo de evaluación, las definiciones de directiva con un efecto DeployIfNotExists que coinciden con los recursos se marcan como no compatibles, pero no se realiza ninguna acción en dicho recurso.

### <a name="deployifnotexists-properties"></a>Propiedades de DeployIfNotExists

La propiedad **details** de los efectos de DeployIfNotExists tiene todas las subpropiedades que definen los recursos relacionados para coincidir y la implementación de plantilla para ejecutar.

- **Type** [obligatorio]
  - Especifica el tipo del recurso relacionado para coincidir.
  - Comienza tratando de recuperar un recurso debajo del recurso de condición **if** , luego consulta dentro del mismo grupo de recursos que el recurso de condición **if**.
- **Nombre** (opcional)
  - Especifica el nombre exacto del recurso para coincidir y hace que la directiva recupere un recurso específico en lugar de todos los recursos del tipo especificado.
  - Cuando la condición de valores para **if.field.type** y **then.details.type** coinciden, a continuación, **nombre** se convierte en _requiere_ y debe ser `[field('name')]`.
- **ResourceGroupName** (opcional)
  - Permite que la coincidencia del recurso relacionado provenga de un grupo de recursos diferente.
  - No se aplica si **type** es un recurso que estaría debajo del recurso de condición **if**.
  - El valor predeterminado es el grupo de recursos del recurso de la condición **if**.
  - Si se ejecuta una implementación de plantilla, se implementa en el grupo de recursos de este valor.
- **ExistenceScope** (opcional)
  - Los valores permitidos son _Subscription_ y _ResourceGroup_.
  - Establece el ámbito de donde obtener el recurso relacionado para que coincida.
  - No se aplica si **type** es un recurso que estaría debajo del recurso de condición **if**.
  - Para _ResourceGroup_, se limitaría al grupo de recursos del recurso de la condición **if** o al grupo de recursos especificado en **ResourceGroupName**.
  - Para _Subscription_, se consulta toda la suscripción para el recurso relacionado.
  - El valor predeterminado es _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Si no se especifica, todo recurso relacionado de **type** cumple con el efecto y no desencadena la implementación.
  - Utiliza el mismo lenguaje que la regla de directiva para la condición **if**, pero se evalúa individualmente en relación con cada recurso relacionado.
  - Si algún recurso relacionado coincidente se evalúa como true, se cumple la condición del efecto y no se desencadena la implementación.
  - Puede utilizar [field()] para comprobar la equivalencia con los valores en la condición **if**.
  - Por ejemplo, podría usarse para validar que el recurso primario (en la condición **if**) está en la misma ubicación de recursos que el recurso relacionado coincidente.
- **roleDefinitionIds** [obligatorio]
  - Esta propiedad debe incluir una matriz de cadenas que coinciden con el identificador de rol de control de acceso basado en rol accesible por la suscripción. Para obtener más información, vea [remediation - configure policy definition](../how-to/remediate-resources.md#configure-policy-definition) (corrección: configurar la definición de directiva).
- **DeploymentScope** (opcional)
  - Los valores permitidos son _Subscription_ y _ResourceGroup_.
  - Establece el tipo de implementación que se desencadena. La _Suscripción_ indica una [implementación a nivel de suscripción](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ indica una implementación en un grupo de recursos.
  - En la _Implementación_ debe especificarse una propiedad _location_ al usar las implementaciones de nivel de suscripción.
  - El valor predeterminado es _ResourceGroup_.
- **Deployment** [obligatorio]
  - Esta propiedad debe incluir la implementación de plantilla completa, ya que luego se pasaría a la API PUT `Microsoft.Resources/deployments`. Para más información, consulte [Deployments REST API](/rest/api/resources/deployments) (API REST de implementaciones).

  > [!NOTE]
  > Todas las funciones dentro de la propiedad **Deployment** se evalúan como componentes de la plantilla, no la directiva. La excepción es la propiedad **parameters** que pasa los valores de la directiva a la plantilla. El **valor** en esta sección bajo el nombre de un parámetro de plantilla se usa para realizar este paso de valor (vea _fullDbName_ en el ejemplo DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Ejemplo de DeployIfNotExists

Ejemplo: evalúa las bases de datos de SQL Server para determinar si está habilitado transparentDataEncryption. De lo contrario, se ejecuta una implementación para habilitarlo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Directivas de distribución en capas

Un recurso puede verse afectado por varias asignaciones. Estas asignaciones pueden estar en el mismo o diferentes ámbitos. Cada una de estas asignaciones también es probable que tenga un efecto diferente al definido. La condición y el efecto de cada directiva se evalúan por separado. Por ejemplo: 

- Directiva 1
  - Restringe la ubicación de recursos a "westus".
  - Se asigna a la suscripción A.
  - Efecto deny.
- Directiva 2
  - Restringe la ubicación de recursos a "eastus".
  - Se asigna al grupo de recursos B de la suscripción A.
  - Efecto audit.
  
Esta configuración produciría el resultado siguiente:

- Cualquier recurso que ya esté en el grupo de recursos B en "eastus" es compatible con la directiva 2 y no compatible con la directiva 1.
- Cualquier recurso que ya esté en el grupo de recursos B y que no esté en "eastus" no es compatible con la directiva 2 ni con la directiva 1 si no está en "westus".
- La directiva 1 deniega cualquier recurso nuevo en la suscripción A que no esté en "westus".
- Cualquier recurso nuevo en la suscripción A y en el grupo de recursos B en "westus"se crea y no es compatible con la directiva 2.

Si tanto la directiva 1 como la directiva 2 tienen efecto deny, la situación cambia a:

- Cualquier recurso que ya esté en el grupo de recursos B y no en "eastus" no es compatible con la directiva 2.
- Cualquier recurso que ya esté en el grupo de recursos B y no en "westus" no es compatible con la directiva 1.
- La directiva 1 deniega cualquier recurso nuevo en la suscripción A que no esté en "westus".
- Cualquier recurso nuevo que esté en el grupo de recursos B de la suscripción A se deniega.

Cada asignación se evalúa individualmente. Por lo tanto, no hay ninguna oportunidad de que un recurso se escape por diferencias en el ámbito. El resultado neto de la distribución en capas o superposición de directivas se considera **acumulativo más restrictivo**. Por ejemplo, si tanto la directiva 1 como la 2 tuviesen un efecto deny, las directivas en conflicto y superpuestas bloquearían un recurso. Si aún necesita que el recurso se cree en el ámbito de destino, revise las exclusiones en cada asignación para validar si las directivas adecuadas están incidiendo en los ámbitos adecuados.

## <a name="next-steps"></a>Pasos siguientes

- Puede consultar ejemplos en [Ejemplos de Azure Policy](../samples/index.md)
- Consulte la [Estructura de definición de directivas](definition-structure.md)
- Entender cómo se pueden [crear directivas mediante programación](../how-to/programmatically-create.md)
- Obtenga información sobre cómo [obtener datos de cumplimiento](../how-to/getting-compliance-data.md)
- Más información sobre cómo [corregir recursos no compatibles](../how-to/remediate-resources.md)
- En [Organización de los recursos con grupos de administración de Azure](../../management-groups/overview.md), obtendrá información sobre lo que es un grupo de administración.
