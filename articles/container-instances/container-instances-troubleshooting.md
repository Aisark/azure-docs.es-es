---
title: Solución de problemas de Azure Container Instances
description: Obtenga información acerca de la solución de problemas relacionados con Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 4b41a3862341ef39c1288985d86d86667fbc5866
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325598"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Solución de problemas habituales de Azure Container Instances

En este artículo se muestra cómo solucionar problemas habituales al administrar o implementar contenedores en Azure Container Instances. Consulte también las [preguntas más frecuentes](container-instances-faq.md).

## <a name="naming-conventions"></a>Convenciones de nomenclatura

Al definir la especificación de contenedor, ciertos parámetros requieren tener en cuenta las restricciones de nomenclatura. A continuación se muestra una tabla con requisitos específicos para las propiedades del grupo de contenedores. Para obtener más información sobre las convenciones de nomenclatura de Azure, vea [Convenciones de nomenclatura][azure-name-restrictions] en el Centro de Arquitectura de Azure.

| Ámbito | Length | Uso de mayúsculas y minúsculas | Caracteres válidos | Patrón sugerido | Ejemplo |
| --- | --- | --- | --- | --- | --- |
| Nombre del grupo de contenedores | 1-64 |No distingue mayúsculas de minúsculas |Caracteres alfanuméricos y guion en cualquier lugar, excepto el primer o último carácter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nombre del contenedor | 1-64 |No distingue mayúsculas de minúsculas |Caracteres alfanuméricos y guion en cualquier lugar, excepto el primer o último carácter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Puertos del contenedor | Entre 1 y 65 535 |Entero |Un número entero comprendido entre 1 y 65 535 |`<port-number>` |`443` |
| Etiqueta de nombre DNS | 5-63 |No distingue mayúsculas de minúsculas |Caracteres alfanuméricos y guion en cualquier lugar, excepto el primer o último carácter |`<name>` |`frontend-site1` |
| Variable de entorno | 1-63 |No distingue mayúsculas de minúsculas |Caracteres alfanuméricos y guion bajo (_) en cualquier lugar, excepto el primer o último carácter |`<name>` |`MY_VARIABLE` |
| Nombre del volumen | 5-63 |No distingue mayúsculas de minúsculas |Letras minúsculas, números y guiones en cualquier lugar, excepto el primer o último carácter. No puede contener dos guiones consecutivos. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Versión del sistema operativo de imagen no admitida

Si se especifica una imagen que Azure Container Instances no admite, se devuelve un error `OsVersionNotSupported`. El error es similar a la siguiente, donde `{0}` es el nombre de la imagen que intentó implementar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Este error se suele encontrar con más frecuencia al implementar imágenes de Windows basadas en una versión de canal semianual (SAC) 1709 o 1803, que no se admiten. Para obtener imágenes de Windows admitidas en Azure Container Instances, consulte las [preguntas más frecuentes](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="unable-to-pull-image"></a>No se puede extraer la imagen

Si inicialmente Azure Container Instances no puede extraer su imagen, lo reintenta durante un período de tiempo. Si la operación de extracción de imágenes sigue generando errores, ACI acaba por no realizar la implementación y se muestra un error `Failed to pull image`.

Para resolver este problema, elimine la instancia de contenedor y vuelva a intentar la implementación. Asegúrese de que existe la imagen en el registro y de que ha escrito correctamente el nombre de imagen.

Si no se puede extraer la imagen, se muestran eventos similares al siguiente en la salida de [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>El contenedor se cierra y se reinicia continuamente (sin proceso de ejecución prolongada)

Los grupos de contenedores tienen una [directiva de reinicio](container-instances-restart-policy.md) establecida en **Always** (Siempre) de forma predeterminada, por lo que los contenedores del grupo de contenedores siempre se reinician después de ejecutarse hasta su finalización. Es posible que deba cambiar este valor a **OnFailure** (En caso de error) o **Never** (Nunca) si va a ejecutar contenedores basados en tareas. Si especifica **OnFailure** y sigue observando reinicios continuos, podría haber un problema con la aplicación o el script que se ejecuta en el contenedor.

Al ejecutar grupos de contenedores sin procesos de ejecución prolongada, es posible que vea cierres y reinicios repetidos con imágenes como Ubuntu o Alpine. La conexión mediante [EXEC](container-instances-exec.md) no funcionará porque el contenedor no tiene ningún proceso que lo mantenga activo. Para resolver este problema, incluya un comando de inicio similar al siguiente con la implementación del grupo de contenedores para mantener el contenedor en ejecución.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

La API de Container Instances y Azure Portal incluyen una propiedad `restartCount`. Para comprobar el número de reinicios de un contenedor, puede usar el comando [az container show][az-container-show] de la CLI de Azure. En la siguiente salida de ejemplo (que se ha truncado por razones de brevedad), puede ver la propiedad `restartCount` al final de la salida.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La mayoría de las imágenes de contenedor para las distribuciones de Linux establecen una shell, como bash, como el comando predeterminado. Puesto que un shell por sí mismo no es un servicio de ejecución prolongada, estos contenedores se cierran inmediatamente y caen en un bucle de reinicio cuando se configuran con la directiva de reinicio predeterminada **Always**.

## <a name="container-takes-a-long-time-to-start"></a>El contenedor tarda mucho tiempo en iniciar

Los dos factores principales que contribuyen al tiempo de inicio del contenedor en Azure Container Instances son:

* [Tamaño de las imágenes](#image-size)
* [Ubicación de las imágenes](#image-location)

Las imágenes de Windows tienen [consideraciones adicionales](#cached-images).

### <a name="image-size"></a>Tamaño de las imágenes

Si el contenedor tarda mucho tiempo en iniciar, pero al final se realiza correctamente, comience por mirar el tamaño de la imagen del contenedor. Como Azure Container Instances extrae la imagen del contenedor a petición, el tiempo de inicio está directamente relacionado con su tamaño.

Puede ver el tamaño de la imagen del contenedor mediante el comando `docker images` en la CLI de Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

La clave para mantener tamaños de imagen pequeños es asegurarse de que la imagen final no contiene nada que no sea necesario en tiempo de ejecución. Una manera de hacerlo es con [compilaciones en varias etapas][docker-multi-stage-builds]. Las compilaciones en varias etapas hacen más sencillo asegurarse de que la imagen final contiene solo los artefactos necesarios para la aplicación y no los del contenido extra que se requiere en tiempo de compilación.

### <a name="image-location"></a>Ubicación de las imágenes

Otra forma de reducir el impacto de la extracción de la imagen en el tiempo de inicio del contenedor es hospedar la imagen del contenedor con [Azure Container Registry](/azure/container-registry/) en la misma región en la que van a implementar las instancias de contenedor. Esto reduce la ruta de acceso de red que debe recorrer la imagen del contenedor, lo que reduce significativamente el tiempo de descarga.

### <a name="cached-images"></a>Imágenes en caché

Azure Container Instances usa un mecanismo de almacenamiento en caché para acelerar el tiempo de inicio del contenedor para las imágenes creadas con [imágenes de base de Windows](container-instances-faq.md#what-windows-base-os-images-are-supported), incluidas `nanoserver:1809`, `servercore:ltsc2019` y `servercore:1809`. También se almacenan en caché las imágenes de Linux usadas comúnmente, como `ubuntu:1604` y `alpine:3.6`. Para obtener una lista actualizada de imágenes y etiquetas en caché, use la API [List Cached Images][list-cached-images].

> [!NOTE]
> El uso de imágenes basadas en Windows Server 2019 en Azure Container Instances está en versión preliminar.

### <a name="windows-containers-slow-network-readiness"></a>Preparación para la red lenta de contenedores de Windows

Durante la creación inicial, los contenedores de Windows pueden no tener conectividad de entrada o salida hasta un máximo de 30 segundos (o, en raras ocasiones, incluso más). Si la aplicación contenedora necesita una conexión a Internet, agregue una lógica de retraso y reintento para dejar 30 segundos para el establecimiento de la conectividad de Internet. Después de la instalación inicial, las redes de contenedores se deben reanudar adecuadamente.

## <a name="resource-not-available-error"></a>Error de recurso no disponible

Debido a la carga variable de recursos regionales en Azure, podría recibir el siguiente error al intentar implementar una instancia del contenedor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este error indica que, debido a una carga elevada en la región en la que está intentando la implementación, no se pueden asignar los recursos especificados para el contenedor en ese momento. Utilice uno o varios de los siguientes pasos de mitigación para ayudar a resolver el problema.

* Compruebe que la configuración de implementación del contenedor se encuentra dentro de los parámetros definidos en [Region availability for Azure Container Instances](container-instances-region-availability.md) (Disponibilidad de regiones en instancias de Azure Container)
* Especifique la configuración de CPU y memoria más baja para el contenedor
* Implemente en una región distinta de Azure
* Implemente en un momento posterior

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>No se puede conectar a la API de Docker subyacente ni ejecutar contenedores con privilegios

Azure Container Instances no expone acceso directo a la infraestructura subyacente que hospeda los grupos de contenedores. Esto incluye el acceso a la API de Docker que se ejecuta en el host del contenedor y la ejecución de contenedores con privilegios elevados. Si se requiere la interacción de Docker, compruebe la [documentación de referencia de REST](https://aka.ms/aci/rest) para ver lo que admite la API de ACI. Si falta algo, envíe una solicitud en los [foro de comentarios de ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Las direcciones IP pueden no estar accesibles debido a que los puertos no coinciden

Azure Container Instances no admite actualmente la asignación de puertos con la configuración normal de docker; sin embargo, esta revisión está en la hoja de ruta. Si encuentra direcciones IP no accesibles que cree que deberían serlo, asegúrese de que ha configurado la imagen de contenedor para que escuche a los mismos puertos que expone en el grupo de contenedores con la propiedad `ports`.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [recuperar eventos y registros de contenedor](container-instances-get-logs.md) para ayudar a depurar los contenedores.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
