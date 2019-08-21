---
title: Usar el escalado automático de clústeres en Azure Kubernetes Service (AKS)
description: Aprenda a usar el escalado automático de clústeres para escalar automáticamente el clúster con el fin de satisfacer las necesidades de su aplicación en un clúster de Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: dc5e862109a766f708338ebddb91a75ffc550306
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031924"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Versión preliminar: Escalado automático de un clúster para satisfacer las necesidades de la aplicación en Azure Kubernetes Service (AKS)

Para satisfacer las necesidades de la aplicación en Azure Kubernetes Service (AKS), es posible que deba ajustar el número de nodos que ejecutan las cargas de trabajo. El componente de escalado automático de clústeres puede supervisar los pods del clúster que no pueden programarse debido a las restricciones de los recursos. Cuando se detectan problemas, la cantidad de nodos de un grupo de nodos aumenta para satisfacer las necesidades de la aplicación. Asimismo, los nodos también se comprueban regularmente para detectar la falta de pods en ejecución y, en consecuencia, la cantidad de nodos se reduce según sea necesario. Esta capacidad de ampliar o reducir automáticamente la cantidad de nodos en su clúster de AKS le permite ejecutar un clúster de forma eficaz y rentable.

En este artículo se muestra cómo habilitar y administrar el escalado automático de clústeres en un clúster de AKS. Solo se debe probar Cluster Autoscaler en versión preliminar en clústeres AKS.

> [!IMPORTANT]
> Las características en vista previa de AKS son de autoservicio y se tienen que habilitar. Las versiones preliminares se proporcionan "tal cual" y "como están disponibles", y están excluidas de los contratos de nivel de servicio y la garantía limitada. Las versiones preliminares de AKS reciben cobertura parcial del soporte al cliente en la medida de lo posible. Por lo tanto, estas características no están diseñadas para usarse en producción. Para obtener información adicional, consulte los siguientes artículos de soporte:
>
> * [Directivas de soporte técnico para AKS][aks-support-policies]
> * [Preguntas más frecuentes de soporte técnico de Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de empezar

Para este artículo es preciso usar la versión 2.0.65 de la CLI de Azure, o cualquier versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

Para usar la escalabilidad automática del clúster, necesitará la versión 0.4.4 de la extensión de la CLI *aks-preview* o una posterior. Instale la extensión de la CLI de Azure *aks-preview* con el comando [az extension add][az-extension-add] y, a continuación, busque las actualizaciones disponibles con el comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Registro del proveedor de características del conjunto de escalado

Para crear una instancia de AKS que use conjuntos de escalado, también debe habilitar una marca de característica en su suscripción. Para registrar la marca de característica *VMSSPreview*, use el comando [az feature register][az-feature-register] tal como se muestra en el ejemplo siguiente:

> [!CAUTION]
> Actualmente, al registrar una característica en una suscripción, no se puede anular el registro de esa característica. Después de habilitar algunas características en vista previa, se pueden usar los valores predeterminados en todos los clústeres de AKS y, luego, se pueden crear en la suscripción. No habilite características en vista previa en las suscripciones de producción. Use una suscripción independiente para probar las características en vista previa y recopilar comentarios.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Cuando todo esté listo, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitaciones

Se aplican las siguientes limitaciones cuando crea y administra clústeres de AKS que usan la escalabilidad automática de clúster:

* El complemento de enrutamiento de aplicación HTTP no se puede utilizar.

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

Si necesita crear un clúster de AKS, use el comando [az aks create][az-aks-create]. Para habilitar y configurar el escalado automático de clústeres en el grupo de nodos del clúster, use el parámetro *--enable-cluster-autoscaler* y especifique un nodo *--min-count* y *--max-count*.

> [!IMPORTANT]
> El escalador automático del clúster es un componente de Kubernetes. Aunque el clúster de AKS usa un conjunto de escalado de máquinas virtuales para los nodos, no habilite ni edite manualmente la configuración de escalado automático del conjunto de escalado en Azure Portal o mediante la CLI de Azure. Permita que el escalador automático del clúster de Kubernetes administre la configuración del escalado necesaria. Para más información, consulte [¿Puedo modificar los recursos de AKS en el grupo de recursos del nodo?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

En el ejemplo siguiente se crea un clúster de AKS con un único grupo de nodos respaldado por un conjunto de escalado de máquinas virtuales. También habilita la escalabilidad automática del clúster en el grupo de nodos para el clúster y establece un mínimo de *1* y un máximo de *3* nodos:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

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

> [!NOTE]
> Si especifica un valor en *--kubernetes-version* al ejecutar `az aks create`, la versión debe coincidir o superar el número de versión mínimo necesario, tal como se describe en la sección anterior [Antes de comenzar](#before-you-begin).

Tardará unos minutos en crear el clúster y configurar las opciones del escalado automático de clústeres.

### <a name="update-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-a-single-node-pool"></a>Actualización del escalado automático de clústeres en un grupo de nodos existente en un clúster con un único grupo de nodos

Puede actualizar la configuración del escalado automático de clústeres anterior en un clúster que cumpla los requisitos descritos en la sección [Antes de comenzar](#before-you-begin). Use el comando [az aks update][az-aks-update] para habilitar el escalado automático de clúster en el clúster con un *único* grupo de nodos.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Posteriormente, se puede habilitar o deshabilitar el escalado automático con los comandos `az aks update --enable-cluster-autoscaler` o `az aks update --disable-cluster-autoscaler`.

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-multiple-node-pools"></a>Habilitación del escalado automático de clústeres en un grupo de nodos existente en un clúster con varios grupos de nodos

El escalador automático de clúster también se puede usar con la [característica en vista previa de varios grupos de nodos](use-multiple-node-pools.md) habilitada. Puede habilitar el escalado automático de clústeres en grupos de nodos individuales dentro de un clúster de AKS que contenga varios grupos de nodos y cumpla los requisitos descritos en la sección [Antes de comenzar](#before-you-begin). Use el comando [az aks nodepool update][az-aks-nodepool-update] para habilitar la escalabilidad automática del clúster en un grupo de nodos individual.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Posteriormente, se puede habilitar o deshabilitar el escalado automático con los comandos `az aks nodepool update --enable-cluster-autoscaler` o `az aks nodepool update --disable-cluster-autoscaler`.

## <a name="change-the-cluster-autoscaler-settings"></a>Cambiar la configuración del escalado automático de clústeres

En el paso anterior para crear un clúster de AKS o actualizar un grupo de nodos existente, el número mínimo de nodos del escalado automático de clústeres se estableció en *1*, y el número máximo de nodos se estableció en *3*. Como las necesidades de la aplicación van cambiando, es posible que deba ajustar el número de nodos del escalado automático de clústeres.

Para cambiar el número de nodos, use el comando [az aks nodepool update][az-aks-nodepool-update].

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

En el ejemplo anterior se actualiza la escalabilidad automática en el grupo de nodos *mynodepool* de *myAKSCluster* a un mínimo de *1* y un máximo de *5* nodos.

> [!NOTE]
> Durante la versión preliminar, no se puede establecer un número mínimo de nodos que sea mayor que el número establecido para el grupo de nodos. Por ejemplo, si actualmente tiene el mínimo establecido en *1*, no se puede actualizar a *3*.

Supervise el rendimiento de las aplicaciones y los servicios y ajuste la cantidad de nodos del escalado automático de clústeres para que coincida con el rendimiento necesario.

## <a name="disable-the-cluster-autoscaler"></a>Deshabilitar el escalado automático de clústeres

Si ya no quiere usar el escalado automático de clústeres, puede deshabilitarlo mediante el comando [az aks nodepool update][az-aks-nodepool-update], especificando el parámetro *--disable-cluster-autoscaler*. Los nodos no se quitan cuando se deshabilita el escalado automático de clústeres.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
