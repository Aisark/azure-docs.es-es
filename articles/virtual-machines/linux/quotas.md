---
title: Cuotas de vCPU de Azure
description: Obtenga información acerca de las cuotas de vCPU de Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 8ff245d851f83031d40cfcb8672d42b4dce1e108
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646683"
---
# <a name="virtual-machine-vcpu-quotas"></a>Cuotas de vCPU de máquinas virtuales

Las cuotas de vCPU para máquinas virtuales y conjuntos de escalado de máquinas virtuales se organizan en dos niveles para cada suscripción en cada región. El primer nivel es el número de vCPU regionales totales y el segundo son los núcleos de las diversas familias de tamaños de máquina virtual, como las vCPU de la serie D estándar. Siempre que se implemente una máquina virtual nueva, las vCPU de dicha máquina virtual no deben exceder la cuota de vCPU de la familia de tamaños de máquina virtual o el total de la cuota de vCPU regional. Si se supera cualquiera de esas dos cuotas, no se permitirá la implementación de la máquina virtual. También hay una cuota para el número total de máquinas virtuales en la región. Se pueden ver los detalles de cada una de estas cuotas en la sección **Uso y cuotas** de la página **Suscripción** de [Azure Portal](https://portal.azure.com), o bien puede consultar los valores mediante la CLI de Azure.


## <a name="check-usage"></a>Comprobación del uso

Puede comprobar el uso de la cuota mediante el comando [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

El resultado debe parecerse a este:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Instancias reservadas de máquina virtual
Las instancias reservadas de máquina virtual, cuyo ámbito es una sola suscripción sin tamaño de máquina virtual flexible, agregarán un aspecto nuevo a las cuotas de vCPU. Estos valores describen el número de instancias del tamaño indicado que deben poderse implementar en la suscripción. Actúan como un marcador de posición en el sistema de cuotas para asegurarse de que las reservas de Azure puedan implementarse en la suscripción. Por ejemplo, si una suscripción específica tiene diez reservas Standard_D1, el límite de usos para las reservas Standard_D1 será diez. Esto hará que Azure se asegure de que siempre hay al menos 10 vCPU disponibles en el total de la cuota de vCPU regional que se utilizará para instancias de Standard_D1 y que hay al menos 10 vCPU disponibles en la cuota de vCPU de la Familia D estándar que se utilizará para las instancias de Standard_D1.

Si es necesario aumentar la cuota para adquirir una instancia reservada de suscripción única, puede [solicitar un aumento de la cuota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) en su suscripción.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre facturación y cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
