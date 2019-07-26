---
title: Uso de cuentas de Azure Cosmos DB
description: Este artículo describe cómo crear y usar cuentas de Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 617d19b0dd9da926eb49170c1566febc6f6280ba
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467809"
---
# <a name="work-with-azure-cosmos-account"></a>Uso de la cuenta de Azure Cosmos

Azure Cosmos DB es una plataforma como servicio (PaaS) totalmente administrada. Para empezar a usar Azure Cosmos DB, debe crear primero una cuenta de Azure Cosmos en su suscripción de Azure. La cuenta de Azure Cosmos contiene un nombre DNS único y puede administrar una cuenta mediante Azure portal, la CLI de Azure o mediante el uso de diferentes SDK específicos del idioma. Para más información, consulte [cómo administrar su cuenta de Azure Cosmos](how-to-manage-database-account.md).

La cuenta de Azure Cosmos es la unidad fundamental de distribución global y alta disponibilidad. Para distribuir globalmente los datos y el rendimiento entre varias regiones de Azure, puede agregar y quitar regiones de Azure en su cuenta de Azure Cosmos en cualquier momento. Puede configurar la cuenta de Azure Cosmos para que tenga una única región de escritura o varias. Para más información, consulte [como agregar y quitar regiones de Azure en la cuenta de Azure Cosmos](how-to-manage-database-account.md). Puede configurar el [nivel de coherencia predeterminado](consistency-levels.md) de la cuenta de Azure Cosmos. Azure Cosmos DB proporciona Acuerdos de Nivel de Servicio completos que abarcan rendimiento, latencia en el percentil 99, coherencia y alta disponibilidad. Para más información, consulte [Contrato de nivel de servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Para administrar de forma segura el acceso a todos los datos de la cuenta de Azure Cosmos, puede usar las [claves maestras](secure-access-to-data.md) asociadas con su cuenta. Para proteger aún más el acceso a sus datos, puede configurar un [punto de conexión de servicio de red virtual](vnet-service-endpoint.md) y un [firewall de IP](firewall-support.md) en la cuenta de Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos de una cuenta de Azure Cosmos

En Azure Cosmos DB, un contenedor es la unidad fundamental de escalabilidad. Prácticamente puede tener un rendimiento aprovisionado (RU/s) y un almacenamiento ilimitado en un contenedor. Azure Cosmos DB realiza particiones de forma transparente en el contenedor mediante la clave de partición lógica que especifique para escalar elásticamente el rendimiento y el almacenamiento aprovisionados. Para más información, consulte [working with Azure Cosmos containers and items](databases-containers-items.md) (Uso de contenedores y elementos de Azure Cosmos).

Actualmente, puede crear un máximo de 100 cuentas de Azure Cosmos en una suscripción de Azure. Una sola cuenta de Azure Cosmos puede administrar una cantidad casi ilimitada de datos y rendimiento aprovisionado. Para administrar los datos y el rendimiento aprovisionado, puede crear una o varias bases de datos de Azure Cosmos en su cuenta y, dentro de esa base de datos, puede crear uno o varios contenedores. La siguiente imagen muestra la jerarquía de elementos en una cuenta de Azure Cosmos:

![Jerarquía de una cuenta de Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Pasos siguientes

Sepa cómo administrar su cuenta de Azure Cosmos y aprenda otros conceptos:

* [Administración de la cuenta de Azure Cosmos](how-to-manage-database-account.md)
* [Distribución global](distribute-data-globally.md)
* [Niveles de coherencia](consistency-levels.md)
* [Uso de contenedores y elementos de Azure Cosmos](databases-containers-items.md)
* [Punto de conexión de servicio de red virtual para la cuenta de Azure Cosmos](vnet-service-endpoint.md)
* [Firewall para direcciones IP para la cuenta de Azure Cosmos](firewall-support.md)
* [Como agregar y quitar regiones de Azure en la cuenta de Azure Cosmos](how-to-manage-database-account.md).
* [Contratos de nivel de servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
