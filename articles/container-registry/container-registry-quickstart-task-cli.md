---
title: 'Inicio rápido: compilación y ejecución de una imagen de contenedor en Azure Container Registry'
description: Ejecute las tareas rápidamente con Azure Container Registry para compilar y ejecutar una imagen de contenedor a petición, en la nube.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e5e02d8194f9164a03bb27d932df45d91486c518
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310626"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Inicio rápido: Compilación y ejecución de una imagen de contenedor en Azure Container Registry Tasks

En este tutorial, se usan comandos de Azure Container Registry Tasks para crear, insertar y ejecutar rápidamente una imagen de un contenedor de Docker de forma nativa en Azure, lo que muestra cómo descargar el ciclo de desarrollo de "bucle interno" en la nube. [ACR Tasks][container-registry-tasks-overview] es un conjunto de características de Azure Container Registry que le ayudan a administrar y modificar imágenes de contenedor durante el ciclo de vida de este. 

Después de este inicio rápido, explore las características más avanzadas de ACR Tasks. ACR Tasks puede automatizar las compilaciones de imágenes basadas en actualizaciones de la imagen base o de confirmaciones de código, o probar varios contenedores, en paralelo, entre otros escenarios. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita][azure-account] antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Puede usar Azure Cloud Shell o una instalación local de la CLI de Azure para completar esta guía de inicio rápido. Si desea usarlo de forma local, se recomienda la versión 2.0.58, o cualquier versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Si aún no tiene un registro de contenedor, cree un grupo de recursos con el comando [az group create][az-group-create]. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

Cree un registro de contenedor mediante el comando [az acr create][az-acr-create]. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el ejemplo siguiente, se usa *myContainerRegistry008*. Actualice este valor a uno único.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Este ejemplo crea un registro *Básico*, una opción rentable para los desarrolladores que aprenden sobre Azure Container Registry. Para más información sobre los niveles de servicio disponibles, consulte [SKU de Azure Container Registry][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Creación de una imagen desde Dockerfile

Ahora usa Azure Container Registry para compilar una imagen. En primer lugar, cree un directorio de trabajo y, después, cree un archivo Dockerfile denominado *Dockerfile* con el contenido siguiente. Esto es un ejemplo sencillo para crear una imagen de contenedor de Linux, pero puede crear su propio Dockerfile estándar y compilar imágenes para otras plataformas.

```bash
echo FROM hello-world > Dockerfile
```

Ejecute el comando [az acr build][az-acr-build] para compilar la imagen. Cuando la haya compilado correctamente, la imagen se inserta en el registro. En el ejemplo siguiente se inserta la imagen `sample/hello-world:v1`. El signo `.` al final del comando establece la ubicación del Dockerfile, en este caso el directorio actual.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

La salida de una compilación e inserciones correctas is similar a esta:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Ejecución de la imagen

Ahora, ejecute rápidamente la imagen que ha compilado e insertado en el registro. En el flujo de trabajo de desarrollo de contenedor, esto podría ser un paso de validación antes de implementar la imagen.

Cree un archivo *quickrun.yaml* en un directorio de trabajo local con el siguiente contenido para un solo paso. Sustituya el nombre del servidor de inicio de sesión del registro para *\<acrLoginServer\>* . El nombre del servidor de inicio de sesión tiene el formato *\<nombre-registro\>.azurecr.io* (todo en minúscula), por ejemplo, *mycontainerregistry008.azurecr.io*. En este ejemplo se da por supuesto que ha compilado e insertado la imagen `sample/hello-world:v1` en la sección anterior:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

El paso `cmd` del ejemplo ejecuta el contenedor en su configuración predeterminada, pero `cmd` admite parámetros de `docker run` adicionales, o incluso otros comandos `docker`.

Ejecute el contenedor con el siguiente comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

La salida es similar a la siguiente:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar el comando [az group delete][az-group-delete] para quitar el grupo de recursos, el registro de contenedor y las imágenes de contenedor almacenadas allí.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha utilizado características de ACR Tasks para compilar, insertar y ejecutar una imagen de contenedor Docker de forma nativa en Azure. Continúe con los tutoriales de Azure Container Registry, donde aprenderá a usar ACR Tasks para automatizar las compilaciones y actualizaciones de imágenes.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
