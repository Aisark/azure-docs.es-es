---
title: archivo de inclusión
description: archivo de inclusión
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d4797232a51739238a88576a1fdd95bc62d6afaa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975345"
---
| Resource | Límite predeterminado |
| --- | --- |
| Rendimiento de los datos |30 Gbps<sup>1</sup> |
|Reglas|10 000, todos los tipos de reglas combinados.|
|Tamaño mínimo de AzureFirewallSubnet |/26|
|Intervalo de puertos en reglas de red y aplicación|0-64.000. El trabajo está en curso para relajar esta limitación.|
|Direcciones IP públicas|100 máximo (actualmente, los puertos SNAT solo se agregan en las primeras cinco direcciones IP públicas).|
|Tabla de rutas|De forma predeterminada, AzureFirewallSubnet tiene una ruta 0.0.0.0/0 con el valor de NextHopType establecido en **Internet**.<br><br>Azure Firewall debe tener conectividad directa a Internet. Si AzureFirewallSubnet aprende una ruta predeterminada a la red local mediante BGP, debe reemplazarla por una UDR 0.0.0.0/0 con el valor **NextHopType** establecido como **Internet** para mantener la conectividad directa a Internet. De forma predeterminada, Azure Firewall no admite la tunelización forzada a una red local.<br><br>Sin embargo, si la configuración requiere la tunelización forzada a una red local, Microsoft proporcionará soporte según el caso. Póngase en contacto con soporte técnico para que podamos revisar su caso. Si acepta, permitiremos su suscripción y nos aseguraremos de que se mantenga la conectividad del firewall a Internet requerida.|

<sup>1</sup>Si necesita aumentar estos límites, póngase en contacto con el soporte técnico de Azure.
