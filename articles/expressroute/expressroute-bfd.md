---
title: 'Configuración de BFD a través de: Azure | Microsoft Docs'
description: En este artículo se proporcionan instrucciones sobre cómo configurar BFD (detección de reenvío bidireccional) sobre el emparejamiento privado de un circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 14f65851e50ed25024524f6d988ba2b2f2b3aeba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367682"
---
# <a name="configure-bfd-over-expressroute"></a>Configuración de BFD a través de ExpressRoute

ExpressRoute admite la detección de reenvío bidireccional (BFD) a través del emparejamiento privado. Al habilitar BFD a través de ExpressRoute, puede acelerar la detección de errores de vínculo entre los dispositivos de Microsoft Enterprise Edge (MSEE) y los enrutadores en los que finaliza el circuito de ExpressRoute (PE). Puede finalizar ExpressRoute a través de dispositivos de enrutamiento del lado del cliente o dispositivos de enrutamiento perimetrales asociados (si se ha completado con el servicio administrado de conexión de nivel 3). Este documento explica en detalle la necesidad de BFD y cómo habilitar BFD a través de ExpressRoute.

## <a name="need-for-bfd"></a>Necesidad de BFD

El siguiente diagrama muestra la ventaja de habilitar BFD a través del circuito de ExpressRoute: [![1]][1]

Puede habilitar el circuito de ExpressRoute mediante conexiones de nivel 2 o de nivel 3 administradas. En cualquier caso, si hay uno o varios dispositivos de nivel 2 en la ruta de acceso de conexión de ExpressRoute, la responsabilidad de detectar los errores de vínculo en la ruta de acceso es del BGP superpuesto.

En los dispositivos MSEE, BGP keepalive y el tiempo de espera generalmente se configuran como 60 y 180 segundos respectivamente. Por lo tanto, después de un error de vínculo pueden pasar hasta tres minutos hasta que se detecta cualquier error del vínculo y se cambia el tráfico a una conexión alternativa.

Puede controlar los temporizadores BGP mediante una configuración con valores inferiores de BGP keepalive y tiempo de espera en el dispositivo de emparejamiento perimetral de cliente. Si los temporizadores BGP no coinciden en los dos dispositivos de emparejamiento, la sesión BGP entre los pares utilizará el valor inferior del temporizador. BGP keepalive se puede establecer en un valor tan bajo como tres segundos y el tiempo de espera en unos diez segundos. De todas formas, es preferible no establecer los temporizadores BGP de forma extrema, ya que el protocolo intensifica el proceso.

En este escenario, BFD puede ayudar. BFD proporciona detección de errores de vínculo de baja sobrecarga en un intervalo de tiempo de subsegundos. 


## <a name="enabling-bfd"></a>Habilitación del BFD

BFD está configurado de forma predeterminada en todas las recién creadas interfaces de emparejamiento privadas de ExpressRoute en los MSEE. Por lo tanto, para habilitar BFD, no tiene más que configurar BFD en los PE. La configuración de BFD es un proceso de dos pasos: tiene que configurar BFD en la interfaz y luego vincularlo a la sesión BGP.

A continuación se muestra un ejemplo de configuración de PE (con Cisco IOS XE). 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Para habilitar BFD en un emparejamiento privado ya existente; tiene que restablecer el emparejamiento. Consulte [Restablecimiento de emparejamientos de ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociación de temporizador BFD

Entre pares BFD, el más lento de los dos pares determina la velocidad de transmisión. Los intervalos de transmisión y recepción de los MSEE BFD se establecen en 300 milisegundos. En determinados escenarios, el intervalo puede establecerse en un valor superior a 750 milisegundos. Si se configuran valores más altos, se puede forzar a estos intervalos a ser más largos; pero no se puede hacer que sean más cortos.

>[!NOTE]
>Si ha configurado circuitos de emparejamiento privado de ExpressRoute con redundancia geográfica, o usa conectividad VPN de IPSec de sitio a sitio como respaldo para el emparejamiento privado de ExpressRoute; la habilitación de BFD a través del emparejamiento privado ayudará a la conmutación por error más rápida después de un error de conectividad de ExpressRoute. 
>

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes artículos:

- [Creación y modificación de un circuito ExpressRoute][CreateCircuit]
- [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD acelera el tiempo de deducción de errores de vínculo"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






