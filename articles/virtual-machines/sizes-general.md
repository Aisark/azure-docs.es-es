---
title: 'Tamaños de las máquinas virtuales de Azure: uso general | Microsoft Docs'
description: Enumera los tamaños diferentes de uso general disponibles para las máquinas virtuales en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 674197bd4d1562d8492a2605d8929572d450af90
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492561"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamaños de máquina virtual de uso general

Los tamaños de VM de uso general proporcionan una relación equilibrada entre CPU y memoria. Ideal para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio. En este artículo se proporciona información sobre las ofertas de informática de uso general.

- Las máquinas virtuales de la [serie Av2](av2-series.md) se pueden implementar en diversos procesadores y tipos de hardware. Las máquinas virtuales de la serie A tienen las configuraciones de memoria y rendimiento de CPU adecuadas para cargas de trabajo de nivel de entrada como desarrollo y pruebas. Según el hardware, el tamaño es una limitación para ofrecer un rendimiento coherente del procesador para la instancia en ejecución, independientemente del hardware en que se implementó. Con el fin de determinar el hardware físico en que se implementó este tamaño, cree una consulta para el hardware virtual desde dentro de la máquina virtual. Algunos casos de uso son, por ejemplo, los servidores de desarrollo y pruebas, los servidores web con poco tráfico, las bases de datos de tamaño pequeño a mediano, las pruebas de concepto y los repositorios de código.

- Las máquinas virtuales [ampliables de la serie B](sizes-b-series-burstable.md) son idóneas para cargas de trabajo que no necesitan un rendimiento completo de la CPU de forma continua, como los servidores web, pequeñas bases de datos y entornos de desarrollo y de prueba. Estas cargas de trabajo suelen necesitar unos requisitos de rendimiento ampliables. La serie B ofrece a estos clientes la posibilidad de comprar un tamaño de máquina virtual con un rendimiento base sensible al precio y que permita a la instancia de la máquina virtual acumular crédito cuando su rendimiento sea inferior al rendimiento base. Cuando la máquina virtual ha acumulado crédito se puede ampliar por encima de la base de referencia de esta con un uso de hasta un 100% de la CPU si la aplicación necesita el mayor rendimiento posible.

- Las [series Dav4 y Dasv4](dav4-dasv4-series.md) son tamaños nuevos que utilizan el procesador EPYC<sup>TM</sup> 7452 de 2,35 GHz de AMD en una configuración de varios subprocesos con una caché L3 de hasta 256 MB que dedica 8 GB de esa caché L3 a cada 8 núcleos. De este modo, aumentan las opciones que tiene el cliente para ejecutar sus cargas de trabajo de uso general. Las series Dav4 y Dasv4 tienen las mismas configuraciones de memoria y disco que las series D y Dsv3.

- La [serie DC](dc-series.md) es una familia de máquinas virtuales de Azure que puede ayudar a proteger la confidencialidad y la integridad de los datos y del código mientras se están procesando en la nube pública. Estas máquinas están respaldadas por la última generación del procesador Intel® Xeon® E-2176G a 3,7 GHz con la tecnología SGX. Con la tecnología Intel Turbo Boost, estas máquinas pueden llegar hasta 4,7 GHz. Las instancias de la serie DC permiten a los clientes crear aplicaciones seguras basadas en enclave para proteger sus datos y código mientras se usan.

- Las máquinas virtuales de las [series Dv2 y Dsv2](dv2-dsv2-series.md), una continuación de la serie D original, presentan una CPU más potente y una configuración óptima de la CPU a la memoria, lo que las hace adecuadas para la mayoría de las cargas de trabajo de producción. La serie Dv2 es un 35 % aproximadamente más rápida que la serie D. La serie Dv2 se ejecuta en procesadores Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) con Intel Turbo Boost Technology 2.0. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

- Las [series Dv3 y Dsv3](dv3-dsv3-series.md) se ejecutan en procesadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) en una configuración de Hyper-Threading, lo que proporciona una mejor propuesta de valor para la mayoría de las cargas de trabajo de uso general. Se ha ampliado la memoria (de ~3,5 GiB/vCPU a 4 GiB/vCPU), y los límites de disco y red se han ajustado por núcleo para equipararse con el cambio a hyperthreading. La serie Dv3 ya no tiene los tamaños de máquina virtual de memoria alta de la serie D/Dv2, que se han pasado a las series [Ev3 y Esv3](ev3-esv3-series.md) optimizadas para memoria.

Algunos casos de uso de la serie D son las aplicaciones empresariales, las bases de datos relacionales, el almacenamiento en caché en memoria y el análisis.

## <a name="other-sizes"></a>Otros tamaños

- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.