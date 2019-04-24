---
title: 'Procedimientos recomendados de operador: características básicas del programador en Azure Kubernetes Service (AKS)'
description: Conozca las prácticas recomendadas para utilizar características básicas del programador, como las cuotas de recursos y los presupuestos de interrupciones de pods en Azure Kubernetes Service (AKS).
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 11/26/2018
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: 8233330973946e552e36a85a11bdbbfb06c739f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60463887"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Procedimientos recomendados para características básicas del programador en Azure Kubernetes Service (AKS)

A medida que administra los clústeres en Azure Kubernetes Service (AKS), a menudo necesita aislar los equipos y las cargas de trabajo. El programador de Kubernetes proporciona características que le permiten controlar la distribución de los recursos de proceso, o limitar el impacto de los eventos de mantenimiento.

Este artículo de procedimientos recomendados se centra en las características básicas de Kubernetes para operadores de clúster. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Utilizar las cuotas de recursos para proporcionar una cantidad fija de recursos para cargas de trabajo o equipos
> * Limitar el impacto del mantenimiento programado utilizando presupuestos de interrupciones de pods
> * Comprobar si faltan solicitudes y límites de recursos de pods con la herramienta `kube-advisor`

## <a name="enforce-resource-quotas"></a>Aplicación de cuotas de recursos

**Orientación con procedimientos recomendados.**: planeación y aplicación de cuotas de recursos a nivel de espacio de nombres. Si los pods no definen solicitudes y límites de recursos, rechace la implementación. Supervise el uso de recursos y ajuste las cuotas según sea necesario.

Las solicitudes y límites de recursos se colocan en la especificación de pod. Estos límites se usan por el programador de Kubernetes en el momento de la implementación para buscar un nodo disponible en el clúster. Estos límites y las solicitudes funcionan en el nivel de pod individual. Para obtener más información sobre cómo definir estos valores, vea [Define pod resource requests and limits][resource-limits] (Definición de los límites y solicitudes de recursos del pod).

Para proporcionar una manera de reservar y limitar los recursos a través de un proyecto o equipo de desarrollo, debe usar *las cuotas de recursos*. Estas cuotas se definen en un espacio de nombres y se pueden usar para establecer cuotas de la siguiente manera:

* **Recursos de proceso**, como CPU y memoria, o GPU.
* **Recursos de almacenamiento**, que incluyen el número total de volúmenes o la cantidad de espacio en disco para una clase de almacenamiento determinada.
* **Recuento de objetos**, como el número máximo de secretos, servicios o trabajos que se pueden crear.

Kubernetes no sobrecarga los recursos. Una vez que el total acumulado de solicitudes o límites de recursos supera la cuota asignada, ya no se permiten más implementaciones.

Al definir las cuotas de recursos, todos los pods creados en el espacio de nombres deben proporcionar límites o recursos en sus especificaciones de pods. Si no proporcionan estos valores, puede rechazar la implementación. En su lugar, puede [configurar solicitudes y límites predeterminados para un espacio de nombres][configure-default-quotas].

El siguiente manifiesto YAML de ejemplo llamado *dev-app-team-quotas.yaml* establece un límite rígido de un total de *10* CPU, *20Gi* de memoria y *10* pods:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Esta cuota de recursos se puede aplicar mediante la especificación del espacio de nombres, como *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabaje con los desarrolladores y propietarios de su aplicación para conocer sus necesidades y aplicar las cuotas de recursos adecuadas.

Para obtener más información acerca de los objetos de recursos, ámbitos y prioridades disponibles, consulte [Resource quotas in Kubernetes][k8s-resource-quotas] (Cuotas de recursos en Kubernetes).

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planeación de disponibilidad mediante presupuestos de interrupciones de pods

**Orientación con procedimientos recomendados**: para mantener la disponibilidad de las aplicaciones, defina presupuestos de interrupciones de pods para asegurarse de que haya un número mínimo de pods disponible en el clúster.

Hay dos eventos de interrupción que provocan que se quiten los pods:

* Las *interrupciones involuntarias* son eventos más allá del control típico del operador del clúster o el propietario de la aplicación.
  * Estas interrupciones involuntarias incluyen un error de hardware en el equipo físico, una alerta de pánico de kernel o la eliminación de una máquina virtual del nodo.
* Las *interrupciones voluntarias* son eventos solicitados por el operador del clúster o el propietario de la aplicación.
  * Estas interrupciones voluntarias incluyen actualizaciones del clúster, la actualización de una plantilla de implementación o la eliminación accidental de un pod.

Las interrupciones involuntarias se pueden mitigar utilizando varias réplicas de los pods en una implementación. La ejecución de varios nodos en el clúster de AKS ayuda también con estas interrupciones voluntarias. Para las interrupciones voluntarias, Kubernetes proporciona *presupuestos de interrupciones de pods* que permiten al operador del clúster definir un número máximo de recursos no disponibles o un número mínimo de recursos disponibles. Estos presupuestos de interrupciones de pods le permiten planear cómo responden las implementaciones o los conjuntos de réplicas cuando se produce un evento de interrupción voluntaria.

Si se debe actualizar un clúster o una plantilla de implementación, el programador de Kubernetes se asegura de que se programen pods adicionales en otros nodos antes de que puedan continuar los eventos de interrupción voluntaria. El programador espera antes de que se reinicie un nodo hasta que la cantidad definida de pods se programe correctamente en otros nodos del clúster.

Veamos un ejemplo de un conjunto de réplicas con cinco pods que ejecutan NGINX. Se ha asignado la etiqueta `app: nginx-frontend` a los pods del conjunto de réplicas. Durante un evento de interrupción voluntaria, como una actualización de clúster, desea asegurarse de que al menos tres pods continúen ejecutándose. El siguiente manifiesto YAML para un objeto *PodDisruptionBudget* define estos requisitos:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

También puede definir un porcentaje, como *60 %*, que permite compensar automáticamente el conjunto de réplicas escalando verticalmente el número de pods.

Puede definir un número máximo de instancias no disponibles en un conjunto de réplicas. De nuevo, también se puede definir un porcentaje máximo de pods no disponibles. El siguiente manifiesto YAML de presupuesto de interrupciones de pods define que no estarán no disponibles más de dos pods en el conjunto de réplicas:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Una vez que se ha definido su presupuesto de interrupciones de pods, créelo en el clúster de AKS al igual que cualquier otro objeto de Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabaje con los desarrolladores y propietarios de su aplicación para conocer sus necesidades y aplicar los presupuestos de interrupciones de pods adecuados.

Para obtener más información sobre el uso de los presupuestos de interrupciones de pods, vea [Specify a disruption budget for your application][k8s-pdbs] (Especificación de un presupuesto de interrupciones para su aplicación).

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Comprobación de forma periódica de problemas de clúster con kube-advisor

**Mejor orientación práctica** -ejecutar con regularidad la versión más reciente de `kube-advisor` herramienta de código abierto para detectar problemas en el clúster. Si aplica cuotas de recursos en un clúster de AKS existente, en primer lugar, ejecute `kube-advisor` para buscar los pods que no tienen definidos los límites y las solicitudes de recursos.

El [kube-asesor] [ kube-advisor] herramienta es un proyecto de código abierto de AKS asociado que examina un clúster de Kubernetes e informa de los problemas que encuentre. Una comprobación útil consiste en identificar los pods que no tienen preparados los límites y las solicitudes de recursos.

En un clúster de AKS que hospeda varios equipos y aplicaciones de desarrollo, puede ser difícil realizar un seguimiento de los pods sin definir estos límites y solicitudes de recursos. Como práctica recomendada, ejecute regularmente `kube-advisor` en los clústeres de AKS, especialmente si no asigna cuotas de recursos a los espacios de nombres.

## <a name="next-steps"></a>Pasos siguientes

Este artículo se ha centrado en características básicas del programador de Kubernetes. Para obtener más información acerca de las operaciones de clúster en AKS, consulte los siguientes procedimientos recomendados:

* [Multi-tenancy and cluster isolation][aks-best-practices-cluster-isolation] (Aislamiento multiempresa y de clúster)
* [Advanced Kubernetes scheduler features][aks-best-practices-advanced-scheduler] (Características avanzadas del programador de Kubernetes)
* [Authentication and authorization][aks-best-practices-identity] (Autenticación y autorización)

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md