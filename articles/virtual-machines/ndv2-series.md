---
title: 'Serie NDv2: máquinas virtuales de Azure'
description: Especificaciones de las máquinas virtuales de la serie NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1aa2a6402a58ba69a7b5999803bb10d48169a035
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267434"
---
# <a name="updated-ndv2-series-preview"></a>Serie NDv2 actualizada (versión preliminar)

La máquina virtual de la serie NDv2 es una nueva incorporación a la familia de GPU diseñada para las necesidades de las cargas de trabajo más exigentes de informática de alto rendimiento, inteligencia artificial aprendizaje automático y simulación de aceleración por GPU.

NDv2 cuenta con 8 GPU NVIDIA Tesla V100 conectadas mediante NVLINK, cada una con 32 GB de memoria de GPU. Cada máquina virtual NDv2 también tiene 40 núcleos Intel Xeon Platinum 8168 (Skylake) sin hyperthreading y 672 GiB de memoria del sistema.

Las instancias de NDv2 proporcionan un rendimiento excelente para las cargas de trabajo de IA y HPC mediante kernels de cálculo optimizados para GPU de CUDA y las numerosas herramientas de inteligencia artificial, Machine Learning y análisis que admiten la aceleración GPU de serie, como TensorFlow, Pytorch, Caffe, RAPIDS y otras plataformas.

Fundamentalmente, NDv2 se ha creado para cargas de trabajo de escalado vertical (con 8 GPU por máquina virtual) y escalado horizontal (con varias máquinas virtuales que funcionan de forma conjunta) que usan muchos cálculos. La serie NDv2 ahora admite redes de back-end InfiniBand EDR de 100 Gigabits, similares a las que están disponibles en la serie HB de máquinas virtuales HPC, para permitir la agrupación en clústeres de alto rendimiento en escenarios paralelos, incluido el entrenamiento distribuido para IA y ML. Esta red de back-end es compatible con todos los protocolos de InfiniBand principales, incluidos los empleados por las bibliotecas NCCL2 de NVIDIA, lo que permite la agrupación en clústeres de las GPU sin problemas.


> [!NOTE]
> Al [habilitar InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) en la máquina virtual ND40rs_v2, use el controlador OFED de Mellanox 4.7-1.0.0.1.
>
> Debido a la mayor cantidad de memoria de GPU, la nueva máquina virtual ND40rs_v2 necesita el uso de [máquinas virtuales de generación 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) y de imágenes de Marketplace. 
>
> [Regístrese para solicitar acceso temprano a la versión preliminar de la máquina virtual NDv2.](https://aka.ms/AzureNDrv2Preview)
>
> Tenga en cuenta lo siguiente: ND40s_v2 con 16 GB de memoria por GPU ya no está disponible para la versión preliminar y se ha reemplazado por la versión actualizada de ND40rs_v2.

<br>

Premium Storage:  Compatible

Almacenamiento en caché de Premium Storage:  Compatible

Migración en vivo: No compatible

Actualizaciones con conservación de memoria: No compatible

InfiniBand: Compatible

| Size | vCPU | Memoria: GiB | Almacenamiento temporal (SSD): GiB | GPU | Memoria de GPU: GiB | Discos de datos máx. | Rendimiento máximo del disco sin almacenamiento en la caché: IOPS / MBps | Ancho de banda de red máx. | Nº máx. NIC |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24 000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos y controladores compatibles

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure, deben instalarse controladores de GPU de NVIDIA.

La [extensión de controlador de GPU de NVIDIA](./extensions/hpccompute-gpu-windows.md) instala los controladores CUDA de NVIDIA o GRID adecuados en una máquina virtual de la serie N. Instale o administre la extensión mediante Azure Portal o con herramientas como las plantillas de Azure PowerShell o Azure Resource Manager. Consulte la [documentación de la extensión de controlador de GPU de NVIDIA](./extensions/hpccompute-gpu-windows.md) para los sistemas operativos compatibles y los pasos de implementación. Para una información general sobre las extensiones de máquina virtual, consulte [Características y extensiones de las máquinas virtuales de Azure](./extensions/overview.md).

Si decide instalar manualmente los controladores de GPU de NVIDIA, consulte el artículo sobre la [instalación de controladores GPU de la serie N para Windows](./windows/n-series-driver-setup.md) o el artículo sobre la [instalación de controladores GPU de la serie N para Linux](./linux/n-series-driver-setup.md) para obtener información sobre los sistemas operativos compatibles, los controladores, la instalación y los pasos de verificación.

## <a name="other-sizes"></a>Otros tamaños

- [Uso general](sizes-general.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU optimizada](sizes-gpu.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.
