---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 82cbffb257d85197848b8bca14231e5363d6d45c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729856"
---
Algunas cargas de trabajo de base de datos como SQL Server u Oracle requieren mucha memoria, almacenamiento y ancho de banda de E/S, pero no un recuento de núcleos alto. Muchas cargas de trabajo de base de datos no consumen demasiados recursos de CPU. Azure ofrece determinados tamaños de VM que permiten restringir el recuento de vCPU de VM para reducir el costo de licencias de software y mantener la misma memoria, almacenamiento y ancho de banda de E/S.

Se puede restringir el número de vCPU a la mitad o un cuarto del tamaño de VM original. Estos nuevos tamaños de VM tienen un sufijo que especifica el número de vCPU activas para facilitar su identificación.

Por ejemplo, el tamaño de VM Standard_GS5 actual incluye 32 vCPU, 448 GB de RAM, 64 discos (hasta 256 TB) y 80 000 E/S por segundo o 2 GB/s de ancho de banda de E/S. Los nuevos tamaños de VM Standard_GS5-16 y Standard_GS5-8 incluyen 16 y 8 vCPU activas, respectivamente y mantienen el resto de especificaciones de Standard_GS5 para memoria, almacenamiento y ancho de banda de E/S.

Las tarifas de licencias que se cobran para SQL Server u Oracle están restringidas al nuevo recuento de vCPU y otros productos deben cobrarse según el nuevo recuento de vCPU. Esto provoca un aumento de entre el 50 % y el 75 % de la relación de las especificaciones de VM con vCPU activas (facturables). Estos nuevos tamaños de VM que solo están disponibles en Azure permiten a las cargas de trabajo un uso de CPU superior por una fracción del costo de licencias (por núcleo). En este momento, el costo de proceso, que incluye licencias de sistema operativo, es igual que el del tamaño original. Para más información, consulte [Azure VM sizes for more cost-effective database workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) (Tamaños de VM de Azure para cargas de trabajo de base de datos más rentables).


| NOMBRE                | vCPU | Especificaciones           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Igual que M8ms    |
| Standard_M8-4ms     | 4    | Igual que M8ms    |
| Standard_M16-4ms    | 4    | Igual que M16ms   |
| Standard_M16-8ms    | 8    | Igual que M16ms   |
| Standard_M32-8ms    | 8    | Igual que M32ms   |
| Standard_M32-16ms   | 16   | Igual que M32ms   |
| Standard_M64-32ms   | 32   | Igual que M64ms   |
| Standard_M64-16ms   | 16   | Igual que M64ms   |
| Standard_M128-64ms  | 64   | Igual que M128ms  |
| Standard_M128-32ms  | 32   | Igual que M128ms  |
| Standard_E4-2s_v3   | 2    | Igual que E4s_v3  |
| Standard_E8-4s_v3   | 4    | Igual que E8s_v3  |
| Standard_E8-2s_v3   | 2    | Igual que E8s_v3  |
| Standard_E16-8s_v3  | 8    | Igual que E16s_v3 |
| Standard_E16-4s_v3  | 4    | Igual que E16s_v3 |
| Standard_E32-16_v3  | 16   | Igual que E32s_v3 |
| Standard_E32-8s_v3  | 8    | Igual que E32s_v3 |
| Standard_E64-32s_v3 | 32   | Igual que E64s_v3 |
| Standard_E64-16s_v3 | 16   | Igual que E64s_v3 |
| Standard_GS4-8      | 8    | Igual que GS4     |
| Standard_GS4-4      | 4    | Igual que GS4     |
| Standard_GS5-16     | 16   | Igual que GS5     |
| Standard_GS5-8      | 8    | Igual que GS5     |
| Standard_DS11-1_v2  | 1    | Igual que DS11_v2 |
| Standard_DS12-2_v2  | 2    | Igual que DS12_v2 |
| Standard_DS12-1_v2  | 1    | Igual que DS12_v2 |
| Standard_DS13-4_v2  | 4    | Igual que DS13_v2 |
| Standard_DS13-2_v2  | 2    | Igual que DS13_v2 |
| Standard_DS14-8_v2  | 8    | Igual que DS14_v2 |
| Standard_DS14-4_v2  | 4    | Igual que DS14_v2 |
