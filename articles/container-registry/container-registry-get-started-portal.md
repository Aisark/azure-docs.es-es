---
title: 'Guía de inicio rápido: Creación de un registro privado de Docker en Azure: Azure Portal'
description: Aprenda rápidamente a crear un registro de contenedor privado de Docker en Azure Portal.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c9e8c7fe4d32a44e8c0831154f02eda1f82aaff3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309483"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Guía de inicio rápido: Creación de un registro de contenedor privado con Azure Portal

Azure Container Registry es un registro privado de Docker en Azure donde se pueden almacenar y administrar las imágenes de contenedor privado de Docker. En esta guía de inicio rápido, creará un registro de contenedor con Azure Portal. A continuación, utilice los comandos de Docker para insertar una imagen de contenedor en el registro y, finalmente, extraiga y ejecute la imagen desde el registro.

Para iniciar sesión en el registro y trabajar con imágenes de contenedor, este inicio rápido requiere que se ejecute la CLI de Azure (se recomienda la versión 2.0.55 o posterior). Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli].

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

Seleccione **Crear un recurso** > **Contenedores** > **Registro de contenedor**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-01]

Escriba valores para **Nombre del Registro** y **Grupo de recursos**. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Para esta guía de inicio rápido, cree un grupo de recursos en la ubicación `West US` denominado `myResourceGroup`y en **SKU**, seleccione "Básico". Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][qs-portal-03]

En este inicio rápido se crea un registro *Básico*, que es una opción rentable para los desarrolladores que aprenden sobre Azure Container Registry. Para más información sobre los niveles de servicio disponibles, consulte [SKU de Azure Container Registry][container-registry-skus].

Cuando aparezca el mensaje **Implementación correcta**, seleccione el registro de contenedor en el portal. 

![Información general de los registros de contenedor en Azure Portal][qs-portal-05]

Tome nota del valor del **servidor de inicio de sesión**. Puede usar este valor en los pasos siguientes al trabajar con el registro con la CLI de Azure y Docker.

## <a name="log-in-to-registry"></a>Iniciar sesión en el registro

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Abra un shell de comandos en el sistema operativo y utilice el comando [az acr login][az-acr-login] en la CLI de Azure.

```azurecli
az acr login --name <acrName>
```

El comando devolverá `Login Succeeded` una vez completado. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Lista de imágenes de contenedor

Para mostrar las imágenes en el registro, vaya al registro en el portal, seleccione **Repositorios** y después seleccione el repositorio que creó con `docker push`.

En este ejemplo, se selecciona el repositorio **hello-world** y se puede ver la imagen etiquetada con `v1` en **ETIQUETAS**.

![Lista de imágenes de contenedor en Azure Portal][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos, vaya al grupo de recursos **myResourceGroup** en el portal. Una vez cargado el grupo de recursos, haga clic en **Eliminar grupo de recursos** para eliminar el grupo de recursos, el registro de contenedor y las imágenes de contenedor almacenadas allí.

![Eliminación de un grupo de recursos en Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una instancia de Azure Container Registry con Azure Portal, ha insertado una imagen de contenedor y ha extraído y ejecutado la imagen del registro. Siga los tutoriales de Azure Container Registry para información más detallada de ACR.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Registry][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
