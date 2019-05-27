---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159199"
---
Las SKU de puertas de enlace de VPN heredadas (antiguas) son:

* Básica
* Estándar
* HighPerformance

La VPN Gateway no utiliza la SKU de puerta de enlace de UltraPerformance. Para obtener información acerca de la SKU de UltraPerformance, consulte la documentación de [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Cuando trabaje con las SKU heredadas, tenga en cuenta lo siguiente:

* Si desea utilizar un tipo de VPN PolicyBased, debe utilizar la SKU de nivel Básico. Las VPN PolicyBased (que anteriormente se denominaba enrutamiento estático) no se admiten en otra SKU.
* BGP no es compatible con la SKU de nivel Básico.
* Las configuraciones de la coexistencia de ExpressRoute-VPN Gateway no se admiten en la SKU de nivel Básico.
* Las conexiones de VPN Gateway S2S activo/activo solo pueden configurarse en la SKU HighPerformance.