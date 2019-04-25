---
title: Tamaños de las máquinas virtuales Linux en Azure | Microsoft Docs
description: Enumera los tamaños diferentes disponibles para las máquinas virtuales Linux en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2018
ms.author: jonbeck
ms.openlocfilehash: 75333332c118e85bbe1ceb31b206360ce5ed3897
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540240"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tamaños de las máquinas virtuales Linux en Azure
En este artículo se describen los tamaños y las opciones disponibles para las máquinas virtuales de Azure que puede usar para ejecutar las aplicaciones y cargas de trabajo de Linux. También ofrece consideraciones de implementación que hay que tener en cuenta siempre que planee usar estos recursos. Este artículo también está disponible para [máquinas virtuales Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Type                     | Tamaños           |    DESCRIPCIÓN       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Uso general](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Uso equilibrado de la CPU en proporción de memoria. Ideal para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio. |
| [Proceso optimizado](sizes-compute.md)        | Fsv2, Fs, F             | Uso elevado de la CPU en proporción de memoria. Bueno para servidores web de tráfico medio, aplicaciones de red, procesos por lotes y servidores de aplicaciones.        |
| [Memoria optimizada](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Memoria alta en proporción de CPU. Excelente para servidores de bases de datos relacionales, memorias caché de capacidad media o grande y análisis en memoria.                 |
| [Almacenamiento optimizado](sizes-storage.md)        | Lsv2, Ls                | Alto rendimiento de disco y de E/S ideales para macrodatos, bases de datos SQL y NoSQL, almacenamiento de datos y bases de datos transaccionales grandes.  |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND, NDv2 (versión preliminar)            | Máquinas virtuales especializadas específicas para la representación de gráficos pesados y la edición de vídeo, así como para el entrenamiento e inferencia de modelos (ND) con aprendizaje profundo. Están disponibles con uno o varios GPU.       |
| [Proceso de alto rendimiento](sizes-hpc.md) | H       | Nuestras máquinas virtuales de CPU más rápidas y eficaces con interfaces de red de alto rendimiento opcionales (RDMA). |


<br>

- Para obtener información sobre los precios de los diferentes tamaños, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Para ver la disponibilidad de los tamaños de máquina virtual en las regiones de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).
- Para ver los límites generales de las máquinas virtuales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-subscription-service-limits.md).
- Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.


## <a name="rest-api"></a>API DE REST

Para obtener información sobre el uso de la API de REST para consultar los tamaños de máquina virtual, consulte lo siguiente:

- [Lista de tamaños de máquina virtual disponibles para cambio de tamaño](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista de tamaños de máquina virtual disponibles para una suscripción](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Lista de tamaños de máquina virtual disponibles en un conjunto de disponibilidad](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.

## <a name="benchmark-scores"></a>Puntuaciones de pruebas comparativas

Más información sobre el rendimiento de los procesos para las máquinas virtuales Linux con las [puntuaciones de pruebas comparativas de CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los diferentes tamaños de máquina virtual que están disponibles:
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- Consulte la página [Generación anterior](sizes-previous-gen.md) para las series A estándar, Dv1 (D1-4 y D11-14 v1) y las series A8-A11



