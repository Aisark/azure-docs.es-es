---
title: Use una dirección IP estática con el equilibrador de carga de Azure Kubernetes Service (AKS).
description: Aprenda a crear y usar una dirección IP estática con el equilibrador de carga de Azure Kubernetes Service (AKS).
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031656"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar una dirección IP pública estática con el equilibrador de carga de Azure Kubernetes Service (AKS)

De forma predeterminada, la dirección IP pública asignada a un recurso de equilibrador de carga creado por un clúster de AKS solo es válida para la duración de ese recurso. Si elimina el servicio de Kubernetes, el equilibrador de carga asociado y la dirección IP también se eliminan. Si quiere asignar una dirección IP específica o conservar una dirección IP para los servicios de Kubernetes reimplementados, puede crear y usar una dirección IP pública estática.

En este artículo se muestra cómo crear una dirección IP pública estática y asignarla al servicio de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, vea la guía de inicio rápido AKS [mediante la CLI de Azure][aks-quickstart-cli] o [mediante Azure Portal][aks-quickstart-portal].

También necesita la CLI de Azure versión 2.0.59 o posterior instalado y configurado. Ejecute  `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea  [Instalación de la CLI de Azure][install-azure-cli].

Actualmente, solo *SKU básica de IP*se admite. Trabajo está en curso para admitir la *IP estándar* SKU de recurso. Para obtener más información, consulte [tipos y métodos de asignación de Azure de direcciones IP][ip-sku].

## <a name="create-a-static-ip-address"></a>Crear una dirección IP estática

Cuando se crea una dirección IP pública estática para usarla con AKS, el recurso de dirección IP debe crearse en el grupo de recursos del **nodo**. Si desea separar los recursos, vea la sección siguiente para [Use una dirección IP estática fuera del grupo de recursos del nodo](#use-a-static-ip-address-outside-of-the-node-resource-group).

En primer lugar, obtenga el nombre del grupo de recursos de nodo con el [show de az aks] [ az-aks-show] de comandos y agregue el `--query nodeResourceGroup` parámetro de consulta. En este ejemplo se obtiene el grupo de recursos del nodo para el nombre de clúster de AKS *myAKSCluster* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

Cree una dirección IP pública estática con el comando [az network public ip create][az-network-public-ip-create]. Especifique el nombre del grupo de recursos del nodo que obtuvo en el comando anterior y, después, un nombre para el recurso de dirección IP, como *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

Se muestra la dirección IP, como se muestra en la siguiente salida de ejemplo reducido:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Después puede obtener la dirección IP pública mediante el comando [az network public-ip list][az-network-public-ip-list]. Especifique el nombre del grupo de recursos del nodo y la dirección IP pública creados y envíe una consulta para *ipAddress*, como se muestra en este ejemplo:

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Crear un servicio mediante la dirección IP estática

Para crear un servicio con la dirección IP pública estática, agregue la propiedad `loadBalancerIP` y el valor de la dirección IP pública estática al manifiesto de YAML. Cree un archivo denominado `load-balancer-service.yaml` y cópielo en el siguiente código YAML. Indique su propia dirección IP pública que creó en el paso anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Cree el servicio y la implementación con el comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Uso de una dirección IP estática fuera del grupo de recursos del nodo

Con Kubernetes 1.10 o versiones posteriores, puede usar una dirección IP estática que se haya creado fuera del grupo de recursos del nodo. La entidad de servicio utilizada por el clúster de AKS debe tener permisos delegados al otro grupo de recursos, como se muestra en el siguiente ejemplo:

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para utilizar una dirección IP fuera del grupo de recursos del nodo, agregue una anotación a la definición de servicio. En el ejemplo siguiente se establece el grupo de recursos denominado *myResourceGroup*. Indique su propio nombre del grupo de recursos:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Solución de problemas

Si ha definido la dirección IP estática en el *loadBalancerIP* propiedad del manifiesto de servicio de Kubernetes no existe o no se ha creado en el grupo de recursos de nodo y no hay delegaciones adicionales configurados, el servicio del equilibrador de carga no se puede crear. Para solucionar este problema, revise los eventos de creación del servicio con el comando [kubectl describe][kubectl-describe]. Indique el nombre del servicio tal y como aparece en el manifiesto de YAML, como se muestra en este ejemplo:

```console
kubectl describe service azure-load-balancer
```

Se muestra información sobre el recurso de servicio de Kubernetes. Los *Eventos* al final de la salida de este ejemplo indican que *no se encontró la dirección IP proporcionada por el usuario*. En estos casos, compruebe que ha creado la dirección IP pública estática en el grupo de recursos del nodo y que la dirección IP especificada en el manifiesto de servicio de Kubernetes es correcta.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Pasos siguientes

Para un control adicional sobre el tráfico de red a las aplicaciones, puede que sea mejor [crear un controlador de entrada][aks-ingress-basic]. También puede [crear un controlador de entrada con una dirección IP pública estática][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku