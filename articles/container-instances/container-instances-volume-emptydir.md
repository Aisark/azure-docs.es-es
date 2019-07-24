---
title: Montaje de un volumen de emptyDir en Azure Container Instances
description: Más información sobre cómo montar un volumen emptyDir para compartir datos entre los contenedores de un grupo de contenedores de Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: d91706da898e84effc6194a74dce69a66be0f4ac
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657635"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montaje de un volumen de emptyDir en Azure Container Instances

Aprenda a montar un volumen *emptyDir* para compartir datos entre los contenedores de un grupo de contenedores en Azure Container Instances.

> [!NOTE]
> El montaje de un volumen *emptyDir* está actualmente restringido a los contenedores de Linux. Aunque se está trabajando para incorporar todas las características a los contenedores Windows, puede consultar las diferencias actuales entre plataformas en [Información general](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>Volumen emptyDir

El volumen *emptyDir* ofrece un directorio de escritura accesible para cada contenedor de un grupo de contenedores. Los contenedores del grupo pueden leer y escribir los mismos archivos en el volumen, que puede montarse mediante las mismas o distintas rutas de acceso en cada contenedor.

Algunos ejemplos de uso de un volumen *emptyDir*:

* Espacio de desecho
* Punto de control durante las tareas de ejecución prolongada
* Almacenamiento de datos recuperados por un contenedor sidecar y atendidos por un contenedor de aplicación

Los datos incluidos en un volumen *emptyDir* se conservan a través de bloqueos de contenedor. Pero no se garantiza que se conserven los datos de un volumen *emptyDir* en los contenedores que se reinicien.

## <a name="mount-an-emptydir-volume"></a>Montaje de un volumen emptyDir

Para montar un volumen emptyDir en una instancia de un contenedor, hay que realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, rellene la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. Luego, para cada contenedor del grupo de contenedores en el que quiera montar el volumen *emptyDir*, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor.

Por ejemplo, la siguiente plantilla de Resource Manager crea un grupo de contenedores que consta de dos contenedores y en cada uno de ellos se monta el volumen *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, consulte [Implementación de grupos de varios contenedores en Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-volume-azure-files.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montaje de un volumen secreto en Azure Container Instances](container-instances-volume-secret.md)