---
title: 'Conceptos: aspectos básicos de Kubernetes para Azure Kubernetes Services (AKS)'
description: Obtenga información sobre el clúster básico y los componentes de carga de trabajo de Kubernetes y cómo se relacionan con las características de Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: ab818c0bded71b4566173f4a6a720fce9bc539c3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514529"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Conceptos básicos de Kubernetes de Azure Kubernetes Service (AKS)

Como el desarrollo de aplicaciones se mueve hacia un enfoque basado en contenedor, es importante la necesidad para organizar y administrar los recursos. Kubernetes es la plataforma líder que ofrece la capacidad de proporcionar programación de confianza de cargas de trabajo de aplicación con tolerancia a errores. Azure Kubernetes Service (AKS) es un oferta de Kubernetes administrado que simplifica aún más la administración e implementación de aplicaciones basadas en contenedores.

En este artículo se presentan los componentes principales de la infraestructura de Kubernetes, como el *patrón de clúster*, los *nodos* y los *grupos de nodos*. También se presentan los recursos de la carga de trabajo, como los *pods*, las *implementaciones* y los *conjuntos*, junto con información acerca de cómo agrupar los recursos en *espacios de nombres*.

## <a name="what-is-kubernetes"></a>¿Qué es Kubernetes?

Kubernetes es una plataforma de rápida evolución que administra aplicaciones basadas en contenedores y sus componentes de red y almacenamiento asociados. El foco está en las cargas de trabajo de la aplicación, no en los componentes de infraestructura subyacente. Kubernetes proporciona un enfoque declarativo en las implementaciones, respaldado por un sólido conjunto de API para las operaciones de administración.

Puede compilar y ejecutar aplicaciones modernas, portátiles y basadas en microservicios que se benefician de Kubernetes mediante la orquestación y administración de la disponibilidad de esos componentes de la aplicación. Kubernetes admite tanto aplicaciones con estado como sin estado, a medida que los equipos progresan a través de la adopción de aplicaciones basadas en microservicios.

Como plataforma abierta, Kubernetes le permite compilar aplicaciones con el lenguaje de programación, el sistema operativo, las bibliotecas o el bus de mensajería que prefiera. La integración continua y las herramientas de entrega continua (CI/CD) existentes pueden integrarse con Kubernetes para programar e implementar versiones.

Azure Kubernetes Service (AKS) proporciona un servicio de Kubernetes administrado que reduce la complejidad de las principales tareas de administración e implementación, incluida la coordinación de actualizaciones. Los patrones de clúster de AKS se administran mediante la plataforma Azure y el usuario solo paga por los nodos de AKS que ejecuten sus aplicaciones. AKS se basa en el motor de código abierto Azure Kubernetes Service ([aks motor][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Arquitectura del clúster de Kubernetes

Un clúster de Kubernetes se divide en dos componentes:

- Los nodos del *patrón de clúster* proporcionan los servicios principales de Kubernetes y la orquestación de cargas de trabajo de la aplicación.
- Los *nodos* ejecutan las cargas de trabajo de la aplicación.

![Componentes del nodo y del patrón de clúster de Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Patrón de clúster

Al crear un clúster de AKS, se crea y se configura automáticamente un patrón de clúster. Este patrón de clúster se proporciona como un recurso de Azure administrado que se extrae del usuario. No hay ningún costo para el patrón del clúster, solo los nodos que forman parte del clúster AKS.

El patrón de clúster incluye los siguientes componentes principales de Kubernetes:

- *kube-apiserver*: el servidor de la API es el modo en el que se exponen las API de Kubernetes subyacentes. Este componente proporciona la interacción de las herramientas de administración, como `kubectl` o el panel de Kubernetes.
- *etcd*: para mantener el estado del clúster de Kubernetes y la configuración, el componente *etcd* de alta disponibilidad es un valor clave en Kubernetes.
- *kube-scheduler*: al crear o escalar aplicaciones, Scheduler determina qué nodos pueden ejecutar la carga de trabajo y los inicia.
- *kube-controller-manager*: el administrador de controladores supervisa un número de controladores más pequeños que realizan acciones como la replicación de los pods y el control de las operaciones del nodo.

AKS proporciona un patrón de clúster de inquilino único con un servidor de API dedicado, Scheduler, etc. El usuario define el número de nodos, así como su tamaño, y la plataforma Azure configura la comunicación segura entre el patrón de clúster y los nodos. La interacción con el patrón de clúster se produce a través de las API de Kubernetes, como `kubectl` o el panel de Kubernetes.

Este patrón de clúster administrado significa que no es necesario configurar componentes, como alta disponibilidad *etcd* almacén, pero también significa que no puede acceder directamente el maestro del clúster. Las actualizaciones de Kubernetes se organizan a través de la CLI de Azure o Azure Portal, que actualiza el patrón de clúster y, a continuación, los nodos. Para solucionar los posibles problemas, puede revisar los registros maestros del clúster mediante registros de Azure Monitor.

Si tiene que configurar el patrón de clúster de una manera determinada o necesita acceso directo a él, puede implementar su propio clúster de Kubernetes con [aks-engine][aks-engine].

Para las prácticas recomendadas asociadas, consulte [procedimientos recomendados de seguridad del clúster y las actualizaciones en AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nodos y grupos de nodos

Para ejecutar las aplicaciones y los servicios de soporte técnico, necesitará un *nodo* de Kubernetes. Un clúster de AKS tiene uno o varios nodos, que consiste en una máquina virtual (VM) de Azure que ejecuta los componentes del nodo de Kubernetes y el entorno de ejecución del contenedor:

- `kubelet` es el agente de Kubernetes que procesa las solicitudes de orquestación desde el patrón de clúster y la programación de la ejecución de los contenedores solicitados.
- Las redes virtuales se controlan mediante *kube-proxy* en cada nodo. El proxy enruta el tráfico de red y administra las direcciones IP para los servicios y los pods.
- El *entorno de ejecución del contenedor* es el componente que permite que las aplicaciones en contenedor ejecuten recursos adicionales e interactúen con ellos, como la red virtual y el almacenamiento. En AKS, Moby se utiliza como el tiempo de ejecución del contenedor.

![Máquina virtual de Azure y recursos auxiliares para un nodo de Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

El tamaño de la máquina virtual de Azure para los nodos define el número de CPU, la cantidad de memoria y el tamaño y tipo de almacenamiento disponible (por ejemplo, SSD de alto rendimiento o HDD normal). Si prevé que las aplicaciones requerirán gran cantidad de CPU y memoria o almacenamiento de alto rendimiento, planifique el tamaño del nodo en consecuencia. También puede escalar verticalmente el número de nodos del clúster de AKS para satisfacer la demanda.

En AKS, la imagen de máquina virtual para los nodos del clúster actualmente se basa en Ubuntu Linux o Windows Server 2019. Al crear un clúster de AKS o escalar verticalmente el número de nodos, la plataforma Azure crea el número solicitado de máquinas virtuales y las configura. No hay ninguna configuración manual para poder realizar. Los nodos de agente se facturan como máquinas virtuales estándares, tenemos los descuentos en el tamaño de máquina virtual que está usando (incluidos [reservas Azure][reservation-discounts]) se aplican automáticamente.

Si tiene que utilizar un sistema operativo de host diferente, otro entorno de ejecución del contenedor o incluir paquetes personalizados, puede implementar su propio clúster de Kubernetes mediante [aks-engine][aks-engine]. El componente `aks-engine` ascendente incluye y ofrece opciones de configuración antes de que se admitan oficialmente en los clústeres de AKS. Por ejemplo, si desea usar un contenedor en tiempo de ejecución que no sean Moby, puede usar `aks-engine` para configurar e implementar un clúster de Kubernetes que satisfaga sus necesidades actuales.

### <a name="resource-reservations"></a>Reservas de recursos

No es necesario administrar los componentes principales de Kubernetes en cada nodo, como *kubelet*, *kube-proxy* y *kube-dns*, pero consumen algunos de los recursos de proceso disponibles. Para mantener la funcionalidad y el rendimiento de los nodos, se reservan los siguientes recursos de proceso en cada nodo:

- **CPU**: 60 ms
- **Memoria**: 20 % hasta 4 GiB

Estas reservas significan que la cantidad de CPU y memoria disponibles para las aplicaciones puede parecer menos de lo que contiene el nodo propiamente dicho. Si hay restricciones de recursos debido al número de aplicaciones que se ejecutan, estas reservas garantizan que CPU y memoria permanecen disponibles para los componentes principales de Kubernetes. No se puede cambiar las reservas de recursos.

Por ejemplo:

- El tamaño de nodo **Estándar DS2 v2** contiene 2 vCPU y 7 GiB de memoria.
    - 20 % de 7 GiB de memoria = 1,4 GiB
    - Hay *(7-1.4) = 5,6 GiB* de memoria en total disponible para el nodo.
    
- El tamaño de nodo **Estándar v3 E4s** contiene 4 vCPU y 32 GiB de memoria.
    - 20% de 32 GiB de memoria = 6,4 GiB, pero AKS solo reserva hasta 4 GB
    - Hay *(32-4) = 28 GiB* en total disponible para el nodo.
    
El sistema operativo del nodo subyacente también requiere cierta cantidad de recursos de CPU y memoria para completar sus propias funciones esenciales.

Para las prácticas recomendadas asociadas, consulte [procedimientos recomendados para las características básicas de programador en AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Grupos de nodos

Los nodos de la misma configuración se agrupan en *grupos de nodos*. Un clúster de Kubernetes contiene uno o varios grupos de nodos. El número de nodos y el tamaño iniciales se definen al crear un clúster de AKS, que crea un *grupo de nodos predeterminado*. Este grupo de nodos predeterminado de AKS contiene las máquinas virtuales subyacentes que ejecutan los nodos del agente. Soporte para varios nodos grupo está actualmente en versión preliminar de AKS.

Al escalar o actualizar un clúster de AKS, la acción se realiza en el grupo de nodos predeterminado. También puede escalar o actualizar un grupo de nodos específicos. Para las operaciones de actualización, los contenedores en ejecución se programan en otros nodos del grupo de nodos hasta que todos los nodos se actualizan correctamente.

Para obtener más información sobre cómo usar varios grupos de nodos de AKS, consulte [crear y administrar varios grupos de nodos para un clúster de AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selectores de nodo

En un clúster AKS que contiene varios grupos de nodos, es posible que necesite indicar que el programador de Kubernetes a qué grupo de nodos que se usará para un recurso determinado. Por ejemplo, controladores de entrada no deben ejecutar en nodos de Windows Server (actualmente en versión preliminar de AKS). Los selectores de nodo le permiten definir varios parámetros, por ejemplo, el nodo del sistema operativo, para controlar dónde se debe programar un pod.

El siguiente ejemplo básico programa una instancia NGINX en un nodo de Linux mediante el selector de nodo *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Para obtener más información sobre cómo controlar dónde se programan pods, consulte [procedimientos recomendados para las características avanzadas de programador en AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

Kubernetes utiliza *pods* para ejecutar una instancia de la aplicación. Un pod representa una instancia individual de la aplicación. Los pods suelen tener una asignación 1:1 con un contenedor, aunque hay escenarios avanzados en los que un pod puede incluir varios contenedores. Los pods de varios contenedores se programan conjuntamente en el mismo nodo y permiten que los contenedores compartan recursos relacionados.

Al crear un pod, puede definir *límites de recursos* para solicitar una determinada cantidad de recursos de memoria o CPU. Scheduler de Kubernetes intenta programar los pods para que se ejecuten en un nodo con recursos disponibles para satisfacer la solicitud. También puede especificar los límites de recursos máximos que impiden que un determinado pod consuma demasiados recursos de proceso del nodo subyacente. Un procedimiento recomendado consiste en incluir los límites de recursos de todos los pods para ayudar a Scheduler de Kubernetes a comprender qué recursos son necesarios y cuáles se permiten.

Para obtener más información, consulte [Kubernetes pods][kubernetes-pods] (Pods de Kubernetes) y [Kubernetes pod lifecycle][kubernetes-pod-lifecycle] (Ciclo de vida de pods de Kubernetes).

Un pod es un recurso lógico, pero los contenedores se refieren al lugar donde se ejecutan las cargas de trabajo de la aplicación. Normalmente los pods son recursos desechables y efímeros, y los pods programados individualmente pierden algunas de las características de redundancia y alta disponibilidad que proporciona Kubernetes. En su lugar, los pods suelen implementarse y administrarse mediante *controladores* de Kubernetes, como el controlador de implementación.

## <a name="deployments-and-yaml-manifests"></a>Implementaciones y manifiestos YAML

Una *implementación* representa uno o varios pods idénticos, administrados por el controlador de implementación de Kubernetes. Una implementación define el número de *réplicas* (pods) que se van a crear y Scheduler de Kubernetes garantiza que, si los pods o nodos tienen algún problema, se programen pods adicionales en los nodos correctos.

Puede actualizar implementaciones para cambiar la configuración de los pods, la imagen del contenedor que se ha utilizado o el almacenamiento conectado. El controlador de implementación purga y finaliza un determinado número de réplicas, crea réplicas a partir de la nueva definición de implementación y continúa el proceso hasta que se actualizan todas las réplicas de la implementación.

La mayoría de las aplicaciones sin estado de AKS debe usar el modelo de implementación en lugar de programar pods individuales. Kubernetes puede supervisar el estado de las implementaciones para asegurarse de que se ejecute el número de réplicas necesario dentro del clúster. Cuando se programación solo pods individuales, los pods no se reinician si se produce un problema y no se vuelve a programar en los nodos en buen estado si su nodo actual encuentra un problema.

Si una aplicación requiere que un cuórum de instancias siempre esté disponible para tomar decisiones de administración, el usuario no quiere que un proceso de actualización interrumpa dicha capacidad. Los *presupuestos de interrupción de pods* se pueden usar para definir el número de réplicas de una implementación que se pueden quitar durante una actualización o la actualización de un nodo. Por ejemplo, si tiene *5* réplicas en la implementación, puede definir una interrupción del pod de *4* para permitir que solo se elimine o se vuelva a programar una réplica a la vez. Como en el caso de los límites de recursos del pod, un procedimiento recomendado consiste en definir los presupuestos de interrupciones de pods en aplicaciones que requieren que siempre esté presente un número mínimo de réplicas.

Normalmente, las implementaciones se crean o administran con `kubectl create` o `kubectl apply`. Para crear una implementación, defina un archivo de manifiesto en formato YAML (YAML no tiene lenguaje de marcado). En el ejemplo siguiente, se crea una implementación básica del servidor web NGINX. La implementación especifica que se creen *3* réplicas y que se abra el puerto *80* en el contenedor. También se definen las solicitudes de recursos y los límites de CPU y memoria.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Las aplicaciones más complejas se pueden crear incluyendo también servicios como los equilibradores de carga en el manifiesto de YAML.

Para más información, consulte [Kubernetes deployments][kubernetes-deployments] (Implementaciones de Kubernetes).

### <a name="package-management-with-helm"></a>Administración de paquetes con Helm

Un enfoque común para la administración de aplicaciones en Kubernetes es con [Helm][helm]. Puede compilar y usar *gráficos* públicos de Helm existentes que contienen una versión empaquetada del código de la aplicación y los manifiestos de YAML de Kubernetes para la implementación de recursos. Estos gráficos de Helm pueden almacenarse localmente o, con frecuencia, en un repositorio remoto, como [Azure Container Registry como un repositorio de Helm para gráficos][acr-helm].

Para usar Helm, se instala un componente de servidor denominado *Tiller* en el clúster de Kubernetes. Tiller administra la instalación de gráficos dentro del clúster. El propio cliente de Helm se instala localmente en el equipo, o bien puede utilizarse dentro de [Azure Cloud Shell][azure-cloud-shell]. Puede buscar o crear gráficos de Helm con el cliente y, a continuación, instalarlos en el clúster de Kubernetes.

![Helm incluye un componente de cliente y un componente de Tiller del lado del servidor que crea recursos dentro del clúster de Kubernetes.](media/concepts-clusters-workloads/use-helm.png)

Para más información, consulte [Instalación de aplicaciones con Helm en Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets y DaemonSets

El controlador de implementación usa Scheduler de Kubernetes para ejecutar un determinado número de réplicas en cualquier nodo disponible con recursos disponibles. Este enfoque del uso de las implementaciones puede ser suficiente para las aplicaciones sin estado, pero no para aquellas que requieren un almacenamiento o una convención de nomenclatura persistentes. Para aplicaciones que requieren que exista una réplica en cada nodo o nodos seleccionados dentro de un clúster, el controlador de implementación no observa el modo en que las réplicas se distribuyen entre los nodos.

Hay dos recursos de Kubernetes que le permiten administrar estos tipos de aplicaciones:

- *StatefulSets*: mantenga el estado de las aplicaciones más allá de un ciclo de vida de pod individual, como el almacenamiento.
- *DaemonSets*: asegure una instancia en ejecución en cada nodo al principio del proceso de arranque de Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Con frecuencia, el desarrollo de aplicaciones modernas tiene como objetivo las aplicaciones sin estado, pero *StatefulSets* puede usarse para aplicaciones con estado, como las aplicaciones que incluyen componentes de base de datos. StatefulSet es similar a una implementación en la que se crean y administran uno o varios pods idénticos. Las réplicas de StatefulSet siguen un enfoque estable y secuencial para la implementación, el escalado, las actualizaciones y las finalizaciones. Con StatefulSet, la convención de nomenclatura, los nombres de red y el almacenamiento persisten como réplicas que se vuelven a programar.

El usuario define la aplicación en formato YAML mediante `kind: StatefulSet` y el controlador de StatefulSet, a continuación, controla la implementación y administración de las réplicas necesarias. Los datos se escriben en el almacenamiento persistente, proporcionado por Azure Managed Disks o Azure Files. Con StatefulSets, el almacenamiento persistente subyacente permanece incluso cuando se elimina StatefulSet.

Para más información, consulte [Kubernetes StatefulSets][kubernetes-statefulsets] (StatefulSets de Kubernetes).

Las réplicas de StatefulSet se programan y se ejecutan en cualquier nodo disponible en un clúster de AKS. Si tiene que asegurarse de que al menos un pod del conjunto se ejecute en un nodo, puede usar DaemonSet en su lugar.

### <a name="daemonsets"></a>DaemonSets

Para la recopilación de registros específicos o las necesidades de supervisión, es posible que tenga que ejecutar un pod determinado en todos los nodos o en aquellos seleccionados. *DaemonSet* vuelve a usarse para implementar uno o varios pods idénticos, pero el controlador de DaemonSet garantiza que cada nodo especificado ejecute una instancia del pod.

El controlador de DaemonSet puede programar los pods en los nodos al principio del proceso de arranque del clúster, antes de que se haya iniciado el programador de Kubernetes predeterminado. Esta capacidad garantiza que los pods de DaemonSet se inicien antes de que se programen los pods tradicionales en una implementación o StatefulSet.

Como StatefulSets, DaemonSet se define como parte de una definición de YAML mediante `kind: DaemonSet`.

Para más información, consulte [Kubernetes DaemonSets][kubernetes-daemonset] (DaemonSets de Kubernetes).

> [!NOTE]
> Si usa el [complemento nodos virtuales](virtual-nodes-cli.md#enable-virtual-nodes-addon), DaemonSets no creará los pods en el nodo virtual.

## <a name="namespaces"></a>Espacios de nombres

Los recursos de Kubernetes, como los pods y las implementaciones, se agrupan lógicamente en un *espacio de nombres*. Estas agrupaciones proporcionan una manera de dividir de forma lógica un clúster de AKS y de restringir el acceso para crear, ver o administrar los recursos. Puede crear espacios de nombres para separar grupos de negocios, por ejemplo. Los usuarios solo pueden interactuar con los recursos dentro de sus espacios de nombres asignados.

![Espacios de nombres de Kubernetes para dividir de forma lógica los recursos y las aplicaciones](media/concepts-clusters-workloads/namespaces.png)

Cuando se crea un clúster de AKS, están disponibles los siguientes espacios de nombres:

- *default*: este espacio de nombres es donde los pods y las implementaciones se crean de forma predeterminada cuando no se proporciona ninguno. En entornos más pequeños, puede implementar aplicaciones directamente en el espacio de nombres predeterminado sin crear separaciones lógicas adicionales. Al interactuar con la API de Kubernetes, como con `kubectl get pods`, el espacio de nombres predeterminado se utiliza cuando no se especifica ninguno.
- *kube-system*: este espacio de nombres es donde existen recursos principales, como las funciones de red como DNS y proxy o el panel de Kubernetes. Normalmente, el usuario no implementa sus propias aplicaciones en este espacio de nombres.
- *kube-public*: este espacio de nombres no se utiliza normalmente, pero puede usarse para que los recursos sean visibles en todo el clúster y para que puedan verlos todos los usuarios.

Para más información, consulte [Kubernetes namespaces][kubernetes-namespaces] (Espacios de nombres de Kubernetes).

## <a name="next-steps"></a>Pasos siguientes

En este artículo se tratan algunos de los componentes básicos de Kubernetes y cómo se aplican a los clústeres de AKS. Para obtener más información sobre los conceptos básicos de Kubernetes y AKS, consulte los artículos siguientes:

- [Kubernetes / AKS access and identity][aks-concepts-identity] (Acceso e identidad de Kubernetes/AKS)
- [Kubernetes / AKS security][aks-concepts-security] (Seguridad de Kubernetes/AKS)
- [Kubernetes / AKS virtual networks][aks-concepts-network] (Redes virtuales de Kubernetes/AKS)
- [Kubernetes / AKS storage][aks-concepts-storage] (Almacenamiento de Kubernetes/AKS)
- [Kubernetes / AKS scale][aks-concepts-scale] (Escala de Kubernetes/AKS)

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md