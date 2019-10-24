---
title: Errores de recurso primario de Azure | Microsoft Docs
description: Describe cómo resolver errores cuando se trabaja con un recurso primario en una plantilla de Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 197554e16e28b4928cab351838f00e1631c269fd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390248"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolución de errores para recursos primarios

Este artículo describe los errores que pueden producirse al implementar un recurso que depende de un recurso primario.

## <a name="symptom"></a>Síntoma

Al implementar un recurso que es un elemento secundario de otro recurso, puede recibir el error siguiente:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Cuando un recurso es un elemento secundario de otro recurso, el primario tiene que existir antes de crear el secundario. El nombre del recurso secundario define la conexión con el recurso primario. El nombre del recurso secundario tiene el formato `<parent-resource-name>/<child-resource-name>`. Por ejemplo, se podría definir una instancia de SQL Database como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Si implementa el servidor y la base de datos en la misma plantilla pero no especifica una dependencia en el servidor, puede iniciar la implementación de la base de datos antes de que implemente el servidor. 

Si el recurso primario ya existe y no se implementa en la misma plantilla, recibirá este error cuando Resource Manager no pueda asociar el recurso secundario con el primario. Este error se puede producir cuando no se encuentra el recurso secundario en el formato correcto, o si el recurso secundario se implementa en un grupo de recursos que es diferente del grupo de recursos del recurso primario.

## <a name="solution"></a>Solución

Para resolver este error cuando se implementan recursos primarios y secundarios en la misma plantilla, incluya una dependencia.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para resolver este error cuando el recurso primario se implementó previamente en una plantilla diferente, no establezca una dependencia. En su lugar, implemente el recurso secundario en el mismo grupo de recursos y proporcione el nombre del recurso primario.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Para más información consulte [Definición del orden de implementación de recursos en plantillas de Azure Resource Manager](resource-group-define-dependencies.md).