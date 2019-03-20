---
title: Creación de un equilibrador de carga interno de Azure Kubernetes Service (AKS)
description: Aprenda a crear y usar un equilibrador de carga interno para exponer los servicios con Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: a26eab83f567a46f613e3bfda95fd99aba2b79c0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404321"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Uso de un equilibrador de carga interno con Azure Kubernetes Service (AKS)

Para restringir el acceso a las aplicaciones en Azure Kubernetes Service (AKS), puede crear y usar un equilibrador de carga interno. Un equilibrador de carga interno permite que un servicio de Kubernetes sea accesible solo para las aplicaciones que se ejecutan en la misma red virtual que el clúster de Kubernetes. En este artículo se muestra cómo crear y usar un equilibrador de carga interno con Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer está disponible en dos SKU: *Básica* y *Estándar*. Actualmente, AKS es compatible con la SKU *Básica*. Si desea usar la SKU *Estándar*, puede usar el motor [aks-engine][aks-engine] ascendente. Para más información, consulte el apartado de [comparación de las SKU de Azure Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, vea la guía de inicio rápido AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

También necesita la CLI de Azure versión 2.0.59 o posterior instalado y configurado. Ejecute  `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea  [Instalación de la CLI de Azure][install-azure-cli].

## <a name="create-an-internal-load-balancer"></a>Creación de un conjunto de equilibrador de carga interno

Para crear un equilibrador de carga interno, cree un manifiesto de servicio denominado `internal-lb.yaml` con el tipo de servicio *LoadBalancer* y la anotación *azure-load-balancer-internal*, tal como se muestra en el siguiente ejemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implementar el equilibrador de carga interno mediante la [kubectl aplicar] kubectl-aplicar] y especifique el nombre de su manifiesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Un equilibrador de carga se crea en el grupo de recursos de nodo y conectado a la misma red virtual que el clúster de AKS.

Cuando visualiza los detalles del servicio, la dirección IP del equilibrador de carga interno se muestra en la columna *EXTERNAL-IP*. En este contexto, *externo* en relación con la interfaz externa del equilibrador de carga, no es que recibe una dirección IP pública externa. La dirección IP puede tardar uno o dos minutos en cambiar de *\<pendiente\>* a una dirección IP interna real, tal como se muestra en el ejemplo siguiente:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especificación de una dirección IP

Si quiere usar una dirección IP específica con el equilibrador de carga interno, agregue la propiedad *loadBalancerIP* al manifiesto YAML del equilibrador de carga. La dirección IP especificada debe encontrarse en la misma subred que el clúster de AKS y no se debe haber asignado ya a un recurso.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Cuando se implementa y ver los detalles del servicio, la dirección IP en el *EXTERNAL-IP* columna refleja la dirección IP especificada:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Uso de redes privadas

Al crear su clúster de AKS, puede especificar la configuración de red avanzada. Este enfoque le permite implementar el clúster en una red y subredes virtuales de Azure existentes. Un escenario es implementar el clúster de AKS en una red privada conectada a su entorno local y ejecutar servicios solo accesibles internamente. Para obtener más información, consulte Configurar sus propias subredes de red virtual con [Kubenet] [ use-kubenet] o [Azure CNI][advanced-networking].

No se necesitan cambios en los pasos anteriores para implementar un equilibrador de carga interno en un clúster de AKS que utilice una red privada. El equilibrador de carga se crea en el mismo grupo de recursos que el clúster de AKS pero está conectado a la red virtual privada y subred, como se muestra en el siguiente ejemplo:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Es posible que tenga que conceder a la entidad de servicio del clúster de AKS la función *Colaborador de red* al grupo de recursos en el que están implementados los recursos de la red virtual de Azure. Consulte la entidad de servicio con [az aks show][az-aks-show], como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Para crear una asignación de roles, use el comando [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Especificación de una subred diferente

Para especificar una subred para el equilibrador de carga, agregue la anotación *azure-load-balancer-internal-subnet* al servicio. La subred especificada debe estar en la misma red virtual que el clúster de AKS. Cuando se implementa, la dirección *EXTERNAL-IP* del equilibrador de carga forma parte de la subred especificada.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Eliminación del equilibrador de carga

Cuando se han eliminado todos los servicios que usan el equilibrador de carga interno, también se elimina el propio equilibrador de carga.

También puede eliminar directamente un servicio del mismo modo que cualquier recurso de Kubernetes, como `kubectl delete service internal-app`, acción que también eliminará el equilibrador de carga de Azure subyacente.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los servicios de Kubernetes en la [documentación de servicios de Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli