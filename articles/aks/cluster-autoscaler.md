---
title: Usar el escalado automático de clústeres en Azure Kubernetes Service (AKS)
description: Aprenda a usar el escalado automático de clústeres para escalar automáticamente el clúster con el fin de satisfacer las necesidades de su aplicación en un clúster de Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 23922ec02f7406b5cbc482c938dbcf6a56cad6d7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234156"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Obtener una vista previa: escalado automático de un clúster para satisfacer las necesidades de aplicación en Azure Kubernetes Service (AKS)

Para satisfacer las necesidades de la aplicación en Azure Kubernetes Service (AKS), es posible que deba ajustar el número de nodos que ejecutan las cargas de trabajo. El componente de escalado automático de clústeres puede supervisar los pods del clúster que no pueden programarse debido a las restricciones de los recursos. Cuando se detectan problemas, la cantidad de nodos aumenta para satisfacer las necesidades de la aplicación. Asimismo, los nodos también se comprueban regularmente para detectar la falta de pods en ejecución y, en consecuencia, la cantidad de nodos se reduce según sea necesario. Esta capacidad de ampliar o reducir automáticamente la cantidad de nodos en su clúster de AKS le permite ejecutar un clúster de forma eficaz y rentable.

En este artículo se muestra cómo habilitar y administrar el escalado automático de clústeres en un clúster de AKS. Solo se debe probar el Escalador automático del clúster en versión preliminar en clústeres AKS con un grupo de nodo único.

> [!IMPORTANT]
> Características de versión preliminar AKS son autoservicio, participación. Se proporcionan para recopilar comentarios y los errores de nuestra comunidad. En la vista previa, estas características no están diseñadas para su uso en producción. Características en versión preliminar pública se incluyen en el soporte técnico de "mejor esfuerzo". Asistencia de los equipos de soporte técnico de AKS está disponible durante el horario comercial del Pacífico (PST) solo timezone. Para obtener más información, consulte los siguientes artículos de soporte técnico:
>
> * [Directivas de soporte técnico AKS][aks-support-policies]
> * [Preguntas más frecuentes de soporte técnico de Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de empezar

En este artículo es necesario ejecutar la versión de la CLI de Azure 2.0.55 o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

Los clústeres de AKS que admitan el escalado automático de clústeres deben usar conjuntos de escalado de máquinas virtuales y ejecutar la versión de Kubernetes *1.12.4* o posterior. La compatibilidad con el conjunto de escalado está en su versión preliminar. Para optar por recibir y crear clústeres que usan conjuntos de escalado, primero instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add], tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si ha instalado anteriormente el *-versión preliminar de aks* instalar haya disponible de extensión, actualizaciones utilizando el `az extension update --name aks-preview` comando.

### <a name="register-scale-set-feature-provider"></a>Registro del proveedor de características del conjunto de escalado

Para crear una instancia de AKS que use conjuntos de escalado, también debe habilitar una marca de característica en su suscripción. Para registrar la marca de característica *VMSSPreview*, use el comando [az feature register][az-feature-register] tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado del registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Cuando todo esté listo, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitaciones

Las siguientes limitaciones se aplican al crear y administrar clústeres AKS que usan conjuntos de escalado de máquinas virtuales:

* No se puede usar el complemento de enrutamiento de aplicación HTTP.

## <a name="about-the-cluster-autoscaler"></a>Acerca del escalado automático de clústeres

Para adaptarse a las cambiantes necesidades de las aplicaciones, como detallar los horarios entre la jornada laboral y la noche o un fin de semana, los clústeres a menudo necesitan una forma de realizar automáticamente el escalado. Los clústeres de AKS pueden escalarse mediante una de dos maneras:

* El **escalado automático de clústeres** busca pods que no puedan programarse en nodos debido a las restricciones de los recursos. El clúster aumenta automáticamente la cantidad de nodos.
* El **escalado automático horizontal de pods** usa el servidor de métricas en un clúster de Kubernetes para supervisar la demanda de recursos de los pods. Si un servicio necesita más recursos, el número de pods aumenta automáticamente para satisfacer la demanda.

![El escalado automático de clústeres y el escalado automático horizontal de pods colaboran a menudo para responder a las exigencias de la aplicación.](media/autoscaler/cluster-autoscaler.png)

Tanto el escalado automático de clústeres como el escalado automático horizontal de pods pueden reducir el número de pods y nodos según sea necesario. El escalado automático de clústeres reduce el número de nodos cuando no se usa toda la capacidad durante un período de tiempo. Los pods de un nodo que quitará el escalado automático de clústeres se programan con seguridad en otra parte del clúster. El escalado automático de clústeres no se puede reducir verticalmente si no se pueden mover los pods, tal como se detalla en las situaciones siguientes:

* Un pod creado directamente y que no lo respalde un objeto de controlador, como una implementación o un conjunto de réplicas.
* Un presupuesto de interrupciones de pods (PDB) es demasiado restrictivo y no permite que la cantidad de pods sea menor que el umbral definido.
* Un pod usa selectores de nodo o antiafinidad que no se pueden respetar si se programan en un nodo diferente.

Para obtener más información acerca de cómo el escalado automático de clústeres no puede realizar reducciones verticales, consulte [What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown] (¿Qué tipos de pods pueden evitar que el escalado automático de clústeres elimine un nodo?)

El escalado automático de clústeres usa parámetros de inicio para cosas como intervalos de tiempo entre eventos de escalado y umbrales de recursos. Estos parámetros se definen mediante la plataforma de Azure y actualmente no se pueden ajustar. Para obtener más información sobre los parámetros que usa el escalado automático de clústeres, consulte [What are the cluster autoscaler parameters?][autoscaler-parameters] (¿Cuáles son los parámetros de clúster del escalado automático de clústeres?).

Los dos escalados automáticos pueden funcionar juntos y a menudo se implementan en un clúster. Cuando se combinan, el escalado automático horizontal de pods se centra en ejecutar el número de pods necesario para satisfacer las exigencias de la aplicación. El escalado automático de clústeres se centra en ejecutar el número de nodos necesario para admitir los pods programados.

> [!NOTE]
> El escalado manual está deshabilitado cuando se usa el escalado automático de clústeres. Permita que el escalado automático de clústeres determine el número de nodos. Si quiere escalar manualmente el clúster, [deshabilite el escalado automático de clústeres](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Crear un clúster de AKS y habilitar el escalado automático de clústeres

Si necesita crear un clúster de AKS, use el comando [az aks create][az-aks-create]. Especifique un valor en *--kubernetes-version* que coincida con el número de versión mínimo necesario o lo supere, tal como se describe en la sección anterior [Antes de comenzar](#before-you-begin). Para habilitar y configurar el escalado automático de clústeres, use el parámetro *--enable-cluster-autoscaler* y especifique un nodo *--min-count* y *--max-count*.

> [!IMPORTANT]
> El escalador automático del clúster es un componente de Kubernetes. Aunque el clúster de AKS usa un conjunto de escalado de máquinas virtuales para los nodos, no habilite ni edite manualmente la configuración de escalado automático del conjunto de escalado en Azure Portal o mediante la CLI de Azure. Permita que el escalador automático del clúster de Kubernetes administre la configuración del escalado necesaria. Para más información, consulte [¿Puedo modificar los recursos de AKS en el grupo de recursos MC_?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

En el ejemplo siguiente se crea un clúster de AKS con un conjunto de escalado de máquinas virtuales y el escalado automático de clústeres habilitado; asimismo, se usará un mínimo de *1* y un máximo de *3* nodos:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Tardará unos minutos en crear el clúster y configurar las opciones del escalado automático de clústeres.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Habilitar el escalado automático de clústeres en un clúster de AKS existente

Puede habilitar el escalado automático de clústeres en un clúster de AKS existente que cumpla los requisitos descritos en la sección [Antes de comenzar](#before-you-begin). Use el comando [az aks update][az-aks-update] y seleccione *--enable-cluster-autoscaler*; a continuación, especifique un nodo *--min-count* y *--max-count*. En el ejemplo siguiente se habilita el escalado automático de clústeres en un clúster existente que usa como mínimo *1* nodo y como máximo *3* nodos:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Si el número mínimo de nodos es mayor que el número de nodos existente en el clúster, los nodos adicionales tardarán unos minutos en crearse.

## <a name="change-the-cluster-autoscaler-settings"></a>Cambiar la configuración del escalado automático de clústeres

En el paso anterior para crear o actualizar un clúster de AKS existente, el número mínimo de nodos del escalado automático de clústeres se estableció en *1*, y el número máximo de nodos se estableció en *3*. Como las necesidades de la aplicación van cambiando, es posible que deba ajustar el número de nodos del escalado automático de clústeres.

Para cambiar el número de nodos, use el comando [az aks update][az-aks-update] y especifique un valor mínimo y máximo. En el ejemplo siguiente se establece el valor de *--min-count* en *1* y el de *--max-count* en *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Durante la versión preliminar, no se puede establecer un número mínimo de nodos que sea mayor que el número establecido para el clúster. Por ejemplo, si actualmente tiene el mínimo establecido en *1*, no se puede actualizar a *3*.

Supervise el rendimiento de las aplicaciones y los servicios y ajuste la cantidad de nodos del escalado automático de clústeres para que coincida con el rendimiento necesario.

## <a name="disable-the-cluster-autoscaler"></a>Deshabilitar el escalado automático de clústeres

Si ya no quiere usar el escalado automático de clústeres, puede deshabilitarlo mediante el comando [z aks update][az-aks-update]. Los nodos no se quitan cuando se deshabilita el escalado automático de clústeres.

Para quitar el escalado automático de clústeres, especifique el parámetro *--disable-cluster-autoscaler*, tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Puede escalar manualmente el clúster con el comando [az aks scale][az-aks-scale]. Si usa el escalado automático horizontal de pods, esa característica continúa ejecutándose con el escalado automático de clústeres deshabilitado, pero es posible que los pods no puedan programarse si todos los recursos de nodo están en uso.

## <a name="next-steps"></a>Pasos siguientes

En este artículo le mostramos cómo escalar automáticamente el número de nodos de AKS. Asimismo, también puede usar el escalado automático horizontal de pods para ajustar automáticamente el número de pods ejecutan la aplicación. Para obtener instrucciones sobre cómo usar el escalado automático horizontal de pods, consulte [Escalado de aplicaciones en AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
