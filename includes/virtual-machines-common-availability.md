---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ffe0d3a85006bbaad53e471a10b10fb1bbea89b1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664451"
---
En este artículo proporciona una visión general de las características de disponibilidad de las máquinas virtuales (VM) de Azure.

## <a name="high-availability"></a>Alta disponibilidad

Por lo general, las cargas de trabajo se distribuyen entre máquinas virtuales distintas para obtener un alto rendimiento y crear redundancia en caso de que una máquina virtual se vea afectada debido a una actualización u otro evento. 

Hay algunas opciones que Azure proporciona para lograr alta disponibilidad. En primer lugar, hablemos sobre las construcciones básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidad

Las [zonas de disponibilidad](../articles/availability-zones/az-overview.md) expanden el nivel de control que tiene para mantener la disponibilidad de las aplicaciones y los datos en las máquinas virtuales. Una zona de disponibilidad es una zona separada físicamente dentro de una región de Azure. Hay tres zonas de disponibilidad por cada región de Azure admitida. 

Cada zona de disponibilidad tiene una fuente de alimentación, una red y un sistema de refrigeración distintos. Si diseña las soluciones para que utilicen máquinas virtuales replicadas en zonas, podrá proteger sus datos y aplicaciones frente a la pérdida de un centro de datos. Aunque una zona esté en peligro, las aplicaciones y los datos replicados estarán disponibles instantáneamente en otra zona. 

![Zonas de disponibilidad](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Obtenga más información acerca de cómo implementar una máquina virtual [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) o [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) en una zona de disponibilidad.


### <a name="fault-domains"></a>Dominios de error

Un dominio de error es un grupo lógico de hardware subyacente que comparte la fuente de alimentación y el conmutador de red, similar a un bastidor dentro de un centro de datos local. 

### <a name="update-domains"></a>Dominios de actualización

Un dominio de actualización es un grupo lógico de hardware subyacente que puede someterse a mantenimiento o reiniciarse al mismo tiempo. 

Este enfoque garantiza que al menos una instancia de la aplicación sigue ejecutándose cuando se realiza el mantenimiento periódico de la plataforma Azure. Es posible que el orden en que se reinician los dominios de actualización no siga una secuencia durante un mantenimiento, pero se reinician de uno en uno.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de escalado de máquinas virtuales 

Los conjuntos de escalado de máquinas virtuales de Azure permiten crear y administrar un grupo de máquinas virtuales con equilibrio de carga. El número de instancias de máquina virtual puede aumentar o disminuir automáticamente según la demanda, o de acuerdo a una programación definida. Los conjuntos de escalado proporcionan una alta disponibilidad a las aplicaciones y le permiten administrar, configurar y actualizar de forma centralizada muchas máquinas virtuales. Se recomienda la creación de dos o más máquinas virtuales en un conjunto de escalado para proporcionar una aplicación de alta disponibilidad y para cumplir el [99,95 % del Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). No hay ningún costo asociado con el conjunto de escalado propiamente dicho, solo se paga por cada instancia de máquina virtual que cree. Cuando una sola máquina virtual usa [discos SSD Premium de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), se aplica el Acuerdo de Nivel de Servicio de Azure para los eventos de mantenimiento no planeados. Las máquinas virtuales de un conjunto de escalado se pueden implementar en varias regiones y dominios de error para maximizar la disponibilidad y la resistencia a las interrupciones debidas a interrupciones del centro de datos y a eventos de mantenimiento planeados o no planeados. Las máquinas virtuales de un conjunto de escalado también se pueden implementar en una sola zona de disponibilidad o en regiones. Las opciones de implementación de zona de disponibilidad pueden diferir según el modo de orquestación.

### <a name="preview-orchestration-mode-preview"></a>Vista previa: versión preliminar del modo de orquestación
Los conjuntos de escalado de máquinas virtuales permiten especificar el modo de orquestación.  Con el modo de orquestación del conjunto de escalado de máquinas virtuales (versión preliminar), ahora puede elegir si el conjunto de escalado debe orquestar las máquinas virtuales que se crean explícitamente fuera de un modelo de configuración de conjunto de escalado o las instancias de máquina virtual creadas de manera implícita en función del modelo de configuración. Elija el modo de orquestación de máquina virtual que le permite agrupar máquinas virtuales definidas de manera explícita en una región o en una zona de disponibilidad. Las máquinas virtuales implementadas en una zona de disponibilidad proporcionan aislamiento de zonas a las máquinas virtuales a las que están enlazadas en el límite de la zona de disponibilidad y no están sujetas a errores que puedan producirse en otra zona de disponibilidad de la región. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Modelo de configuración de máquina virtual| Ninguno. VirtualMachineProfile no se define en el modelo del conjunto de escalado. | Necesario. VirtualMachineProfile se rellena en el modelo del conjunto de escalado. |
| Incorporación de una máquina virtual nueva a un conjunto de escalado| Las máquinas virtuales se agregan de manera explícita al conjunto de escalado cuando se crea la máquina virtual. | Las máquinas virtuales se crean de manera implícita y se agregan al conjunto de escalado según el modelo de configuración de la máquina virtual, el recuento de instancias y las reglas de escalado automático. |
| Zonas de disponibilidad| Admite la implementación regional o las máquinas virtuales en una zona de disponibilidad| Admite la implementación regional o varias zonas de disponibilidad. Puede definir la estrategia de equilibrio de zona. |
| Dominios de error| Puede definir el recuento de dominios de error. 2 o 3 según la compatibilidad regional y 5 para la zona de disponibilidad. El dominio de error de VM asignado se conservará con el ciclo de vida de la máquina virtual, incluida la desasignación y el reinicio. | Puede definir 1, 2 o 3 dominios de error para implementaciones sin zonas y 5 para implementaciones de zonas de disponibilidad. El dominio de error de VM asignado no se conserva con el ciclo de vida de la máquina virtual, a las máquinas virtuales se les asigna un dominio de error en el momento de la asignación. |
| Dominios de actualización| N/D Los dominios de actualización se asignan automáticamente a los dominios de error| N/D Los dominios de actualización se asignan automáticamente a los dominios de error |

**Dominios de error y dominios de actualización**

Los conjuntos de escalado de máquinas virtuales simplifican el diseño para lograr alta disponibilidad al alinear los dominios de error y los dominios de actualización. Solo tendrá que definir el número de dominios de error del conjunto de escalado. El número de dominios de error disponibles para los conjuntos de escalado puede variar en función de la región. Consulte [Número de dominios de error por región](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Conjuntos de disponibilidad
Un conjunto de disponibilidad es una agrupación lógica de máquinas virtuales dentro de un centro de datos que permite a Azure conocer cómo se crea su aplicación para proporcionar redundancia y disponibilidad. Se recomienda la creación de dos, o más, máquinas virtuales en un conjunto de disponibilidad no solo para proporcionar una aplicación de alta disponibilidad sino también para cumplir el [99,95 % del Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). No hay ningún costo asociado con el conjunto de disponibilidad propiamente dicho, solo se paga por cada instancia de máquina virtual que cree. Cuando una sola máquina virtual usa [discos SSD Premium de Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), se aplica el Acuerdo de Nivel de Servicio de Azure para los eventos de mantenimiento no planeados.

En un conjunto de disponibilidad, las máquinas virtuales se distribuyen automáticamente entre estos dominios de error. Este enfoque limita el impacto de potenciales errores de hardware físico, interrupciones de red o cortes de alimentación eléctrica.

Para las máquinas virtuales que usen [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), las máquinas virtuales se alinean con los dominios de error de disco administrado cuando se usa un conjunto de disponibilidad administrada. Esta alineación garantiza que todos los discos administrados conectados a una máquina virtual se encuentran en el mismo dominio de error de disco administrado. 

Solo se pueden crear máquinas virtuales con discos administrados en un conjunto de disponibilidad administrada. El número de dominios de error de disco administrado varía según la región: dos o tres dominios de error de disco administrado por región. Para más información sobre estos dominios de error de Managed Disks consulte [máquinas virtuales Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) o [máquinas virtuales Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidad administrado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Las máquinas virtuales dentro de un conjunto de disponibilidad también se distribuyen automáticamente entre los dominios de actualización. 

![Conjuntos de disponibilidad](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Pasos siguientes
Ya puede empezar a usar estas características de disponibilidad y redundancia para crear un entorno de Azure. Para información sobre los procedimientos recomendados, consulte [Lista de comprobación de disponibilidad](../articles/best-practices-availability-checklist.md).

