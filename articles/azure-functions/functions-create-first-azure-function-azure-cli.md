---
title: Creación de su primera función con la CLI de Azure
description: Obtenga información sobre cómo crear su primera función de Azure para su ejecución sin servidor mediante la CLI de Azure y Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 625fcda4b612fdeda940f9219238ac79354dd056
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444665"
---
# <a name="create-your-first-function-from-the-command-line"></a>Creación de su primera función desde la línea de comandos

Este tema de inicio rápido lo guía a través de la creación de la primera función desde la línea de comandos o el terminal. Use la CLI de Azure para crear una aplicación de función, que será la infraestructura [sin servidor](https://azure.microsoft.com/solutions/serverless/) que hospedará la función. El proyecto de código de función se genera a partir de una plantilla mediante el uso de [Azure Functions Core Tools](functions-run-local.md), que también se usa para implementar el proyecto de aplicación de función en Azure.

Puede seguir los pasos siguientes con un equipo Mac, Windows o Linux.

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, debe tener lo siguiente:

+ Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versión 2.6.666 u otra posterior.

+ Instale la [CLI de Azure]( /cli/azure/install-azure-cli). En este artículo se necesita la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión que tiene. También puede usar [Azure Cloud Shell](https://shell.azure.com/bash).

+ Una suscripción de Azure activa.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Creación del proyecto de aplicación de función local

Ejecute el comando siguiente desde la línea de comandos para crear un proyecto de aplicación de función en la carpeta `MyFunctionProj` del directorio local actual. También se crea un repositorio GitHub en `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Cuando se le pida, seleccione un tiempo de ejecución del trabajo de entre los siguientes lenguajes:

+ `dotnet`: crea un proyecto de biblioteca de clases .NET (.csproj).
+ `node`: crea un proyecto JavaScript.

Cuando se ejecute el comando, verá una salida similar a la siguiente:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Use el siguiente comando para ir a la nueva carpeta del proyecto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

## <a name="enable-extension-bundles"></a>Habilitación de conjuntos de extensiones

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. La Function App proporciona un entorno para la ejecución sin servidor de su código de función. Le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. Cree una Function App con el comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

En el siguiente comando, sustituya un nombre de aplicación de función único donde vea el marcador de posición `<APP_NAME>` y el nombre de la cuenta de almacenamiento para `<STORAGE_NAME>`. El `<APP_NAME>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. También debe establecer el entorno de ejecución de `<language>` para la aplicación de función de `dotnet` (C#), o `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Si establece el parámetro _consumption-plan-location_, la aplicación de función se hospedará en un plan de hospedaje de consumo. Los recursos de este plan sin servidor se agregan de forma dinámica según lo requieren las funciones y solo se paga por ellos cuando se están ejecutando dichas funciones. Para obtener más información, vea [Choose the correct hosting plan](functions-scale.md) (Elegir el plan de hospedaje correcto).

Después de que se haya creado la Function App, la CLI de Azure muestra información similar al siguiente ejemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

