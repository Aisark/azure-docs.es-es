---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2020
ms.locfileid: "66425754"
---
|  | **De punto a sitio** | **De sitio a sitio** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Servicios de Azure compatibles** |Cloud Services y Virtual Machines |Cloud Services y Virtual Machines |[Lista de servicios](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Anchos de banda típicos** |Se basa en la SKU de puerta de enlace |Agregación típica de < 1 Gbps |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protocolos admitidos** |Protocolo de túnel de sockets seguros (SSTP), OpenVPN e IPsec |IPsec |Conexión directa a través de redes VLAN y tecnologías VPN de NSP (MPLS, VPLS...) |
| **Enrutamiento** |RouteBased (dinámico) |Admitimos elementos basados en directivas (enrutamiento estático) y basados en enrutamiento (VPN de enrutamiento dinámico) |BGP |
| **Resistencia de la conexión** |activa-pasiva |activa-pasiva o activa-activa |activa-activa |
| **Caso de uso típico** |Creación de prototipos, escenarios de laboratorio, pruebas o desarrollo para Cloud Services y Virtual Machines |Escenarios de laboratorio, pruebas o desarrollo y cargas de trabajo de producción a pequeña escala para Cloud Services y Virtual Machines |Acceso a todos los servicios de Azure (lista validada), cargas de trabajo críticas y empresariales, copias de seguridad, macrodatos, Azure como sitio de recuperación ante desastres |
| **Acuerdo de Nivel de Servicio** |[Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) |[Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) |[Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) |
| **Precios** |[Precios](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Precios](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Precios](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentación técnica** |[Documentación de VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentación de VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **P+F** |[Preguntas más frecuentes sobre VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Preguntas más frecuentes sobre VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Preguntas frecuentes sobre ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
