---
title: 'Azure Front Door Service: arquitectura de enrutamiento | Microsoft Docs'
description: Este artículo le ayudará a comprender el aspecto de la visión general de la arquitectura de Front Door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736296"
---
# <a name="routing-architecture-overview"></a>Introducción a la arquitectura de enrutamiento

Cuando recibe las solicitudes de cliente, Azure Front Door Service las responde (si está habilitado el almacenamiento en caché) o bien las reenvía al back-end de aplicación adecuado (como un proxy inverso).

</br>Existen oportunidades de optimizar el tráfico cuando se enruta a Azure Front Door y cuando se enruta a los back-end.

## <a name = "anycast"></a>Selección del entorno de Front Door para el enrutamiento del tráfico (Anycast)

El enrutamiento a los entornos de Azure Front Door aprovecha las ventajas de [Anycast](https://en.wikipedia.org/wiki/Anycast) para DNS (Sistema de nombres de dominio) y para el tráfico HTTP (Protocolo de transferencia de hipertexto), por lo que el tráfico del usuario será dirigido al entorno más cercano en términos de topología de red (menor número saltos). Normalmente, esta arquitectura ofrece mejores tiempos de ida y vuelta para los usuarios finales (maximiza las ventajas de la División TCP). Front Door organiza sus entornos en "anillos" principales y de reserva.  El anillo exterior tiene entornos que están más próximos a los usuarios, ofreciendo latencias más bajas.  El anillo interior tiene entornos que pueden controlar la conmutación por error para el entorno del anillo exterior en caso de que se produzca un problema. El anillo exterior es el destino preferido para todo el tráfico, pero el anillo interior es necesario para controlar el desbordamiento de tráfico desde el anillo exterior. En términos de direcciones VIP (direcciones del protocolo de Internet virtual), se asigna una dirección VIP principal a cada host de front-end o dominio servido por Front Door, que los entornos anuncian en el anillo interior y exterior, así como una dirección VIP de reserva, que los entornos solo anuncian en el anillo interior. 

</br>Esta estrategia garantiza que las solicitudes de los usuarios finales siempre alcanzan el entorno de Front Door más cercano y que incluso si el entorno de Front Door preferido es incorrecto, el tráfico pasa automáticamente al siguiente entorno más cercano.

## <a name = "splittcp"></a>Conexión al entorno de Front Door (División TCP)

La [División TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) es una técnica para reducir las latencias y los problemas de TCP mediante la división de una conexión que podría incurrir en unos tiempos de ida y vuelta altos en partes más pequeñas.  Al colocar los entornos de Front Door más próximos a los usuarios finales y cerrar las conexiones TCP dentro del entorno de Front Door, una conexión TCP con un gran tiempo de ida y vuelta (RTT) al back-end de aplicación se divide en dos conexiones TCP. Una conexión corta entre el usuario final y el entorno de Front Door significa que la conexión se establece sobre tres viajes de ida y vuelta cortos en lugar de tres viajes de ida y vuelta largos, mejorando así la latencia.  La conexión larga entre el entorno de Front Door y el back-end se puede establecer previamente y reutilizar en varias llamadas del usuario final, mejorando de nuevo el tiempo de la conexión TCP.  El efecto se multiplica al establecer una conexión SSL/TLS (Seguridad de la capa de transporte), ya que hay más viajes de ida y vuelta para proteger la conexión.

## <a name="processing-request-to-match-a-routing-rule"></a>Procesamiento de la solicitud para que coincida con una regla de enrutamiento
Cuando una solicitud llega a un entorno de Front Door, después de establecer una conexión y realizar un protocolo de enlace SSL, el primer paso es la coincidencia con una regla de enrutamiento. Esta coincidencia consiste básicamente en determinar qué regla de enrutamiento particular coincide con la solicitud de entre todas las configuraciones de Front Door. Obtenga información sobre cómo realiza Front Door la [coincidencia de rutas](front-door-route-matching.md).

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificación de los back-end disponibles en el grupo de back-end para la regla de enrutamiento
Una vez que Front Door tiene una coincidencia para una regla de enrutamiento en función de la solicitud entrante y, si no hay almacenamiento en caché, el siguiente paso es extraer el estado del sondeo de mantenimiento del grupo de back-end asociado con la ruta coincidente. Obtenga información sobre cómo supervisa Front Door el mantenimiento del back-end mediante [Sondeos de estado](front-door-health-probes.md).

## <a name="forwarding-the-request-to-your-application-backend"></a>Reenvío de la solicitud al back-end de aplicación
Por último, suponiendo que no se ha configurado el almacenamiento en caché, se reenvía la solicitud del usuario al "mejor" back-end según la configuración del [método de enrutamiento de Front Door](front-door-routing-methods.md).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
