---
title: archivo de inclusión
description: archivo de inclusión
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485437"
---
| Resource | Límite predeterminado |
| --- | :--- |
| Número máximo de clústeres por suscripción | 100 |
| Número máximo de nodos por clúster con los conjuntos de disponibilidad de máquina virtual y el SKU básico de Load Balancer  | 100 |
| Número máximo de nodos por clúster con Virtual Machine Scale Sets y el [SKU estándar de Load Balancer][standard-load-balancer] | 800 (100 nodos por [grupo de nodos][node-pool]) |
| Número máximo de pods por nodo: [Redes básicas][basic-networking] con Kubenet | 110 |
| Número máximo de pods por nodo: [Conexiones de red avanzadas][advanced-networking] con la interfaz de red de contenedor de Azure | Implementación de la CLI de Azure: 30<sup>1</sup><br />Plantilla de Azure Resource Manager: 30<sup>1</sup><br />Implementación del portal: 30 |

<sup>1</sup>Cuando implementa un clúster de Azure Kubernetes Service (AKS) con la CLI de Azure o una plantilla de Resource Manager, este valor es configurable en hasta 250 pods por nodo. No puede configurar un número máximo de pods por nodo después de haber implementado un clúster AKS, o si implementa un clúster mediante Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
