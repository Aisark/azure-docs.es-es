---
title: Uso de grupos de varios nodos de Azure Kubernetes Service (AKS)
description: Aprenda a crear y administrar grupos de varios nodos para un clúster de Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/14/2020
ms.author: mlearned
ms.openlocfilehash: e77710fe446810ec566ebc7088d802f0721806d2
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443931"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Creación y administración de varios grupos de nodos para un clúster de Azure Kubernetes Service (AKS)

En Azure Kubernetes Service, los nodos de la misma configuración se agrupan en *grupos de nodos*. Estos grupos de nodos contienen las máquinas virtuales subyacentes que ejecutan las aplicaciones. El número de nodos y su tamaño (SKU) inicial se definen al crear un clúster de AKS, lo cual crea un *grupo de nodos predeterminado*. Para admitir aplicaciones con diferentes necesidades de proceso o almacenamiento, puede crear grupos de nodos adicionales. Por ejemplo, puede usar estos grupos de nodos adicionales para proporcionar GPU para aplicaciones de proceso intensivo o acceso a almacenamiento SSD de alto rendimiento.

> [!NOTE]
> Esta característica le permite obtener un mayor control sobre cómo crear y administrar varios grupos de nodos. Como resultado, se requieren comandos separados para crear, actualizar y eliminar elementos. Anteriormente, las operaciones de clúster a través de `az aks create` o `az aks update` usaban la API managedCluster y eran la única opción para cambiar el plano de control y un grupo de nodo único. Esta característica expone un conjunto de operaciones independiente para los grupos de agentes a través de la API agentPool y requiere el uso del conjunto de comandos `az aks nodepool` para ejecutar operaciones en un grupo de nodos individual.

Este artículo le muestra cómo crear y administrar grupos de varios nodos en un clúster de AKS.

## <a name="before-you-begin"></a>Antes de empezar

Es preciso que esté instalada y configurada la versión 2.0.76 de la CLI de Azure, o cualquier otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].

## <a name="limitations"></a>Limitaciones

Se aplican las siguientes limitaciones cuando crea y administra clústeres de AKS que admiten varios grupos de nodos:

* Consulte [Cuotas, restricciones de tamaño de máquinas virtuales y disponibilidad de regiones en Azure Kubernetes Service (AKS)][quotas-skus-regions].
* No puede eliminar el primer grupo de nodos.
* El complemento de enrutamiento de aplicación HTTP no se puede utilizar.
* El clúster de AKS debe usar el equilibrador de carga de SKU estándar para usar varios grupos de nodos; la característica no es compatible con los equilibradores de carga de SKU básica.
* El clúster de AKS debe usar conjuntos de escalado de máquinas virtuales para los nodos.
* El nombre de un grupo de nodos solo puede contener caracteres alfanuméricos en minúsculas y debe comenzar con una letra minúscula. En el caso de los grupos de nodos de Linux, la longitud debe estar comprendida entre 1 y 12 caracteres. Para los grupos de nodos de Windows, la longitud debe estar comprendida entre 1 y 6 caracteres.
* Todos los grupos de nodos deben residir en la misma red virtual y la misma subred.
* Al crear varios grupos de nodos durante la creación del clúster, todas las versiones de Kubernetes que se usen en los grupos de nodos deben coincidir con la versión establecida para el plano de control. Se puede actualizar después de aprovisionar el clúster mediante operaciones en función del grupo de nodos.

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

Para empezar, cree un clúster de AKS con un grupo de nodo único. El ejemplo siguiente usa el comando [az group create][az-group-create] para crear un grupo de recursos denominado *myResourceGroup* en la región *eastus*. Se crea un clúster de AKS denominado *myAKSCluster* mediante el comando [az aks create][az-aks-create]. Se emplea la línea de código *--kubernetes-version* con el valor *1.15.7* para mostrar cómo se actualiza un grupo de nodos en un paso posterior. Puede especificar cualquier [versión admitida de Kubernetes][supported-versions].

> [!NOTE]
> **No se admite** la SKU *Básico* del equilibrador de carga cuando se usan varios grupos de nodos. De forma predeterminada, los clústeres de AKS se crean con el equilibrador de carga de SKU *Estándar* de la CLI de Azure y Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

La operación de creación del clúster tarda unos minutos.

> [!NOTE]
> Para asegurarse de que el clúster funciona de forma confiable, debe ejecutar al menos dos nodos en el grupo de nodos predeterminado, ya que los servicios esenciales del sistema se ejecutan en este grupo de nodos.

Cuando el clúster esté listo, utilice el comando [az aks get-credentials][az-aks-get-credentials] para obtener las credenciales del clúster para su uso con `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adición de un grupo de nodos

El clúster creado en el paso anterior tiene un grupo de nodo único. Vamos a agregar un segundo grupo de nodos mediante el comando [az aks node pool add][az-aks-nodepool-add]. En el ejemplo siguiente se crea un grupo de nodos denominado *mynodepool* que ejecuta *3* nodos:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> El nombre de un grupo de nodos debe empezar con una letra minúscula y solo puede contener caracteres alfanuméricos. En el caso de los grupos de nodos de Linux, la longitud debe estar comprendida entre 1 y 12 caracteres. Para los grupos de nodos de Windows, la longitud debe estar comprendida entre 1 y 6 caracteres.

Para ver el estado de los grupos de nodos, use el comando [az aks node pool list][az-aks-nodepool-list] y especifique el nombre del clúster y del grupo de recursos:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

En la siguiente salida de ejemplo se puede ver que *mynodepool* se ha creado correctamente con tres nodos en el grupo de nodos. Cuando se creó el clúster de AKS en el paso anterior, también se creó un grupo de nodos predeterminado denominado *nodepool1* con *2* nodos.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Si no se especifica *VmSize* al agregar un grupo de nodos, el tamaño predeterminado será *Standard_DS2_v3* para los grupos de nodos de Windows y *Standard_DS2_v2* para los grupos de nodos de Linux. Si no se especifica *OrchestratorVersion*, se establecerá de forma predeterminada en la misma versión que el plano de control.

## <a name="upgrade-a-node-pool"></a>Actualización de un grupo de nodos

> [!NOTE]
> Las operaciones de actualización y escalado en un grupo de clústeres o nodos no se pueden realizar simultáneamente. Si se intenta, se devuelve un error. En su lugar, cada tipo de operación debe completarse en el recurso de destino antes de la siguiente solicitud en ese mismo recurso. Obtenga más información al respecto en nuestra [guía de solución de problemas](https://aka.ms/aks-pending-upgrade).

Cuando se creó el clúster de AKS en el primer paso, se especificó `--kubernetes-version` con el valor *1.15.7*. De este modo, se establece la versión de Kubernetes tanto para el plano de control como para el grupo de nodos predeterminado. Los comandos de esta sección explican cómo actualizar un único grupo de nodos específico.

La relación entre actualizar la versión de Kubernetes del plano de control y el grupo de nodos se explica en la [sección que tiene a continuación](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> La versión de la imagen del sistema operativo del grupo de nodos está vinculada a la versión de Kubernetes del clúster. Solo obtendrá actualizaciones de la imagen del sistema operativo cuando se haya realizado una actualización del clúster.

Como en este ejemplo hay dos grupos de nodos, debemos usar [az aks nodepool upgrade][az-aks-nodepool-upgrade] para actualizar un grupo de nodos. Vamos a actualizar *mynodepool* a Kubernetes *1.15.7*. Use el comando [az aks nodepool upgrade][az-aks-nodepool-upgrade] para actualizar el grupo de nodos tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Muestre el estado de los grupos de nodos de nuevo mediante el comando [az aks node pool list][az-aks-nodepool-list]. En el ejemplo siguiente, se muestra que *mynodepool* se encuentra en el estado *Actualizando* a la versión *1.15.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Tarda unos minutos en actualizar los nodos a la versión especificada.

Se recomienda que actualice todos los grupos de nodos de un clúster de AKS a la misma versión de Kubernetes. El comportamiento predeterminado de `az aks upgrade` es actualizar todos los grupos de nodos junto con el plano de control para lograr esta alineación. La posibilidad de actualizar grupos de nodos individuales le permite realizar una actualización gradual y programar pods entre grupos de nodos para mantener el tiempo de actividad de las aplicaciones dentro de las restricciones antes mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Actualización del plano de control de un clúster con varios grupos de nodos

> [!NOTE]
> Kubernetes usa el esquema de versiones estándar de [Versionamiento Semántico](https://semver.org/). El número de versión se expresa como *x.y.z*, donde *x* es la versión principal, *y* es la versión secundaria y *z* es la versión de revisión. Por ejemplo, en la versión *1.12.6*, 1 es la versión principal, 12 es la versión secundaria y 6 es la versión de revisión. Las versiones de Kubernetes del plano de control y del grupo de nodos inicial se establecen durante la creación del clúster. Todos los grupos de nodos adicionales tienen establecida la versión de Kubernetes cuando se agregan al clúster. Las versiones de Kubernetes pueden diferir entre los grupos de nodos, así como entre un grupo de nodos y el plano de control.

Un clúster de AKS tiene dos objetos de recursos de clúster con las versiones de Kubernetes asociadas.

1. Un plano de control del clúster con una versión de Kubernetes.
2. Un grupo de nodos con una versión de Kubernetes.

Un plano de control se asigna a uno o varios grupos de nodos. El comportamiento de una operación de actualización depende del comando de la CLI de Azure que se use.

La actualización de un plano de control de AKS requiere el uso de `az aks upgrade`. De este modo se actualiza la versión del plano de control y todos los grupos de nodos del clúster. 

Si se emite el comando `az aks upgrade` con la marca `--control-plane-only`, solo se actualiza el plano de control del clúster. No se cambia ninguno de los grupos de nodos asociados del clúster.

La actualización de los grupos de nodos individuales requiere el uso de `az aks nodepool upgrade`. Esto actualiza solo el grupo de nodos de destino con la versión de Kubernetes especificada.

### <a name="validation-rules-for-upgrades"></a>Reglas de validación para actualizaciones

Las actualizaciones válidas de Kubernetes del plano de control o de los grupos de nodos de un clúster se validan mediante los siguientes conjuntos de reglas.

* Reglas de versiones válidas para actualizar grupos de nodos:
   * La versión del grupo de nodos debe tener la misma versión *principal* que el plano de control.
   * La versión del grupo de nodos *secundaria* debe estar dentro de dos versiones *secundarias* de la versión del plano de control.
   * La versión del grupo de nodos no puede ser mayor que la versión `major.minor.patch` de control.

* Reglas para enviar una operación de actualización:
   * No se puede cambiar a la versión anterior de Kubernetes en el plano de control ni en el grupo de nodos.
   * Si no se especifica una versión de Kubernetes del grupo de nodos, el comportamiento depende del cliente que se use. La declaración en las plantillas de Resource Manager revierte a versión existente definida para el grupo de nodos. Si no se establece ninguna, se usa la versión del plano de control para la reversión.
   * Puede actualizar o escalar un plano de control o un grupo de nodos en un momento dado, pero no puede enviar simultáneamente varias operaciones en un único recurso de plano de control o de grupo de nodos.

## <a name="scale-a-node-pool-manually"></a>Escalado manual de un grupo de nodos

A medida que la carga de trabajo de las aplicaciones cambia, puede que tenga que escalar el número de nodos de un grupo de nodos. El número de nodos se puede escalar o reducir verticalmente.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para escalar el número de nodos de un grupo de nodos, use el comando [az aks node pool scale][az-aks-nodepool-scale]. En el ejemplo siguiente se escala el número de nodos de *mynodepool* a *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Muestre el estado de los grupos de nodos de nuevo mediante el comando [az aks node pool list][az-aks-nodepool-list]. El ejemplo siguiente muestra que *mynodepool* está en el estado *Escalando* con un nuevo recuento de *5* nodos:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

La operación de escalado tarda unos minutos en completarse.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Escalar un grupo de nodos específico automáticamente habilitando para ello la escalabilidad automática del clúster

AKS ofrece una característica independiente para escalar automáticamente grupos de nodos con una característica denominada [cluster autocaler](cluster-autoscaler.md). Esta característica se puede habilitar en función del grupo de nodos con recuentos de escala mínimos y máximos únicos por grupo de nodos. Aprenda a [ usar la escalabilidad automática del clúster en función del grupo de nodos](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Eliminación de un grupo de nodos

Si ya no necesita un grupo, puede eliminarlo y quitar los nodos de máquinas virtuales subyacentes. Para eliminar un grupo de nodos, use el comando [az aks node pool delete][az-aks-nodepool-delete] y especifique el nombre del grupo de nodos. El ejemplo siguiente elimina el grupo *mynoodepool* que se creó en los pasos anteriores:

> [!CAUTION]
> No hay ninguna opción de recuperación para la pérdida de datos que se puede producir cuando se elimina un grupo de nodos. Si no se pueden programar pods en otros grupos de nodos, esas aplicaciones dejarán de estar disponibles. Asegúrese de que no elimina un grupo de nodos si las aplicaciones en uso no disponen de copias de seguridad de los datos o de la posibilidad de ejecutarse en otros grupos de nodos del clúster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

En la siguiente salida de ejemplo del comando [az aks node pool list][az-aks-nodepool-list] se puede ver que *mynodepool* se encuentra en el estado *Eliminando*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Tarda unos minutos en eliminar los nodos y el grupo de nodos.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificación de un tamaño de máquina virtual para un grupo de nodos

En los ejemplos anteriores para crear un grupo de nodos, se usó un tamaño de máquina virtual predeterminado para los nodos creados en el clúster. Un escenario habitual consiste en crear grupos de nodos con diferentes tamaños y funcionalidades de máquina virtual. Por ejemplo, puede crear un grupo de nodos que contenga nodos con grandes cantidades de memoria o CPU, o un grupo de nodos que proporcione compatibilidad con GPU. En el paso siguiente, se [usan taints y tolerations](#schedule-pods-using-taints-and-tolerations) para indicar al programador de Kubernetes cómo limitar el acceso a los pods que se pueden ejecutar en esos nodos.

En el ejemplo siguiente, cree un grupo de nodos basado en GPU que use el tamaño de máquina virtual *Standard_NC6*. Estas máquinas virtuales disponen de una tarjeta Tesla K80 de NVIDIA. Para más información sobre los tamaños de máquina virtual disponibles, consulte [Tamaños de las máquinas virtuales Linux en Azure][vm-sizes].

Cree un grupo de nodos mediante el comando [az aks node pool add][az-aks-nodepool-add] de nuevo. Esta vez, especifique el nombre *gpunodepool* y use el parámetro `--node-vm-size` para especificar el tamaño *Standard_NC6*:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

En la siguiente salida de ejemplo del comando [az aks node pool list][az-aks-nodepool-list] se puede ver que *gpunodepool* está *Creando* nodos con el *VmSize* especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Tarda unos minutos en crear correctamente *gpunodepool*.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Programación de pods con taints y tolerations

Ahora tiene dos grupos de nodos en el clúster, el grupo de nodos predeterminado que se creó inicialmente y el grupo de nodos basado en GPU. Use el comando [kubectl get nodes][kubectl-get] para ver los nodos del clúster. En la siguiente salida de ejemplo se muestran los nodos:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

El programador de Kubernetes puede utilizar taints y tolerations para limitar las cargas de trabajo que se pueden ejecutar en los nodos.

* Un valor **taint** se aplica a un nodo que indica que solo se pueden programar pods específicos en él.
* Luego se aplica un valor **toleration** a un pod que le permite *tolerar* el valor taint de un nodo.

Para más información sobre cómo usar las características avanzadas programadas de Kubernetes, consulte [Best practices for advanced scheduler features in AKS][taints-tolerations] (Procedimientos recomendados para las características avanzadas del programador en AKS).

En este ejemplo, aplique un valor taint al nodo basado en GPU mediante el comando --node-taints. Especifique el nombre del nodo basado en GPU de la salida del comando `kubectl get nodes` anterior. El valor taint se aplica como *key:value* y como una opción de programación. En el ejemplo siguiente se usa el par *sku=gpu* y se definen los pods que, en caso contrario, tendrán la capacidad *NoSchedule*:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

El siguiente manifiesto YAML básico de ejemplo usa un valor toleration para permitir al programador de Kubernetes ejecutar un pod NGINX en el nodo basado en GPU. Para ver un ejemplo más adecuado pero que requiere mucho tiempo para ejecutar un trabajo de Tensorflow en el conjunto de datos de MNIST, consulte [Use GPUs for compute-intensive workloads on AKS][gpu-cluster] (Uso de GPU para cargas de trabajo de procesos intensivos en AKS).

Cree un archivo denominado `gpu-toleration.yaml` y cópielo en el ejemplo siguiente de YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Programe el pod mediante el comando `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Se tarda unos segundos en programar el pod y extraer la imagen NGINX. Use el comando [kubectl describe pod][kubectl-describe] para ver el estado del pod. En la siguiente salida de ejemplo reducida se puede ver que se aplica el valor *sku=gpu:NoSchedule* a toleration. En la sección de eventos, el programador ha asignado el pod al nodo basado en GPU *aks-gpunodepool-28993262-vmss000000*:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Solo los pods a los que se ha aplicado este valor taint se pueden programar en los nodos de *gpunodepool*. Cualquier otro pod se tendría que programar en el grupo de nodos *nodepool1*. Si crea grupos de nodos adicionales, puede usar valores taint y toleration adicionales para limitar los pods que se pueden programar en esos recursos del nodo.

## <a name="specify-a-tag-for-a-node-pool"></a>Especificación de una etiqueta para un grupo de nodos

Puede aplicar una etiqueta de Azure a grupos de nodos en su clúster de AKS. Las etiquetas aplicadas a un grupo de nodos se aplican a cada nodo del grupo y se conservan de una actualización a otra. También se aplican etiquetas a los nuevos nodos que se agregan a un grupo durante las operaciones de escalado horizontal. Agregar una etiqueta puede ayudar con tareas como el seguimiento de directivas o la estimación de costos.

> [!IMPORTANT]
> Para usar etiquetas de grupo de nodos, necesitará la extensión de la CLI *aks-preview* versión 0.4.29 o posterior. Instale la extensión de la CLI de Azure *aks-preview* con el comando [az extension add][az-extension-add] y, a continuación, busque las actualizaciones disponibles con el comando [az extension update][az-extension-update]:
> 
> ```azurecli-interactive
> # Install the aks-preview extension
> az extension add --name aks-preview
> 
> # Update the extension to make sure you have the latest version installed
> az extension update --name aks-preview
> ```

Cree un grupo de nodos mediante el comando [az aks node pool add][az-aks-nodepool-add] de nuevo. Especifique el nombre *tagnodepool* y use el parámetro `--tag` para especificar *dept=IT* y *costcenter=9999* para las etiquetas.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> También puede usar el parámetro `--tags` cuando use el comando [az aks nodepool update][az-aks-nodepool-update], y también durante la creación del clúster. Durante la creación del clúster, el parámetro `--tags` aplica la etiqueta al grupo de nodos inicial que se crea con el clúster. Todos los nombres de etiqueta deben cumplir las limitaciones de [Uso de etiquetas para organizar los recursos de Azure][tag-limitation]. Al actualizar un grupo de nodos con el parámetro `--tags`, se actualizan los valores de etiqueta existentes y se anexan las etiquetas nuevas. Por ejemplo, si el grupo de nodos tuviera *dept=IT* y *costcenter=9999* en las etiquetas y lo actualizara a *team=dev* y *costcenter=111*, el grupo de nodos tendría *dept=IT*, *costcenter=111* y *team=dev* como etiquetas.

En la siguiente salida de ejemplo del comando [az aks nodepool list][az-aks-nodepool-list] se puede ver que *tagnodepool* está creando (*Creating*) nodos con el valor de *tag* especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Administración de grupos de nodos con una plantilla de Resource Manager

Cuando usa una plantilla de Azure Resource Manager para crear y administrar recursos, normalmente puede actualizar la configuración de la plantilla y volver a implementarla para actualizar el recurso. Con grupos de nodos de AKS, el perfil del grupo de nodos inicial no se podrá actualizar una vez que se haya creado el clúster de AKS. Este comportamiento significa que no se puede actualizar una plantilla de Resource Manager existente, realizar un cambio en los grupos de nodos y volver a implementar. En su lugar, debe crear una plantilla de Resource Manager independiente que actualice solo los grupos de nodos de un clúster de AKS existente.

Cree una plantilla como `aks-agentpools.json` y pegue el siguiente ejemplo de manifiesto. Esta plantilla de ejemplo configura los valores siguientes:

* Actualiza el grupo de nodos de *Linux* denominado *myagentpool* para que ejecute tres nodos.
* Establece los nodos del grupo para ejecutar la versión de Kubernetes *1.15.7*.
* Define el tamaño del nodo como *Standard_DS2_v2*.

Edite estos valores para actualizar, agregar o eliminar grupos de nodos según sea necesario:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Implemente esta plantilla mediante el comando [az group deployment create][az-group-deployment-create], tal como se muestra en el ejemplo siguiente. Se le solicitará el nombre y la ubicación del clúster de AKS existente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Para agregar una etiqueta al grupo de nodos, utilice la propiedad *tag* en la plantilla, tal y como se muestra en el ejemplo siguiente.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Puede que tarde unos minutos en actualizarse el clúster de AKS según la configuración del grupo de nodos y las operaciones que defina en la plantilla de Resource Manager.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Asignar una IP pública por nodo en un grupo de nodos

> [!WARNING]
> Durante la vista previa de la asignación de una IP pública por nodo, no se puede usar con la *SKU de Standard Load Balancer en AKS* debido a posibles reglas de equilibrador de carga en conflicto con el aprovisionamiento de la máquina virtual. Como consecuencia de esta limitación, los grupos de agentes de Windows no se admiten con esta característica en vista previa. Durante la versión preliminar, debe usar la *SKU básica de Load Balancer* si necesita asignar una dirección IP pública por nodo.

Los nodos de AKS no necesitan sus propias direcciones IP públicas para la comunicación. Sin embargo, algunos escenarios pueden requerir que los nodos de un grupo de nodos tengan sus propias direcciones IP públicas. Un ejemplo son los juegos, en los que se necesita una consola para tener una conexión directa a una máquina virtual en la nube para minimizar los saltos. Esto se puede lograr si se registra para una característica en vista previa (GB) independiente: IP pública de nodo (versión preliminar).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Después de realizar el registro correctamente, implemente una plantilla de Azure Resource Manager siguiendo las mismas instrucciones que se detallaron [antes](#manage-node-pools-using-a-resource-manager-template) y agregue la propiedad de valor booleano `enableNodePublicIP` en agentPoolProfiles. Establezca el valor en `true` ya que, de forma predeterminada, se establece en `false` si no se especifica. Esta es una propiedad de tiempo de creación y requiere una versión mínima de API de 2019-06-01. Esto se puede aplicar a los grupos de nodos de Linux y Windows.

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, ha creado un clúster de AKS que incluye nodos basados en GPU. Para reducir costos innecesarios, puede que desee eliminar *gpunodepool* o todo el clúster de AKS.

Para eliminar el grupo de nodos basado en GPU, use el comando [az aks nodepool delete][az-aks-nodepool-delete] tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para eliminar el clúster propiamente dicho, use el comando [az group delete][az-group-delete] para eliminar el grupo de recursos de AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido a crear y administrar grupos de varios nodos en un clúster de AKS. Para más información sobre cómo controlar pods en grupos de nodos, consulte [Best practices for advanced scheduler features in AKS][operator-best-practices-advanced-scheduler] (Procedimientos recomendados para las características avanzadas del programador en AKS).

Para crear y usar grupos de nodos de contenedores de Windows Server, consulte [Creación de un contenedor de Windows Server en AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md