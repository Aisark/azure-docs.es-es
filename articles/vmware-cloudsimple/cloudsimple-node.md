---
title: 'Solución de Azure VMware de CloudSimple: introducción a los nodos'
description: Obtenga información sobre los conceptos y los nodos CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899032"
---
# <a name="cloudsimple-nodes-overview"></a>Introducción a los nodos CloudSimple

Los nodos son los bloques de compilación de una nube privada. Un nodo:

* Es un host de proceso completo dedicado en el que se instala el hipervisor ESXi de VMware.  
* Es una unidad de computación que puede aprovisionar o reservar para crear nubes privadas.
* Está disponible para aprovisionar o reservar en una región donde está disponible el servicio CloudSimple.

Puede crear una nube privada a partir de los nodos aprovisionados. Para crear una nube privada, necesita un mínimo de tres nodos de la misma SKU. Para expandir una nube privada, agregue más nodos.  Puede agregar nodos a un clúster existente o crear un clúster nuevo; para ello, aprovisione nodos en Azure Portal y asócielos al servicio CloudSimple.  Todos los nodos aprovisionados están visibles en el servicio CloudSimple.  

## <a name="provisioned-nodes"></a>Nodos aprovisionados

Los nodos aprovisionados proporcionan capacidad de pago por uso. Al aprovisionar nodos, puede escalar rápidamente su clúster de VMware a petición. Puede agregar nodos según sea necesario o eliminar un nodo aprovisionado para reducir verticalmente el clúster de VMware. Los nodos aprovisionados se facturan mensualmente y se cargan a la suscripción en la que están aprovisionados.

* Si paga la suscripción a Azure con tarjeta de crédito, se factura de inmediato en la tarjeta.
* Si se le factura, los cargos aparecerán en la próxima factura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>SKU de los nodos de la solución de VMware de CloudSimple

Los siguientes tipos de nodos están disponibles para aprovisionar o reservar.

| SKU           | Nodo CS28                 | Nodo CS36                 | Nodo CS36m                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Este de EE. UU., Oeste de EE. UU.            | Este de EE. UU., Oeste de EE. UU.            | Europa occidental                 |
| CPU           | 2 × 2,2 GHz, 28 núcleos (56 HT) | 2 × 2,3 GHz, 36 núcleos (72 HT) | 2 × 2,3 GHz, 36 núcleos (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco de caché    | 1,6 TB NVMe                 | 3,2 TB NVMe                 | 3,2 TB NVMe                 |
| Disco de capacidad | 5,625 TB sin formato                | 11,25 TB sin formato                | 15,36 TB sin formato                |
| Tipo de almacenamiento  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>límites

Se aplican los siguientes límites de nodo a las nubes privadas.

| Resource | Límite |
|----------|-------|
| Número mínimo de nodos para crear una nube privada | 3 |
| Número máximo de nodos en un clúster en una nube privada | 16 |
| Número máximo de nodos en una nube privada | 64 |
| Número mínimo de nodos en un clúster nuevo | 3 |

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [aprovisionar nodos](create-nodes.md).
* Obtenga más información sobre las [nubes privadas](cloudsimple-private-cloud.md).
