---
title: 'Tutorial: Creación de una instancia de Azure Container Registry con replicación geográfica'
description: Crear un registro de contenedor de Azure, configurar la replicación geográfica, preparar una imagen de Docker e implementarla en el registro. Primera parte de una serie de tres partes.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 5a2aedfe93aa27f839c416c27ac028db1e650295
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931365"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Tutorial: Preparar un registro de contenedor de Azure con replicación geográfica

Un registro de contenedor de Azure es un registro de Docker privado implementado en Azure que puede mantener cerca de la red en las implementaciones. En este conjunto de artículos de tres tutoriales, aprenderá a usar la replicación geográfica para implementar una aplicación web de ASP.NET Core que se ejecuta en un contenedor de Linux en dos instancias de [Web App for Containers](../app-service/containers/index.yml). Podrá ver cómo Azure implementa automáticamente la imagen en cada instancia de Web App desde el repositorio de replicación geográfica más cercano.

Esta es la primera parte de este tutorial de tres partes:

> [!div class="checklist"]
> * Crear un registro de contenedor de Azure con replicación geográfica
> * Clonar el código fuente de la aplicación desde GitHub
> * Compilar una imagen de contenedor de Docker desde el origen de la aplicación
> * Insertar la imagen de contenedor en el registro

En los siguientes tutoriales implementará el contenedor desde el registro privado en una aplicación web que se ejecute en dos regiones de Azure. A continuación, actualizará el código en la aplicación y ambas instancias de Web App con un solo elemento `docker push` en el registro.

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesaria una instalación local de la CLI de Azure (versión 2.0.31 o posterior). Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

Debe estar familiarizado con los principales conceptos de Docker, como los contenedores, las imágenes de contenedor y los comandos básicos de la CLI de Docker. Si es necesario, consulte la [introducción a Docker]( https://docs.docker.com/get-started/), donde encontrará datos básicos acerca de los contenedores.

Para completar este tutorial, necesita una instalación local de Docker. Docker ofrece instrucciones de instalación para sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) y [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Por lo tanto, le recomendamos que realice una instalación local del entorno de desarrollo de la CLI de Azure y Docker.

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

Inicie sesión en el [Azure Portal](https://portal.azure.com).

Seleccione **Crear un recurso** > **Contenedores** > **Azure Container Registry**.

![Creación de un registro de contenedor en Azure Portal][tut-portal-01]

Configure el registro nuevo con la configuración siguiente:

* **Nombre del Registro**: cree un nombre del Registro único global en Azure y que contenga entre 5 y 50 caracteres alfanuméricos.
* **Grupos de recursos**: **Crear nuevo** > `myResourceGroup`
* **Ubicación**: `West US`.
* **Usuario administrador**: `Enable` (es necesario para que Web App for Containers extraiga imágenes).
* **SKU**: `Premium` (es necesario para la replicación geográfica).

Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][tut-portal-02]

En el resto de este tutorial, usaremos `<acrName>` como marcador de posición del **nombre del registro** del contenedor que eligió.

> [!TIP]
> Dado que los registros de contenedor de Azure son normalmente recursos de larga duración que se usan en varios hosts de contenedor, le recomendamos que cree el registro en su propio grupo de recursos. Al configurar webhooks y registros con replicación geográfica, estos recursos adicionales se colocan en el mismo grupo de recursos.
>

## <a name="configure-geo-replication"></a>Configuración de la replicación geográfica

Ahora que tiene un registro Premium, puede configurar la replicación geográfica. La aplicación web (la cual configurará en el tutorial siguiente para que se ejecute en dos regiones) puede extraer las imágenes de contenedor del registro más cercano.

Vaya al nuevo registro de contenedor de Azure Portal y seleccione **Replicaciones** en **SERVICIOS**:

![Replicaciones en la interfaz de usuario del registro de contenedor en Azure Portal][tut-portal-03]

Se muestra un mapa con hexágonos verdes que representan las regiones de Azure disponibles para realizar la replicación geográfica:

 ![Mapa de regiones de Azure Portal][tut-map-01]

Para replicar el registro en la región del Este de EE. UU., seleccione el hexágono verde correspondiente y, a continuación, seleccione **Crear** en **Crear replicación**:

 ![Interfaz de usuario de la creación de la replicación en Azure Portal][tut-portal-04]

Una vez completada la replicación, el portal mostrará el mensaje *Listo* para ambas regiones. Use el botón **Actualizar** para actualizar el estado de la replicación; tenga en cuenta que tal vez tenga que esperar un par de minutos hasta que se complete la creación y sincronización de las réplicas.

![Interfaz de usuario del estado de la replicación en Azure Portal][tut-portal-05]

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Ahora que ha configurado la replicación geográfica, compile una imagen de contenedor e insértela en el registro. Primero debe iniciar sesión en la instancia de ACR antes de insertar imágenes en ella.

Use el comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) para autenticarse y almacenar en caché las credenciales del registro. Reemplace `<acrName>` por el nombre del registro que creó anteriormente.

```azurecli
az acr login --name <acrName>
```

El comando devolverá `Login Succeeded` cuando esté completo.

## <a name="get-application-code"></a>Obtención del código de la aplicación

En el ejemplo de este tutorial se incluye una pequeña aplicación web compilada con [ASP.NET Core][aspnet-core]. La aplicación sirve a una página HTML que muestra la región desde la que Azure Container Registry implementó la imagen.

![Aplicación del tutorial tal como se muestra en un explorador][tut-app-01]

Use GIT para descargar el ejemplo en un directorio local y `cd` en el directorio:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Si no tiene `git` instalado, puede [descargar el archivo ZIP][acr-helloworld-zip] directamente desde GitHub.

## <a name="update-dockerfile"></a>Actualizar Dockerfile

El archivo Dockerfile que se proporciona en el ejemplo muestra cómo se compila el contenedor. Se inicia desde una imagen oficial [aspnetcore][dockerhub-aspnetcore], copia los archivos de aplicación en el contenedor, instala las dependencias, compila los elementos de salida con la imagen oficial [aspnetcore-build][dockerhub-aspnetcore-build] y, finalmente, compila una imagen optimizada aspnetcore.

El archivo [Dockerfile][dockerfile] se encuentra en `./AcrHelloworld/Dockerfile` en el origen clonado.

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

La aplicación en la imagen *acr helloworld* intenta determinar la región desde la que se implementó el contenedor consultando al DNS para obtener información sobre el servidor de inicio de sesión del registro. Debe especificar el nombre de dominio completo (FQDN) del servidor de inicio de sesión del registro en la variable de entorno `DOCKER_REGISTRY` que se encuentra en el archivo Dockerfile.

Primero, obtenga el servidor de inicio de sesión del registro con el comando `az acr show`. Reemplace `<acrName>` por el nombre del registro que creó en los pasos anteriores.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Salida:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

A continuación, actualice la línea `ENV DOCKER_REGISTRY` con el FQDN del servidor de inicio de sesión del registro. Este ejemplo refleja el nombre del registro de ejemplo, *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Compilar la imagen del contenedor

Ahora que ha actualizado el archivo Dockerfile con el FQDN del servidor de inicio de sesión del registro, puede usar `docker build` para crear la imagen del contenedor. Ejecute el siguiente comando para compilar la imagen y etiquetarla con la dirección URL de su registro privado; a continuación, vuelva a reemplazar `<acrName>` con el nombre del registro:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Se muestran varias líneas de salida a medida que se compila la imagen de Docker (aquí se muestra truncada):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Use `docker images` para ver la imagen compilada y etiquetada:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Inserción de imágenes en Azure Container Registry

A continuación, use el comando `docker push` para insertar la imagen *acr-helloworld* en el registro. Reemplace `<acrName>` por el nombre del registro.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Como ha configurado el registro para la replicación geográfica, la imagen se replica automáticamente en las dos regiones de *Oeste de EE. UU.* y *Este de EE. UU.* con este único comando `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó un registro de contenedor privado con replicación geográfica, compiló una imagen de contenedor y la insertó en el registro.

Vaya al siguiente tutorial para implementar el contenedor en varias instancias de Web App for Containers mediante la replicación geográfica y así poder atender las imágenes de forma local.

> [!div class="nextstepaction"]
> [Implementación de una aplicación web desde Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile