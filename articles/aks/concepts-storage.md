---
title: 'Conceptos: almacenamiento en Azure Kubernetes Service (AKS)'
description: Obtenga información sobre el almacenamiento en Azure Kubernetes Service (AKS), incluidos los volúmenes, los volúmenes persistentes, las clases de almacenamiento y las notificaciones
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: cce38eb12d803c0640d9ee774dbc6c98ab5db219
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60466824"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opciones de almacenamiento de aplicaciones en Azure Kubernetes Service (AKS)

Las aplicaciones que se ejecutan en Azure Kubernetes Service (AKS) pueden necesitar almacenar y recuperar datos. Para algunas cargas de trabajo de la aplicación, este almacenamiento de datos puede usar el almacenamiento rápido local en el nodo que ya no es necesario cuando se eliminan los pods. Otras cargas de trabajo de la aplicación pueden requerir almacenamiento que conserve en volúmenes de datos más regulares dentro de la plataforma Azure. Es posible varios pods necesiten compartir los mismos volúmenes de datos o volver a conectar los volúmenes de datos si se vuelve a programar el pod en otro nodo. Por último, es posible que deba insertar datos confidenciales o información de configuración de la aplicación en los pods.

![Opciones de almacenamiento de aplicaciones en un clúster de Azure Kubernetes Service (AKS)](media/concepts-storage/aks-storage-options.png)

En este artículo se presentan los conceptos básicos que proporcionan almacenamiento para sus aplicaciones en AKS:

- [Volúmenes](#volumes)
- [Volúmenes persistentes](#persistent-volumes)
- [Clases de almacenamiento](#storage-classes)
- [Notificaciones de volúmenes persistentes](#persistent-volume-claims)

## <a name="volumes"></a>Volúmenes

A menudo, las aplicaciones necesitan poder almacenar y recuperar datos. Dado que Kubernetes suele tratar los pods individuales como recursos efímeros y descartables, existen diferentes enfoques disponibles para usar aplicaciones y conservar datos según sea necesario. Un *volumen* representa una manera de almacenar, recuperar y conservar datos entre pods y durante el ciclo de vida de la aplicación.

Los volúmenes tradicionales para almacenar y recuperar datos se crean como recursos de Kubernetes respaldados por Azure Storage. Puede crear manualmente estos volúmenes de datos para que se asignen directamente a los pods, o hacer que Kubernetes los cree automáticamente. Estos volúmenes de datos pueden utilizar Azure Disks o Azure Files:

- *Azure Disks* puede usarse para crear un recurso *DataDisk* de Kubernetes. Azure Disks puede usar almacenamiento Premium de Azure, respaldado por SSD de alto rendimiento, o bien almacenamiento estándar de Azure, respaldado por unidades de disco duro normales. Para la mayoría de las cargas de trabajo de producción y desarrollo, utilice el almacenamiento Premium. Los discos de Azure Disks se montan como *ReadWriteOnce*, por lo que solo están disponibles para un único nodo. Para los volúmenes de almacenamiento a los que pueden acceder varios nodos simultáneamente, use Azure Files.
- *Azure Files* se puede usar para montar un recurso compartido de SMB 3.0 respaldado por una cuenta de Azure Storage en los pods. Azure Files permite compartir datos entre varios nodos y pods. Actualmente, Azure Files solo puede usar el almacenamiento estándar de Azure respaldado por unidades de discos duro normales.

En Kubernetes, los volúmenes pueden representar más que un disco tradicional donde se puede almacenar y recuperar información. Los volúmenes de Kubernetes pueden utilizarse también como una forma de insertar datos en un pod para su uso en contenedores. Algunos tipos de volumen adicional comunes de Kubernetes son:

- *emptyDir*: este volumen se suele utilizar como espacio temporal para un pod. Todos los contenedores dentro de un pod pueden acceder a los datos del volumen. Los datos escritos en este tipo de volumen se conservan únicamente para la duración del pod (cuando se elimina el pod, se elimina el volumen). Este volumen suele usar el almacenamiento en disco del nodo local subyacente, aunque también puede existir solo en la memoria del nodo.
- *secreto*: este volumen se usa para insertar datos confidenciales en pods, como contraseñas. En primer lugar, cree un secreto mediante la API de Kubernetes. Al definir el pod o la implementación, puede solicitar un secreto específico. Los secretos solo se proporcionan a los nodos que tienen un pod programado que lo requiere. El secreto se almacena en el sistema *tmpfs*, no se escribe en el disco. Cuando se elimina el último pod en un nodo que requiere un secreto, el secreto se elimina del sistema tmpfs del nodo. Los secretos se almacenan en un espacio de nombres determinado y solo son accesibles para los pods del mismo espacio de nombres.
- *configMap*: este tipo de volumen se usa para insertar las propiedades de pares clave-valor en pods, como la información de configuración de la aplicación. En lugar de definir la información de configuración de la aplicación dentro de una imagen de contenedor, puede definirla como un recurso de Kubernetes que se pueda actualizar fácilmente y aplicarse a las nuevas instancias de pods a medida que se implementan. Al igual que con un secreto, primero se crea el volumen ConfigMap mediante la API de Kubernetes. Posteriormente, el volumen ConfigMap se puede solicitar al definir un pod o una implementación. Los volúmenes ConfigMap se almacenan en un espacio de nombres determinado y solo son accesibles para los pods del mismo espacio de nombres.

## <a name="persistent-volumes"></a>Volúmenes persistentes

Los volúmenes que se definen y se crean como parte del ciclo de vida del pod solo existen hasta que se elimina este. Los pods suelen esperar que su almacenamiento se conserve si un pod se vuelve a programar en un host diferente durante un evento de mantenimiento, especialmente en StatefulSets. Un *volumen persistente* es un recurso de almacenamiento creado y administrado por la API de Kubernetes, que puede existir más allá de la duración de un pod individual.

Se usan Azure Disks o Azure Files para proporcionar el volumen PersistentVolume. Como se indicó en la sección anterior sobre los volúmenes, la elección de Azure Disks o Azure Files suele venir determinada por la necesidad de acceso simultáneo a los datos o al nivel de rendimiento.

![Volúmenes persistentes en un clúster de Azure Kubernetes Service (AKS)](media/concepts-storage/persistent-volumes.png)

Un volumen PersistentVolume puede crearlo *de forma estática* un administrador de clústeres o *de forma dinámica* el servidor de API de Kubernetes. Si un pod está programado y solicita almacenamiento que no está disponible actualmente, Kubernetes puede crear el almacenamiento subyacente de Azure Disks o Azure Files y conectarlo al pod. El aprovisionamiento dinámico usa *StorageClass* para identificar el tipo de almacenamiento que Azure Storage debe crear.

## <a name="storage-classes"></a>Clases de almacenamiento

Para definir niveles de almacenamiento diferentes, como Premium y Estándar, puede crear una clase *StorageClass*. La clase StorageClass también define la directiva *reclaimPolicy*. Esta directiva reclaimPolicy controla el comportamiento del recurso de almacenamiento subyacente de Azure cuando se elimina el pod y el volumen persistente puede dejar de ser necesario. El recurso de almacenamiento subyacente se puede eliminar o conservar para su uso con un pod futuro.

En AKS, se crean dos clases StorageClass iniciales:

- *default*: usa el almacenamiento estándar de Azure para crear un disco administrado. La directiva de reclamación indica que el disco de Azure Disks subyacente se elimina cuando se elimina el pod que lo utiliza.
- *managed-premium*: utiliza el almacenamiento Premium de Azure para crear un disco administrado. De nuevo, la directiva de reclamación indica que el disco de Azure Disks subyacente se elimina cuando se elimina el pod que lo utiliza.

Si no se especifica ninguna clase StorageClass para un volumen persistente, se usa el valor de StorageClass predeterminado. Tenga cuidado cuando solicite volúmenes persistentes y compruebe que usan el almacenamiento adecuado que necesita. Puede crear una clase StorageClass para satisfacer necesidades adicionales mediante `kubectl`. En el siguiente ejemplo se utilizan discos administrados Premium y se especifica que el disco de Azure Disks subyacente debe *conservarse* cuando se elimine el pod:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Notificaciones de volúmenes persistentes

Una notificación PersistentVolumeClaim solicita almacenamiento en disco o archivo de una clase StorageClass, un modo de acceso y un tamaño determinados. El servidor de API de Kubernetes puede aprovisionar dinámicamente el recurso de almacenamiento subyacente de Azure si no hay ningún recurso existente para satisfacer la notificación de acuerdo con la clase StorageClass definida. La definición del pod incluye el montaje del volumen una vez que se este se ha conectado al pod.

![Notificaciones de volúmenes persistentes en un clúster de Azure Kubernetes Service (AKS)](media/concepts-storage/persistent-volume-claims.png)

Un volumen PersistentVolume se *enlaza* a una notificación PersistentVolumeClaim cuando se asigna un recurso de almacenamiento disponible al pod que lo solicita. Existe una asignación de 1:1 de volúmenes a notificaciones.

El manifiesto YAML de ejemplo siguiente muestra una notificación de volumen persistente que usa la clase StorageClass *managed-premium* y solicita un disco de *5 Gi* de tamaño:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Cuando se crea una definición de pod, se especifica la notificación de volumen persistente para solicitar el almacenamiento deseado. A continuación, se especifica el campo *volumeMount* para que sus aplicaciones lean y escriban datos. El siguiente manifiesto YAML de ejemplo muestra cómo se puede usar la notificación de volumen persistente anterior para montar un volumen en */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Pasos siguientes

Para consultar los procedimientos recomendados, vea [Procedimientos recomendados para el almacenamiento y las copias de seguridad en AKS][operator-best-practices-storage].

Para ver cómo crear volúmenes dinámicos y estáticos que usen Azure Disks o Azure Files, consulte los artículos de procedimientos siguientes:

- [Create a static volume using Azure Disks][aks-static-disks] (Crear un volumen estático con Azure Disks)
- [Create a static volume using Azure Files][aks-static-files] (Crear un volumen estático con Azure Files)
- [Create a dynamic volume using Azure Disks][aks-dynamic-disks] (Crear un volumen dinámico con Azure Disks)
- [Create a dynamic volume using Azure Files][aks-dynamic-files] (Crear un volumen dinámico con Azure Files)

Para obtener más información sobre los conceptos básicos de Kubernetes y AKS, consulte los artículos siguientes:

- [Kubernetes / AKS clusters and workloads][aks-concepts-clusters-workloads] (Clústeres y cargas de trabajo de Kubernetes/AKS)
- [Kubernetes / AKS identity][aks-concepts-identity] (Identidad de Kubernetes/AKS)
- [Kubernetes / AKS security][aks-concepts-security] (Seguridad de Kubernetes/AKS)
- [Kubernetes / AKS virtual networks][aks-concepts-network] (Redes virtuales de Kubernetes/AKS)
- [Kubernetes / AKS scale][aks-concepts-scale] (Escala de Kubernetes/AKS)

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
