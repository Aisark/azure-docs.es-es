---
title: '(EN DESUSO) Supervisión de un clúster de Azure Kubernetes: Sysdig'
description: Supervisión de un clúster de Kubernetes en Azure Container Service utilizando Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309272"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(EN DESUSO) Supervisión de un clúster de Kubernetes de Azure Container Service con Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

También se da por supuesto que tiene herramientas de kubectl y de la CLI de Azure instaladas.

Puede probar si tiene la herramienta `az` instalada mediante la ejecución de:

```console
$ az --version
```

Si no tiene la herramienta `az` instalada, se ofrecen instrucciones [aquí](https://github.com/azure/azure-cli#installation).

Puede probar si tiene la herramienta `kubectl` instalada mediante la ejecución de:

```console
$ kubectl version
```

Si no tiene la herramienta `kubectl` instalada, puede ejecutar:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig una empresa de servicio de supervisión externa que puede supervisar los contenedores en un clúster de Kubernetes que se ejecuta en Azure. El uso de Sysdig requiere una cuenta de Sysdig activa.
Puede registrarse para obtener una cuenta en su [sitio](https://app.sysdigcloud.com).

Una vez que haya iniciado sesión en el sitio web de la nube de Sysdig, haga clic en su nombre de usuario; debería aparecer en la página la clave de acceso (Access Key). 

![Clave de API de Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalación de los agentes Sysdig en Kubernetes
Para supervisar los contenedores, Sysdig ejecuta un proceso en cada equipo con un `DaemonSet` de Kubernetes.
DaemonSets son objetos de la API de Kubernetes que ejecutan una instancia única de un contenedor por máquina.
Son perfectos para instalar herramientas como el agente de supervisión de Sysdig.

Para instalar el daemonset Sysdig, primero debe descargar [la plantilla](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) desde sysdig. Guarde el archivo como `sysdig-daemonset.yaml`.

En Linux y OS X, puede ejecutar:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

En PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

A continuación, modifique ese archivo para insertar la clave de acceso que obtuvo de la cuenta Sysdig.

Por último, cree el DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Visualización de la supervisión
Una vez instalados y en ejecución, los agentes deben bombear datos a Sysdig.  Vuelva al [panel de sysdig](https://app.sysdigcloud.com) y debería ver la información acerca de los contenedores.

También puede instalara paneles específicos de Kubernetes a través del [nuevo Asistente de panel](https://app.sysdigcloud.com/#/dashboards/new).
