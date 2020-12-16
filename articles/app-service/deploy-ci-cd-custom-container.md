---
title: CI/CD para contenedores Linux personalizados
description: Aprenda a configurar la implementación continua en un contenedor Linux personalizado en Azure App Service. La implementación continua es compatible con Docker Hub y ACR.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e879d8370821ea465147e344d0fe95836c843ff4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008017"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementación continua con Web App for Containers

En este tutorial, configurará una implementación continua para una imagen de contenedor personalizada desde los repositorios administrados de [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) o [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Habilitación de la implementación continua con ACR

![Captura de pantalla del webhook ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione la opción **App Service** del lado izquierdo de la página.
3. Seleccione el nombre de la aplicación para la que quiere configurar una implementación continua.
4. En la página **Configuración del contenedor**, seleccione **Contenedor único**
5. Seleccione **Azure Container Registry**.
6. Seleccione **Implementación continua > Activar**
7. Seleccione **Guardar** para habilitar la implementación continua.

## <a name="use-the-acr-webhook"></a>Uso del webhook de ACR

Una vez que se ha habilitado la implementación continua, puede ver el webhook recién creado en la página de webhooks de Azure Container Registry.

![Captura de pantalla que muestra dónde puede ver el webhook recién creado en la página de webhooks de Azure Container Registry.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

En Container Registry, haga clic en Webhooks para ver los webhooks actuales.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Habilitación de la implementación continua con Docker Hub (opcional)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione la opción **App Service** del lado izquierdo de la página.
3. Seleccione el nombre de la aplicación para la que quiere configurar una implementación continua.
4. En la página **Configuración del contenedor**, seleccione **Contenedor único**
5. Seleccione **Docker Hub**
6. Seleccione **Implementación continua > Activar**
7. Seleccione **Guardar** para habilitar la implementación continua.

![Captura de pantalla de la configuración de la aplicación](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Copie la dirección URL del webhook. Para agregar un webhook para Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks para Docker Hub</a>.

## <a name="automate-with-cli"></a>Automatización con la interfaz de la línea de comandos

Para configurar CI/CD con la CLI de Azure, ejecute el comando [az webapp deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) para generar la dirección URL del webhook. Esta dirección URL se puede usar para configurar su instancia de Azure Container Registry o DockerHub.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Pasos siguientes

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Creación de una aplicación web de .NET Core en App Service en Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Creación de una aplicación web de Ruby en App Service en Linux](quickstart-ruby.md)
* [Inicio rápido: Ejecución de un contenedor personalizado en App Service](quickstart-custom-container.md?pivots=container-linux)
* [Peguntas más frecuentes sobre App Service en Linux](faq-app-service-linux.md)
* [Configuración de un contenedor Linux personalizado](configure-custom-container.md)
