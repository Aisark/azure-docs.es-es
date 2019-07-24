---
title: 'Creación de una aplicación de varios contenedores con Docker Compose: Azure App Service'
description: Implemente su primera aplicación de varios contenedores en Azure Web App for Containers en minutos
keywords: servicio de aplicación de azure, aplicación web, linux, docker, compose, multicontenedor, varios contenedores, aplicación web para contenedores, contenedor, wordpress, azure db for mysql, base de datos de producción con contenedores
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fce819e84d5c532777ffee91ed4e75a0b65dec9b
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853699"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Creación de una aplicación de varios contenedores (versión preliminar) mediante una configuración de Docker Compose

[Web App for Containers](app-service-linux-intro.md) proporciona una manera flexible de utilizar imágenes de Docker. Esta guía de inicio rápido muestra cómo implementar una aplicación de varios contenedores en Web App for Containers con [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) mediante una configuración de Docker Compose.

Completará esta guía de inicio rápido en Cloud Shell, pero también puede ejecutar estos comandos localmente con la [CLI de Azure](/cli/azure/install-azure-cli) (2.0.32 o posterior). 

![Aplicación de varios contenedores de ejemplo en Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo

En esta guía de inicio rápido se usa el archivo de Compose de [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). El archivo de configuración se pueden encontrar en los [ejemplos de Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

En Cloud Shell, cree un directorio de inicio rápido y luego cambie a él.

```bash
mkdir quickstart

cd quickstart
```

A continuación, ejecute el comando siguiente para clonar el repositorio de la aplicación de ejemplo en el directorio de inicio rápido. Después, cambie al directorio `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

En Cloud Shell, cree un grupo de recursos con el comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *Centro y Sur de EE. UU.* Para ver todas las ubicaciones de App Service que se admiten en Linux en el nivel **Estándar**, ejecute el comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Generalmente se crean el grupo de recursos y los recursos en una región cercana.

Cuando finaliza el comando, una salida de JSON muestra las propiedades del grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Crear un plan de Azure App Service

En Cloud Shell, cree un plan de App Service en el grupo de recursos con el comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

En el ejemplo siguiente se crea un plan de App Service denominado `myAppServicePlan` en el plan de tarifa **Estándar** (`--sku S1`) y en un contenedor Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Creación de una aplicación de Docker Compose

En el terminal de Cloud Shell, cree una [aplicación web](app-service-linux-intro.md) de varios contenedores en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). No olvide reemplazar _\<app_name>_ por un nombre de aplicación único (los caracteres válidos son `a-z`, `0-9` y `-`).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Navegación hasta la aplicación

Vaya a la aplicación implementada en (`http://<app_name>.azurewebsites.net`). La aplicación puede tardar unos minutos en cargarse. Si recibe un error, deje pasar unos minutos, y actualice el explorador.

![Aplicación de varios contenedores de ejemplo en Web App for Containers][1]

**Enhorabuena**, ha creado una aplicación de varios contenedores en Web App for Containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Aplicación de WordPress con varios contenedores](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Configure a custom container](configure-custom-container.md) (Configuración de un contenedor personalizado)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png