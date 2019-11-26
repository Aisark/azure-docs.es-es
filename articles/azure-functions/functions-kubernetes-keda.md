---
title: Azure Functions en Kubernetes con KEDA
description: Aprenda a ejecutar Azure Functions en Kubernetes en la nube o en el entorno local mediante KEDA, el escalado automático controlado por eventos basado en Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: 8e07032f84ead4bb003176af84cb4c731819ffa4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900061"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions en Kubernetes con KEDA

El tiempo de ejecución de Azure Functions proporciona flexibilidad de hospedaje dónde y cómo desee.  [KEDA](https://github.com/kedacore/kore) (escalado automático controlado por eventos basado en Kubernetes) se empareja sin problemas con el tiempo de ejecución y las herramientas de Azure Functions para proporcionar la escala controlada por eventos en Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Funcionamiento de las funciones basadas en Kubernetes

El servicio de Azure Functions está formado por dos componentes clave: un tiempo de ejecución y un controlador de escala.  El tiempo de ejecución de Functions se ejecuta y ejecuta el código.  El tiempo de ejecución incluye lógica sobre cómo desencadenar, registrar y administrar las ejecuciones de funciones.  El otro componente es un controlador de escala.  El controlador de escala supervisa la tasa de eventos que se dirigen a la función y escala de forma proactiva el número de instancias que ejecutan la aplicación.  Para más información, consulte [Escalado y hospedaje de Azure Functions](functions-scale.md).

Las funciones basadas en Kubernetes proporcionan el tiempo de ejecución de Functions en un [Contenedor de Docker](functions-create-function-linux-custom-image.md) con escalado controlado por eventos a través de KEDA.  KEDA puede reducir a 0 instancias (cuando no se produce ningún evento) y ampliar hasta *n* instancias. Lo hace mediante la exposición de las métricas personalizadas de Kubernetes Autoscaler (Horizontal Pod Autoscaler).  El uso de contenedores de Functions con KEDA hace posible replicar las capacidades de la función sin servidor en cualquier clúster de Kubernetes.  Estas funciones también se pueden implementar mediante la característica de [nodos virtuales de Azure Kubernetes Service (AKS)](../aks/virtual-nodes-cli.md) para la infraestructura sin servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Administración de KEDA y Functions en Kubernetes

Para ejecutar Functions en el clúster de Kubernetes, debe instalar el componente KEDA. Puede instalar este componente mediante [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalación con Azure Functions Core Tools

De forma predeterminada, Core Tools instala los componentes de KEDA y Osiris, que admiten el escalado controlado por eventos y de HTTP, respectivamente.  La instalación usa `kubectl` que se ejecuta en el contexto actual.

Instale KEDA en el clúster mediante la ejecución del siguiente comando de instalación:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Implementación de una aplicación de función en Kubernetes

Puede implementar cualquier aplicación de función en un clúster de Kubernetes mediante la ejecución de KEDA.  Puesto que las funciones se ejecutan en un contenedor de Docker, su proyecto necesita un elemento `Dockerfile`.  Si aún no tiene ninguno, puede agregar un archivo Dockerfile ejecutando el comando siguiente en la raíz del proyecto de Functions:

```cli
func init --docker-only
```

Para crear una imagen e implementar las funciones en Kubernetes, ejecute el siguiente comando:

> [!NOTE]
> Las herramientas principales usarán la CLI de Docker para compilar y publicar la imagen. Asegúrese de que Docker ya está instalado y conectado a su cuenta con `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Reemplace `<name-of-function-deployment>` por el nombre de la aplicación de función.

Esto crea un recurso `Deployment` de Kubernetes, un recurso `ScaledObject` y `Secrets`, que incluye las variables de entorno importadas desde su archivo `local.settings.json`.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implementación de una aplicación de funciones desde un registro privado

El flujo anterior también funciona con registros privados.  Si extrae la imagen de contenedor de un registro privado, incluya la marca `--pull-secret` que hace referencia al secreto de Kubernetes que contiene las credenciales del registro privado al ejecutar `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Eliminación de una aplicación de función de Kubernetes

Después de la implementación puede quitar una función mediante la eliminación de los `Deployment`, `ScaledObject` asociados y `Secrets` creados.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalación de KEDA de Kubernetes

Puede ejecutar el siguiente comando de las herramientas principales para quitar KEDA de un clúster de Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Desencadenadores admitidos en KEDA

KEDA está actualmente en versión beta con compatibilidad con los siguientes desencadenadores de Azure Functions:

* [Colas de Azure Storage](functions-bindings-storage-queue.md)
* [Colas de Azure Service Bus](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Creación de una función con una imagen personalizada](functions-create-function-linux-custom-image.md)
* [Codificación y comprobación de Azure Functions en un entorno local](functions-develop-local.md)
* [Cómo funciona el plan de consumo de Azure Functions](functions-scale.md)