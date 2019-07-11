---
title: 'Implementación continua con Azure Web App for Containers: Azure App Service | Microsoft Docs'
description: Configuración de la implementación continua en Web App for Containers.
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: be3768aadb3e256aa7b0ba7fe3d72cf4169e9bb2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705716"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implementación continua con Web App for Containers

En este tutorial, configurará una implementación continua para una imagen de contenedor personalizada desde los repositorios administrados de [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) o [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Habilitación de la implementación continua con ACR

![Captura de pantalla del webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione la opción **App Service** del lado izquierdo de la página.
3. Seleccione el nombre de la aplicación para la que quiere configurar una implementación continua.
4. En la página **Configuración del contenedor**, seleccione **Contenedor único**
5. Seleccione **Azure Container Registry**.
6. Seleccione **Implementación continua > Activar**
7. Seleccione **Guardar** para habilitar la implementación continua.

## <a name="use-the-acr-webhook"></a>Uso del webhook de ACR

Una vez que se ha habilitado la implementación continua, puede ver el webhook recién creado en la página de webhooks de Azure Container Registry.

![Captura de pantalla del webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

En Container Registry, haga clic en Webhooks para ver los webhooks actuales.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Habilitación de la implementación continua con Docker Hub (opcional)

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione la opción **App Service** del lado izquierdo de la página.
3. Seleccione el nombre de la aplicación para la que quiere configurar una implementación continua.
4. En la página **Configuración del contenedor**, seleccione **Contenedor único**
5. Seleccione **Docker Hub**
6. Seleccione **Implementación continua > Activar**
7. Seleccione **Guardar** para habilitar la implementación continua.

![Captura de pantalla de la configuración de la aplicación](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copie la dirección URL del webhook. Para agregar un webhook para Docker Hub, siga <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks para Docker Hub</a>.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure App Service en Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Creación de una aplicación web de .NET Core en App Service en Linux](quickstart-dotnetcore.md)
* [Creación de una aplicación web de Ruby en App Service en Linux](quickstart-ruby.md)
* [Implementación de una aplicación web de Docker o Go en Web App for Containers](quickstart-docker-go.md)
* [Peguntas más frecuentes sobre Azure App Service en Linux](./app-service-linux-faq.md)
* [Administración de Web App for Containers mediante la CLI de Azure](./app-service-linux-cli.md)
