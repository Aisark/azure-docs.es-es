---
title: Optimización de los costos para las implementaciones en varias regiones de Azure Cosmos DB
description: En este artículo se explica cómo administrar los costos de las implementaciones en varias regiones de Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 012eacb172acfdeb0b82343c484c664a3f75310e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929311"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimización del costo de varias regiones de Azure Cosmos DB

Puede agregar regiones a la cuenta de Azure Cosmos o eliminarlas en cualquier momento. La capacidad de proceso que se configura para varias bases de datos y contenedores de Azure Cosmos se reserva en cada región asociada a la cuenta. Si la capacidad de proceso aprovisionada por hora, que es la suma de RU/s configurada en todos los contenedores y las bases de datos para su cuenta de Azure Cosmos, es `T` y el número de regiones de Azure asociado a la cuenta de la base de datos es `N`, la capacidad de proceso aprovisionada para la cuenta de Cosmos para una hora determinada es:

1. `T x N RU/s` si su cuenta de Azure Cosmos está configurada con una región de escritura. 

1. `T x (N+1) RU/s` si su cuenta de Azure Cosmos está configurada con todas las regiones con capacidad de procesar escritura. 

La capacidad de proceso con una sola región de escritura cuesta 0,008 USD/hora por cada 100 RU/s y la capacidad de proceso aprovisionada con varias regiones de escritura cuesta 0,016 USD/hora por cada 100 RU/s. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/) de Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Costos para varias regiones de escritura

En un sistema de arquitectura multimaestro, las RU disponibles netas para las operaciones de escritura aumentan `N`, siendo `N` el número de regiones de escritura. A diferencia de la arquitectura con una sola región de escritura, ahora se podrá escribir en todas las regiones y estas admitirán la resolución de conflictos. La carga de trabajo de escritura ha aumentado. Desde el costo de planeación de punto de vista, para realizar `M` worth RU/s de escrituras en todo el mundo, tendrá que aprovisionar M `RUs` en un nivel de contenedor o base de datos. A continuación, puede agregar tantas regiones como desee y usarlas para la escritura global por valor de `M` RU. 

### <a name="example"></a>Ejemplo

Imagine que tiene un contenedor en la región Oeste de EE. UU. con una capacidad de proceso aprovisionada de 10 000 RU/s y almacena 1 TB de datos este mes. Suponga que agrega tres regiones (Este de EE. UU., Europa del Norte y Asia Oriental), cada una con el mismo almacenamiento y capacidad de proceso, y que quiere ser capaz de escribir en los contenedores de las cuatro regiones desde la aplicación distribuida mundialmente. Su factura total mensual (suponiendo que un mes tiene 31 días) será:

|**Elemento**|**Uso (mensual)**|**Tarifa**|**Costo mensual**|
|----|----|----|----|
|Factura de capacidad de proceso para un contenedor en la región Oeste de EE. UU. (operaciones de escritura en varias regiones) |10 000 RU/s * 24 * 31 |0,016 USD por cada 100 RU/s por hora |1190,40 USD |
|Factura de capacidad de proceso para 3 regiones más: Este de EE. UU., Europa del Norte y Asia Oriental (operaciones de escritura en varias regiones) |(3 + 1) * 10 000 RU/s * 24 * 31 |0,016 USD por cada 100 RU/s por hora |4761,60 USD |
|Factura de almacenamiento por el contenedor en Oeste de EE. UU. |100 GB |0,25 USD/GB |25 USD |
|Factura de almacenamiento para 3 regiones adicionales: Este de EE. UU., Europa del Norte y Asia Pacífico |3 * 1 TB |0,25 USD/GB |75 USD |
|**Total**|||**6052 USD** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Mejora del uso de la capacidad de proceso por región

Si el uso no es el adecuado, como una o más regiones con menos o más uso del necesario, puede tomar las siguientes medidas para mejorar la utilización de la capacidad de proceso:  

1. Asegúrese primero de optimizar la capacidad de proceso aprovisionada (RU) en la región de escritura y, a continuación, aproveche al máximo los RU en las regiones de lectura mediante la fuente de cambios de la región de lectura. 

2. Las distintas lecturas y escrituras de las regiones de escritura se pueden escalar en todas las regiones asociadas a la cuenta de Azure Cosmos. 

3. Supervise la actividad de todas las regiones; podrá agregar o quitar las regiones necesarias para escalar la capacidad de proceso de lectura y escritura.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede seguir obteniendo más información sobre la optimización de costos en Azure Cosmos DB con los siguientes artículos:

* Sobre la [optimización para desarrollo y pruebas](optimize-dev-test.md)
* Obtenga más información sobre [la factura de Azure Cosmos DB](understand-your-bill.md).
* Obtenga más información sobre la [optimización del costo de la capacidad del rendimiento](optimize-cost-throughput.md).
* Obtenga más información sobre la [optimización del costo del almacenamiento](optimize-cost-storage.md).
* Obtenga más información sobre la [optimización del costo de la lectura y la escritura](optimize-cost-reads-writes.md).
* Sobre la [Optimización del costo de las consultas](optimize-cost-queries.md)

