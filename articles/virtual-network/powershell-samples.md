---
title: Ejemplos de Azure PowerShell para red virtual | Microsoft Docs
description: Ejemplos de Azure PowerShell para red virtual
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/17/2019
ms.author: kumud
ms.openlocfilehash: e10f7ad4ae8995d9efffec97a37dc64b28fa37b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688022"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Ejemplos de Azure PowerShell para red virtual

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell:

| | |
|----|----|
| [Creación de una red virtual para aplicaciones de niveles múltiples](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Creación de una red virtual con subredes de front-end y back-end. El tráfico a la subred de front-end está limitado a HTTP, mientras que el tráfico a la subred de back-end está limitado a SQL, al puerto 1433. |
| [Emparejamiento de dos redes virtuales](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Creación y conexión de dos redes virtuales en la misma región. |
| [Enrutamiento del tráfico por una aplicación virtual de red](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Creación de una red virtual con subredes de front-end y back-end y una máquina virtual que pueda enrutar el tráfico entre dos subredes. |
| [Filtrado del tráfico de red entrante y saliente de máquina virtual](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Creación de una red virtual con subredes de front-end y back-end. El tráfico de red entrante a la subred de front-end está limitado a HTTP y HTTPS. No se permite el tráfico saliente a Internet desde la subred de back-end. |
|[Configuración de una red virtual de doble pila de IPv4 + IPv6](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Implementa la red virtual de doble pila (IPv4 + IPv6) con dos máquinas virtuales y una instancia de Azure Basic Load Balancer con las direcciones IP públicas IPv4 e IPv6. |
