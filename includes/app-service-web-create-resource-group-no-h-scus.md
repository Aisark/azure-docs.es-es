---
title: archivo de inclusión
description: archivo de inclusión
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66139308"
---
[!INCLUDE [resource group intro text](resource-group.md)]

En Cloud Shell, cree un grupo de recursos con el comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *Centro y Sur de EE. UU.* Para ver todas las ubicaciones que se admiten en App Service en el nivel **Gratis**, ejecute el comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Generalmente se crean el grupo de recursos y los recursos en una región cercana. 

Cuando finaliza el comando, una salida de JSON muestra las propiedades del grupo de recursos.