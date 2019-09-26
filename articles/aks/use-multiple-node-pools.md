---
title: Uso de grupos de varios nodos de Azure Kubernetes Service (AKS)
description: Aprenda a crear y administrar grupos de varios nodos para un clúster de Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 92accf4317ef8d0e3837ce3789615b5aaf6f6919
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996896"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Versión preliminar: Creación y administración de grupos de varios nodos para un clúster de Azure Kubernetes Service (AKS).

En Azure Kubernetes Service, los nodos de la misma configuración se agrupan en *grupos de nodos*. Estos grupos de nodos contienen las máquinas virtuales subyacentes que ejecutan las aplicaciones. El número de nodos y su tamaño (SKU) inicial se definen al crear un clúster de AKS, lo cual crea un *grupo de nodos predeterminado*. Para admitir aplicaciones con diferentes necesidades de proceso o almacenamiento, puede crear grupos de nodos adicionales. Por ejemplo, puede usar estos grupos de nodos adicionales para proporcionar GPU para aplicaciones de proceso intensivo o acceso a almacenamiento SSD de alto rendimiento.

> [!NOTE]
> Esta característica le permite obtener un mayor control sobre cómo crear y administrar varios grupos de nodos. Como resultado, se requieren comandos separados para crear, actualizar y eliminar elementos. Anteriormente, las operaciones de clúster a través de `az aks create` o `az aks update` usaban la API managedCluster y eran la única opción para cambiar el plano de control y un grupo de nodo único. Esta característica expone un conjunto de operaciones independiente para los grupos de agentes a través de la API agentPool y requiere el uso del conjunto de comandos `az aks nodepool` para ejecutar operaciones en un grupo de nodos individual.

Este artículo le muestra cómo crear y administrar grupos de varios nodos en un clúster de AKS. Esta funcionalidad actualmente está en su versión preliminar.

> [!IMPORTANT]
> Las características en vista previa de AKS son de autoservicio y se tienen que habilitar. Las versiones preliminares se proporcionan "tal cual" y "como están disponibles", y están excluidas de los contratos de nivel de servicio y la garantía limitada. Las versiones preliminares de AKS reciben cobertura parcial del soporte al cliente en la medida de lo posible. Por lo tanto, estas características no están diseñadas para usarse en producción. Para obtener información adicional, consulte los siguientes artículos de soporte:
>
> * [Directivas de soporte técnico para AKS][aks-support-policies]
> * [Preguntas más frecuentes de soporte técnico de Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de empezar

Es preciso que esté instalada y configurada la versión 2.0.61 de la CLI de Azure u otra versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

Para usar grupos de varios nodos, necesitará la versión 0.4.12 de la extensión de la CLI *aks-preview* o una posterior. Instale la extensión de la CLI de Azure *aks-preview* con el comando [az extension add][az-extension-add] y, a continuación, busque las actualizaciones disponibles con el comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Registro del proveedor de características de grupos de varios nodos

Para crear un clúster de AKS que pueda usar grupos de varios nodos, primero hay que habilitar una marca de características en la suscripción. Para registrar la marca de característica *MultiAgentpoolPreview*, use el comando [az feature register][az-feature-register] tal como se muestra en el siguiente ejemplo:

> [!CAUTION]
> Actualmente, al registrar una característica en una suscripción, no se puede anular el registro de esa característica. Después de habilitar algunas características en vista previa, se pueden usar los valores predeterminados en todos los clústeres de AKS y, luego, se pueden crear en la suscripción. No habilite características en vista previa en las suscripciones de producción. Use una suscripción independiente para probar las características en vista previa y recopilar comentarios.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Todos los clústeres de AKS que cree una vez que haya registrado satisfactoriamente la marca de característica *MultiAgentpoolPreview* usarán la versión preliminar de esta experiencia de clúster. Para seguir creando clústeres normales, totalmente compatibles, no habilite características en versión preliminar en las suscripciones de producción. Utilice una suscripción independiente de prueba o de desarrollo de Azure para probar características en versión preliminar.

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
```

Cuando todo esté listo, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitaciones

Se aplican las siguientes limitaciones cuando crea y administra clústeres de AKS que admiten varios grupos de nodos:

* Los grupos de varios nodos solo están disponibles para los clústeres creados después de registrar correctamente la característica *MultiAgentpoolPreview* en la suscripción. No se pueden agregar ni administrar grupos de nodos con un clúster de AKS existente creado antes de que esta característica se registrara satisfactoriamente.
* No puede eliminar el primer grupo de nodos.
* El complemento de enrutamiento de aplicación HTTP no se puede utilizar.
* No pueden agregar ni eliminar grupos de nodos mediante una plantilla de Resource Manager como sucede con la mayoría de las operaciones. En su lugar, [use una plantilla de Resource Manager independiente](#manage-node-pools-using-a-resource-manager-template) para realizar cambios en los grupos de nodos de un clúster de AKS.

Aunque esta característica está en versión preliminar, se aplican las siguientes limitaciones adicionales:

* El clúster de AKS puede tener un máximo de ocho grupos de nodos.
* El clúster de AKS puede tener un máximo de 400 nodos distribuidos entre esos ocho grupos de nodos.
* Todos los grupos de nodos deben residir en la misma subred.

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

Para empezar, cree un clúster de AKS con un grupo de nodo único. El ejemplo siguiente usa el comando [az group create][az-group-create] para crear un grupo de recursos denominado *myResourceGroup* en la región *eastus*. Se crea un clúster de AKS denominado *myAKSCluster* mediante el comando [az aks create][az-aks-create]. Se emplea la línea de código *--kubernetes-version* con el valor *1.13.10* para mostrar cómo actualizar un grupo de nodos en un paso posterior. Puede especificar cualquier [versión admitida de Kubernetes][supported-versions].

Se recomienda usar el equilibrador de carga de SKU estándar al utilizar varios grupos de nodos. Lea [este documento](load-balancer-standard.md) para más información sobre el uso de equilibradores de carga estándar con AKS.

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
    --kubernetes-version 1.13.10 \
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
    --kubernetes-version 1.12.7
```

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
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Si no se especifica *OrchestratorVersion* o *VmSize* al agregar un grupo de nodos, los nodos se crean según los valores predeterminados del clúster de AKS. En este ejemplo, esos valores eran la versión de Kubernetes *1.13.10* y el tamaño de nodo de *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Actualización de un grupo de nodos
 
> [!NOTE]
> Las operaciones de actualización y escalado en un grupo de clústeres o nodos no se pueden realizar simultáneamente; si se intenta, se devolverá un error. En su lugar, cada tipo de operación debe completarse en el recurso de destino antes de la siguiente solicitud en ese mismo recurso. Obtenga más información al respecto en nuestra [guía de solución de problemas](https://aka.ms/aks-pending-upgrade).

Cuando se creó el clúster de AKS en el primer paso, se especificó `--kubernetes-version` con el valor *1.13.10*. De este modo, se establece la versión de Kubernetes tanto para el plano de control como para el grupo de nodos predeterminado. Los comandos de esta sección explican cómo actualizar un único grupo de nodos específico. La relación entre actualizar la versión de Kubernetes del plano de control y el grupo de nodos se explica en la [sección que tiene a continuación](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> La versión de la imagen del sistema operativo del grupo de nodos está vinculada a la versión de Kubernetes del clúster. Solo obtendrá actualizaciones de la imagen del sistema operativo cuando se haya realizado una actualización del clúster.

Como en este ejemplo hay dos grupos de nodos, debemos usar [az aks nodepool upgrade][az-aks-nodepool-upgrade] para actualizar un grupo de nodos. Vamos a actualizar *mynodepool* a Kubernetes *1.13.10*. Use el comando [az aks nodepool upgrade][az-aks-nodepool-upgrade] para actualizar el grupo de nodos tal como se muestra en el ejemplo siguiente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

> [!Tip]
> Para actualizar el plano de control a *1.14.6*, ejecute `az aks upgrade -k 1.14.6`. Para más información sobre las [actualizaciones del plano de control con varios grupos de nodos, haga clic aquí](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

Muestre el estado de los grupos de nodos de nuevo mediante el comando [az aks node pool list][az-aks-nodepool-list]. En el ejemplo siguiente se muestra que *mynodepool* se encuentra en el estado *Actualizando* a la versión *1.13.10*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Tarda unos minutos en actualizar los nodos a la versión especificada.

Se recomienda que actualice todos los grupos de nodos de un clúster de AKS a la misma versión de Kubernetes. La posibilidad de actualizar grupos de nodos individuales le permite realizar una actualización gradual y programar pods entre grupos de nodos para mantener el tiempo de actividad de las aplicaciones dentro de las restricciones antes mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Actualización del plano de control de un clúster con varios grupos de nodos

> [!NOTE]
> Kubernetes usa el esquema de versiones estándar de [Versionamiento Semántico](https://semver.org/). El número de versión se expresa como *x.y.z*, donde *x* es la versión principal, *y* es la versión secundaria y *z* es la versión de revisión. Por ejemplo, en la versión *1.12.6*, 1 es la versión principal, 12 es la versión secundaria y 6 es la versión de revisión. La versión de Kubernetes del plano de control, así como el grupo de nodos inicial, se establece durante la creación del clúster. Todos los grupos de nodos adicionales tienen establecida la versión de Kubernetes cuando se agregan al clúster. Las versiones de Kubernetes pueden diferir entre los grupos de nodos, así como entre un grupo de nodos y el plano de control, pero se aplican las restricciones siguientes:
> 
> * La versión del grupo de nodos debe tener la misma versión principal que el plano de control.
> * La versión del grupo de nodos puede ser una versión secundaria anterior a la versión del plano de control.
> * La versión del grupo de nodos puede ser cualquier versión de revisión, siempre y cuando se sigan las otras dos restricciones.

Un clúster de AKS tiene dos objetos de recurso de clúster. La primera es una versión de Kubernetes del plano de control. El segundo es un grupo de agentes con una versión de Kubernetes. Un plano de control se asigna a uno o varios grupos de nodos, y cada uno de ellos tiene su propia versión de Kubernetes. El comportamiento de una operación de actualización depende del recurso a la que vaya dirigida y de la versión de la API subyacente a la que se llame.

1. La actualización del plano de control requiere el uso de `az aks upgrade`
   * También se actualizarán todos los grupos de nodos del clúster.
1. Actualización con `az aks nodepool upgrade`
   * Esto actualizará solo el grupo de nodos de destino con la versión de Kubernetes especificada

La relación entre las versiones de Kubernetes que contienen los grupos de nodos también debe seguir un conjunto de reglas.

1. No se puede cambiar a la versión anterior de Kubernetes en el plano de control ni en el grupo de nodos.
1. Si no se especifica una versión de Kubernetes del grupo de nodos, el valor predeterminado que haya usado volverá a la versión del plano de control.
1. Se puede actualizar o escalar un plano de control o un grupo de nodos en un momento dado, pero no se pueden enviar ambas operaciones simultáneamente.
1. La versión de Kubernetes de un grupo de nodos debe tener la misma versión principal que el plano de control.
1. La versión de Kubernetes de un grupo de nodos puede ser, como máximo, dos (2) versiones secundarias menores que la del plano de control, nunca mayor.
1. Un grupo de nodos puede ser de cualquier versión de revisión de Kubernetes menor o igual que la del plano de control, nunca mayor.

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
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

AKS ofrece una característica independiente en la versión preliminar para escalar automáticamente grupos de nodos con una característica denominada [cluster autocaler](cluster-autoscaler.md). Esta característica es un complemento de AKS que se puede habilitar en función del grupo de nodos con recuentos de escala mínimos y máximos únicos por grupo de nodos. Aprenda a [ usar la escalabilidad automática del clúster en función del grupo de nodos](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

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
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

El programador de Kubernetes puede utilizar taints y tolerations para limitar las cargas de trabajo que se pueden ejecutar en los nodos.

* Un valor **taint** se aplica a un nodo que indica que solo se pueden programar pods específicos en él.
* Luego se aplica un valor **toleration** a un pod que le permite *tolerar* el valor taint de un nodo.

Para más información sobre cómo usar las características avanzadas programadas de Kubernetes, consulte [Best practices for advanced scheduler features in AKS][taints-tolerations] (Procedimientos recomendados para las características avanzadas del programador en AKS).

En este ejemplo, aplique un valor taint al nodo basado en GPU mediante el comando [kubectl taint node][kubectl-taint]. Especifique el nombre del nodo basado en GPU de la salida del comando `kubectl get nodes` anterior. El valor taint se aplica como *key:value* y como una opción de programación. En el ejemplo siguiente se usa el par *sku=gpu* y se definen los pods que, en caso contrario, tendrán la capacidad *NoSchedule*:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Administración de grupos de nodos con una plantilla de Resource Manager

Cuando usa una plantilla de Azure Resource Manager para crear y administrar recursos, normalmente puede actualizar la configuración de la plantilla y volver a implementarla para actualizar el recurso. Con grupos de nodos de AKS, el perfil del grupo de nodos inicial no se podrá actualizar una vez que se haya creado el clúster de AKS. Este comportamiento significa que no se puede actualizar una plantilla de Resource Manager existente, realizar un cambio en los grupos de nodos y volver a implementar. En su lugar, debe crear una plantilla de Resource Manager independiente que actualice solo los grupos de agentes de un clúster de AKS existente.

Cree una plantilla como `aks-agentpools.json` y pegue el siguiente ejemplo de manifiesto. Esta plantilla de ejemplo configura los valores siguientes:

* Actualiza el grupo de agentes de *Linux* denominado *myagentpool* para que ejecute tres nodos.
* Establece los nodos en el grupo de nodos para ejecutar la versión de Kubernetes *1.13.10*.
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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
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

Puede que tarde unos minutos en actualizarse el clúster de AKS según la configuración del grupo de nodos y las operaciones que defina en la plantilla de Resource Manager.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Asignar una IP pública por nodo en un grupo de nodos

> [!NOTE]
> Durante la vista previa de la asignación de una IP pública por nodo, no se puede usar con la *SKU de Standard Load Balancer en AKS* debido a posibles reglas de equilibrador de carga en conflicto con el aprovisionamiento de la máquina virtual. Durante la versión preliminar,use la *SKU de Load Balancer básico* si necesita asignar una dirección IP pública por nodo.

Los nodos de AKS no necesitan sus propias direcciones IP públicas para la comunicación. Sin embargo, algunos escenarios pueden requerir que los nodos de un grupo de nodos tengan sus propias direcciones IP públicas. Un ejemplo son los juegos, en los que se necesita una consola para tener una conexión directa a una máquina virtual en la nube para minimizar los saltos. Esto se puede lograr si se registra para una característica en vista previa (GB) independiente: IP pública de nodo (versión preliminar).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Después de realizar el registro correctamente, implemente una plantilla de Azure Resource Manager siguiendo las mismas instrucciones que se detallaron [antes](#manage-node-pools-using-a-resource-manager-template) y agregue la siguiente propiedad de valor booleano "enableNodePublicIP" en agentPoolProfiles. Establezca esta opción en `true` ya que, de forma predeterminada, se establecerá en `false` si no se especifica. Esta es una propiedad de tiempo de creación y requiere una versión mínima de API de 2019-06-01. Esto se puede aplicar a los grupos de nodos de Linux y Windows.

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

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
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
