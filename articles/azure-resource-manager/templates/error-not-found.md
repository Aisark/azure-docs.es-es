---
title: Errores de recurso no encontrado
description: Describe cómo resolver errores cuando un recurso no se puede encontrar al implementarse con una plantilla de Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773259"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolver errores de recursos de Azure no encontrados

En este artículo se describen los errores que pueden encontrar cuando no se encuentra un recurso durante la implementación.

## <a name="symptom"></a>Síntoma

Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibe un error similar al siguiente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si usa las funciones [reference](template-functions-resource.md#reference) o [listKeys](template-functions-resource.md#listkeys) con un recurso que no se puede resolver, recibirá el error siguiente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Resource Manager necesita recuperar las propiedades de un recurso, pero no puede identificar el recurso en su suscripción.

## <a name="solution-1---set-dependencies"></a>Solución 1: Establecimiento de dependencias

Si está tratando de implementar el recurso que falta en la plantilla, compruebe si tiene que agregar una dependencia. Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro, debe usar el elemento **dependsOn** de la plantilla. Por ejemplo, al implementar una aplicación web, debe existir el plan de App Service. Si no ha especificado que la aplicación web dependa del plan de App Service, Resource Manager crea ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de App Service, porque aún no existe cuando se intenta establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

No obstante, desea evitar configurar dependencias que no sean necesarias. Cuando hay dependencias innecesarias, prolonga la duración de la implementación al impedir que los recursos que no son dependientes entre sí se implementen en paralelo. Además, puede crear dependencias circulares que bloqueen la implementación. La función [reference](template-functions-resource.md#reference) y [list*](template-functions-resource.md#list) crea una dependencia implícita del recurso al que hace referencia, cuando este se implementa en la misma plantilla y se hace referencia a él con su nombre (no el identificador del recurso). Por lo tanto, puede tener más dependencias que las especificadas en la propiedad **dependsOn**. La función [resourceId](template-functions-resource.md#resourceid) no crea una dependencia implícita ni valida que el recurso existe. Las funciones [reference](template-functions-resource.md#reference) y [list*](template-functions-resource.md#list) no crean una dependencia implícita cuando el recurso se conoce por su identificador de recurso. Para crear una dependencia implícita, pase el nombre del recurso que se implementa en la misma plantilla.

Cuando se encuentre con problemas de dependencia, debe comprender mejor el orden de la implementación de recursos. Para ver el orden de las operaciones de implementación:

1. Seleccione el historial de implementaciones para el grupo de recursos.

   ![seleccionar el historial de implementaciones](./media/error-not-found/select-deployment.png)

2. Seleccione una implementación del historial y seleccione **Eventos**.

   ![seleccionar eventos de implementación](./media/error-not-found/select-deployment-events.png)

3. Examine la secuencia de eventos para cada recurso. Preste atención para el estado de cada operación. Por ejemplo, la siguiente imagen muestra tres cuentas de almacenamiento que se implementan en paralelo. Tenga en cuenta que las tres cuentas de almacenamiento se inician al mismo tiempo.

   ![implementación paralela](./media/error-not-found/deployment-events-parallel.png)

   La siguiente imagen muestra tres cuentas de almacenamiento que no se implementan en paralelo. La segunda cuenta de almacenamiento depende de la primera cuenta de almacenamiento y la tercera cuenta de almacenamiento depende de la segunda cuenta de almacenamiento. La primera cuenta de almacenamiento se inicia, se acepta y se completa antes de que se inicie la siguiente.

   ![implementación secuencial](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solución 2: Obtención de recursos de otro grupo de recursos

Si el recurso existe en un grupo de recursos diferente al que se va a implementar, utilice la [función resourceId](template-functions-resource.md#resourceid) para obtener el nombre completo del recurso.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solución 3: Comprobación de la función de referencia

Busque una expresión que incluya la función [reference](template-functions-resource.md#reference). Los valores que proporcione varían en función de si el recurso se está en la misma plantilla, grupo de recursos y suscripción. Compruebe que está proporcionando los valores de parámetro necesarios para su escenario. Si el recurso está en otro grupo de recursos, proporcione el identificador de recurso completo. Por ejemplo, para hacer referencia a una cuenta de almacenamiento en otro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solución 4: Obtención de una identidad administrada del recurso

Si va a implementar un recurso que crea implícitamente una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md), debe esperar hasta que ese recurso se implemente antes de recuperar los valores de la identidad administrada. Si pasa el nombre de la identidad administrada a la función [reference](template-functions-resource.md#reference), Resource Manager intenta resolver la referencia antes de implementar el recurso y la identidad. En su lugar, pase el nombre del recurso al que se aplica la identidad. Este enfoque garantiza que el recurso y la identidad administrada se implementan antes de que Resource Manager resuelva la función reference.

En la función reference, use `Full` para obtener todas las propiedades, incluida la identidad administrada.

Por ejemplo, para obtener el identificador de inquilino de una identidad administrada que se aplica a un conjunto de escalado de máquinas virtuales, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```