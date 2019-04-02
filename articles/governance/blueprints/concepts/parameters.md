---
title: Uso de parámetros para crear planos técnicos dinámicos
description: Conozca más información sobre los parámetros estáticos y dinámicos, y sobre como usarlos para crear planos técnicos dinámicos.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 538015f3cd26d451285f1fd66853366f814f3b0a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805579"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Creación de planos técnicos mediante parámetros

Un plano técnico totalmente definido con distintos artefactos (por ejemplo, grupos de recursos, plantillas de Resource Manager, directivas o asignaciones de roles) permite la creación rápida y el aprovisionamiento coherente de los objetos dentro de Azure. Para habilitar el uso flexible de estos patrones de diseño y contenedores reutilizables, Azure Blueprint admite parámetros. El parámetro crea flexibilidad durante la definición y la asignación lo cual permite cambiar las propiedades de los artefactos implementados por el plano técnico.

Un ejemplo sencillo es el artefacto de grupo de recursos. Cuando se crea un grupo de recursos se deben proporcionar dos valores obligatorios: nombre y ubicación. Al agregar un grupo de recursos a su plano técnico, si no existieran los parámetros tendría que definir ese nombre y ubicación para cada uso del plano técnico. Esta repetición haría que todos los usos del plano técnico crearan artefactos en el mismo grupo de recursos. Los recursos dentro de ese se duplicarían y se podría producir un conflicto.

> [!NOTE]
> No hay ningún problema en que dos planos técnicos distintos incluyan un grupo de recursos con el mismo nombre.
> Si un grupo de recursos incluido en un plano técnico ya existe, este sigue creando los artefactos correspondientes en ese grupo de recursos. Esto podría producir un conflicto ya que dos recursos con el mismo nombre y tipo de recurso no pueden existir dentro de una suscripción.

La solución a este problema son los parámetros. Los planos técnicos le permiten definir el valor para cada propiedad del artefacto durante la asignación a una suscripción. El parámetro permite reutilizar un plano técnico que crea un grupo de recursos y otros recursos en una suscripción única sin tener conflictos.

## <a name="blueprint-parameters"></a>Parámetros de plano técnico

Con la API REST, los parámetros se pueden crear en el propio plano técnico. Estos parámetros son diferentes de los de cada uno de los artefactos admitidos. Cuando se crea un parámetro en el plano técnico, los artefactos de este pueden usarlo. Un ejemplo podría ser el del prefijo para la asignación del nombre del grupo de recursos. El artefacto puede usar el parámetro del plano técnico para crear un parámetro "principalmente dinámico". Como el parámetro también se puede definir durante la asignación, este patrón permite una coherencia que puede cumplir las reglas de nomenclatura. Para más información sobre los pasos, consulte [configuración de parámetros estáticos: parámetro en el nivel del plano técnico](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Uso de los parámetros secureString y secureObject

Aunque un _artefacto_ de plantilla de Resource Manager admite parámetros de los tipos **secureString** y **secureObject**, Azure Blueprint requiere que cada uno esté conectado con una instancia de Azure Key Vault.
Esta medida de seguridad impide el procedimiento no seguro de almacenar secretos junto con el plano técnico y anima a la implementación de patrones seguros. Azure Blueprints admite esta medida de seguridad y detecta la inclusión de un parámetro seguro en un _artefacto_ de una plantilla de Resource Manager. El servicio entonces solicita durante la asignación las siguientes propiedades de Key Vault por cada parámetro seguro detectado:

- Identificador de recurso de Key Vault
- Nombre del secreto de Key Vault
- Versión del secreto de Key Vault

Si usa la asignación del plano técnico un **asignado por el sistema de identidad administrada**, hace referencia a Key Vault _debe_ existe en la misma suscripción que la definición del plano técnico está asignada a.

Si usa la asignación del plano técnico un **asignada por el usuario de la identidad administrada**, la hace referencia a Key Vault _puede_ existe en una suscripción centralizada. La identidad administrada debe tener los derechos correspondientes en el almacén de claves antes de la asignación del plano técnico.

En ambos casos, debe tener el almacén de claves **habilitar el acceso a Azure Resource Manager para la implementación de plantilla** configurado en el **las directivas de acceso** página. Para obtener instrucciones sobre cómo habilitar esta característica, consulte [Key Vault: Habilitar la implementación de plantillas](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Para más información sobre Azure Key Vault, consulte [Introducción a Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipos de parámetro

### <a name="static-parameters"></a>Parámetros estáticos

Un valor de parámetro definido en la definición de un plano técnico se denomina **parámetro estático**, ya que todos los usos del plano técnico implementarán el artefacto con ese valor estático. En el ejemplo del grupo de recursos, aunque esto no tenga sentido para el nombre del grupo de recursos, podría tenerlo para la ubicación. En ese caso, cada asignación del plano técnico crearía el grupo de recursos, sea cual sea el nombre que se le da durante la asignación, en la misma ubicación. Esta flexibilidad le permite seleccionar aquello que desea que sea obligatorio frente a aquello que se puede modificar durante la asignación.

#### <a name="setting-static-parameters-in-the-portal"></a>Establecimiento de parámetros estáticos en el portal

1. Seleccione **Todos los servicios** en el panel izquierdo. Busque y seleccione **Planos técnicos**.

1. Seleccione **Definiciones del plano técnico** en la página de la izquierda.

1. Haga clic en un proyecto existente y, a continuación, haga clic en **Editar plano** o haga clic en **+ crear instancia de blueprint** y rellene la información en el **Fundamentos** ficha.

1. Haga clic en **Siguiente: Artefactos** o haga clic en la pestaña **Artefactos**.

1. En los artefactos que se agregaron al plano técnico que tienen opciones de parámetro aparece **X of Y parameters populated** (X de Y parámetros rellenos) en la columna **Parámetros**. Haga clic en la fila del artefacto para editar los parámetros de este.

   ![Parámetros de plano técnico en una definición de plano técnico](../media/parameters/parameter-column.png)

1. En la página **Editar artefacto** aparecen las opciones de valor adecuadas para el artefacto en el que se hace clic. Cada parámetro del artefacto tiene un título, un cuadro de valor y una casilla. Desactive la casilla para hacer que sea un **parámetro estático**. En el ejemplo siguiente, solo la _ubicación_ es un **parámetro estático** ya que está desactivada y la opción _Nombre del grupo de recursos_ está activada.

   ![Instancia de blueprint parámetros estáticos en un artefacto de plano técnico](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Establecimiento de parámetros estáticos desde la API REST

En cada identificador URI de la API REST, hay variables usadas que se deben reemplazar por sus propios valores:

- `{YourMG}`: reemplácelo por el nombre del grupo de administración
- `{subscriptionId}`: reemplácelo por el identificador de suscripción

##### <a name="blueprint-level-parameter"></a>Parámetro en el nivel del plano técnico

Al crear un plano técnico mediante la API REST, es posible crear [parámetros de plano técnico](#blueprint-parameters). Para ello, utilice el siguiente formato de URI de la API REST y de cuerpo:

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Cuerpo de la solicitud

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Una vez que se cree un parámetro en el nivel del plano técnico, se puede usar en los artefactos que se agreguen a ese plano.
En el siguiente ejemplo de la API REST se crea un artefacto de asignación de roles en el plano técnico y se usa el parámetro en el nivel del plano técnico.

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Cuerpo de la solicitud

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

En este ejemplo, la propiedad **principalIds** usó el parámetro en el nivel de plano técnico **owners** con el valor `[parameters('owners')]`. Establecer un parámetro en un artefacto mediante un parámetro de nivel de plano técnico sigue siendo un ejemplo de **parámetro estático**. El parámetro de nivel de plano técnico no se puede establecer durante la asignación del plano técnico y será el mismo valor en cada asignación.

##### <a name="artifact-level-parameter"></a>Parámetro en el nivel de artefacto

La creación de **parámetros estáticos** en un artefacto es parecida, pero toma un valor directo en lugar de usar la función `parameters()`. El ejemplo siguiente crea dos parámetros estáticos, **tagName** y **tagValue**. El valor de cada uno se proporciona directamente y no utiliza una llamada de función.

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Cuerpo de la solicitud

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parámetros dinámicos

Lo contrario de un parámetro estático es un **parámetro dinámico**. Este parámetro no se define en el plano técnico, sino que se define durante cada asignación del mismo. En el ejemplo de grupo de recursos, el uso de un **parámetro dinámico** tiene sentido para el nombre del grupo de recursos. Proporciona un nombre diferente para cada asignación del plano técnico.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Establecimiento de parámetros dinámicos en el portal

1. Seleccione **Todos los servicios** en el panel izquierdo. Busque y seleccione **Planos técnicos**.

1. Seleccione **Definiciones del plano técnico** en la página de la izquierda.

1. Haga clic con el botón derecho clic en el plano técnico que desee asignar. Seleccione **asignar blueprint** o haga clic en el proyecto que desea asignar, a continuación, haga clic en el **asignar blueprint** botón.

1. En el **asignar blueprint** página, busque el **parámetros de artefacto** sección. Cada artefacto que tiene al menos un **parámetro dinámico** muestra el artefacto y las opciones de configuración. Proporcione los valores necesarios para los parámetros antes de asignar el plano técnico. En el ejemplo siguiente, _Name_ es un **parámetro dinámico** que se debe definir para completar la asignación del plano técnico.

   ![Parámetro dinámico del plano técnico durante la asignación del plano técnico](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Establecimiento de parámetros dinámicos desde la API REST

Para establecer **parámetros dinámicos** durante la asignación, debe escribir el valor directamente.
En lugar de usar una función, como `parameters()`, el valor proporcionado es una cadena adecuada.
Los artefactos para un grupo de recursos se definen con un "nombre de plantilla" y las propiedades **name** y **location**. Todos los demás parámetros para el artefacto incluido se definen en **parámetros** con un par de clave **\<name\>** y **value**. Si el plano técnico se configura para un parámetro dinámico que no se proporciona durante la asignación, se producirá un error en esta.

- URI DE LA API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Cuerpo de la solicitud

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [ciclo de vida del plano técnico](lifecycle.md)
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../how-to/update-existing-assignments.md).
- Puede consultar la información de [solución de problemas generales](../troubleshoot/general.md) para resolver los problemas durante la asignación de un plano técnico.