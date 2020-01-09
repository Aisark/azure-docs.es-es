---
title: Conectividad entre redes de Azure
description: Esta página describe un escenario de aplicación para la conectividad entre redes y la solución basada en las características de red de Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644263"
---
# <a name="cross-network-connectivity"></a>Conectividad entre redes

Fabrikam Inc. tiene una presencia física importante e implementación de Azure en el Este de EE. UU. Fabrikam tiene conectividad de back-end entre sus servidores locales y las implementaciones de Azure a través de ExpressRoute. De igual modo, Contoso Ltd. tiene presencia e implementación de Azure en el Oeste de EE. UU. Contoso tiene conectividad de back-end entre sus servidores locales y las implementaciones de Azure a través de ExpressRoute.  

Fabrikam Inc. adquiere Contoso Ltd. Después de la fusión, Fabrikam desea interconectar las redes. En la siguiente ilustración se muestra este escenario:

 [![1]][1]

Las flechas discontinuas en medio de la ilustración anterior indican las interconexiones de red deseadas. En concreto, hay tres tipos de conexiones cruzadas deseadas: 1) Conexiones cruzadas entre las redes virtuales de Fabrikam y Contoso, 2) Conexiones cruzadas entre redes virtuales y locales entre regiones (es decir, una conexión entre la red local de Fabrikam y la red virtual de Contoso, y otra conexión entre la red local de Contoso y la red virtual de Fabrikam), y 3) Conexión cruzada entre las redes locales de Fabrikam y Contoso. 

En la siguiente tabla se muestra la tabla de rutas del emparejamiento privado con ExpressRoute de Contoso Ltd. antes de la fusión.

[![2]][2]

La siguiente tabla muestra las rutas eficaces de una máquina virtual en la suscripción de Contoso, antes de la fusión. Según la tabla, la máquina virtual en la red virtual conoce el espacio de direcciones de la red virtual y la red local de Contoso, aparte de los valores predeterminados. 

[![4]][4]

En la siguiente tabla se muestra la tabla de rutas del emparejamiento privado con ExpressRoute de Fabrikam Inc. antes de la fusión.

[![3]][3]

La siguiente tabla muestra las rutas eficaces de una máquina virtual en la suscripción de Fabrikam, antes de la fusión. Según la tabla, la máquina virtual en la red virtual conoce el espacio de direcciones de la red virtual y la red local de Fabrikam, aparte de los valores predeterminados.

[![5]][5]

En este artículo, explicaremos el proceso paso a paso y discutiremos cómo lograr las conexiones cruzadas deseadas utilizando las siguientes características de red de Azure:

* [Emparejamiento de redes virtuales][Virtual network peering] 
* [Conexión de red virtual con ExpressRoute][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Conexión cruzada entre redes virtuales

El emparejamiento de redes virtuales proporciona el mejor y más óptimo rendimiento de red cuando se conectan dos redes virtuales. El emparejamiento de redes virtuales admite el emparejamiento de dos redes virtuales en la misma región de Azure (comúnmente denominado emparejamiento de red virtual) y en dos regiones diferentes (comúnmente denominado emparejamiento de red virtual global). 

Vamos a configurar el emparejamiento de red virtual global entre las redes virtuales en las suscripciones a Contoso y Fabrikam. Para crear el emparejamiento de red virtual entre las dos redes virtuales, consulte el artículo [Creación de un emparejamiento de redes virtuales][Configure VNet peering].

La siguiente imagen muestra la arquitectura de red después de configurar el emparejamiento de red virtual global.

[![6]][6]

La siguiente tabla muestra las rutas conocidas a la máquina virtual de la suscripción a Contoso. Preste atención a la última entrada de la tabla. Esta entrada es el resultado de realizar una conexión cruzada entre las redes virtuales.

[![7]][7]

La siguiente tabla muestra las rutas conocidas a la máquina virtual de la suscripción a Fabrikam. Preste atención a la última entrada de la tabla. Esta entrada es el resultado de realizar una conexión cruzada entre las redes virtuales.

[![8]][8]

El emparejamiento de red virtual vincula directamente dos redes virtuales (observe que no hay ningún próximo salto para la entrada *VNetGlobalPeering* en las dos tablas anteriores).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Conexión cruzada entre las redes virtuales y las redes locales

Es posible conectar un circuito ExpressRoute a varias redes virtuales. Consulte los [límites de servicio y suscripciones][Subscription limits] para conocer el número máximo de redes virtuales que se pueden conectar a un circuito ExpressRoute. 

Vamos a conectar el circuito ExpressRoute de Fabrikam con la red virtual de la suscripción a Contoso y, de igual modo, conectaremos el circuito ExpressRoute de Contoso a la red virtual de la suscripción a Fabrikam para habilitar la conectividad cruzada entre redes virtuales y redes locales. Para conectar una red virtual a un circuito ExpressRoute en una suscripción diferente, es necesario crear y utilizar una autorización.  Consulte el artículo: [Conexión de una red virtual a un circuito ExpressRoute][Connect-ER-VNet].

La siguiente imagen muestra la arquitectura de red después de configurar la conectividad cruzada de ExpressRoute a las redes virtuales.

[![9]][9]

En la tabla siguiente se muestra la tabla de rutas del emparejamiento privado de ExpressRoute de Contoso Ltd. después de crear una conexión cruzada entre las redes virtuales y las redes locales a través de ExpressRoute. Observe que la tabla de rutas tiene rutas que pertenecen a ambas redes virtuales.

[![10]][10]

En la tabla siguiente se muestra la tabla de rutas del emparejamiento privado de ExpressRoute de Fabrikam Inc. después de crear una conexión cruzada entre las redes virtuales y las redes locales a través de ExpressRoute. Observe que la tabla de rutas tiene rutas que pertenecen a ambas redes virtuales.

[![11]][11]

La siguiente tabla muestra las rutas conocidas a la máquina virtual de la suscripción a Contoso. Preste atención a las entradas de *Virtual network gateway* (Puerta de enlace de red virtual) de la tabla. La máquina virtual muestra rutas para ambas redes locales.

[![12]][12]

La siguiente tabla muestra las rutas conocidas a la máquina virtual de la suscripción a Fabrikam. Preste atención a las entradas de *Virtual network gateway* (Puerta de enlace de red virtual) de la tabla. La máquina virtual muestra rutas para ambas redes locales.

[![13]][13]

>[!NOTE]
>En cualquiera de las suscripciones a Fabrikam y Contoso, también pueden existir redes virtuales de radio a la red virtual hub correspondiente (no se muestra un diseño de hub y radio en los diagramas de arquitectura de este artículo). Las conexiones cruzadas entre las puertas de enlace de red virtual de hub y ExpressRoute también permiten la comunicación entre hub y radios de las regiones Este y Oeste.
>

## <a name="cross-connecting-on-premises-networks"></a>Conexión cruzada entre redes locales

Global Reach de ExpressRoute proporciona conectividad entre redes locales que están conectadas a distintos circuitos ExpressRoute. Vamos a configurar Global Reach entre los circuitos ExpressRoute de Contoso y Fabrikam. Dado que los circuitos ExpressRoute se encuentran en distintas suscripciones, es necesario crear y utilizar una autorización. Consulte el artículo [Configuración de ExpressRoute Global Reach][Configure Global Reach] para obtener instrucciones paso a paso.

La siguiente imagen muestra la arquitectura de red después de configurar Global Reach.

[![14]][14]

En la siguiente tabla se muestra la tabla de rutas del emparejamiento privado con ExpressRoute de Contoso Ltd. después de configurar Global Reach. Observe que la tabla de rutas tiene rutas que pertenecen a ambas redes locales. 

[![15]][15]

En la siguiente tabla se muestra la tabla de rutas del emparejamiento privado con ExpressRoute de Fabrikam Inc. después de configurar Global Reach. Observe que la tabla de rutas tiene rutas que pertenecen a ambas redes locales.

[![16]][16]

## <a name="next-steps"></a>Pasos siguientes

Consulte las [preguntas más frecuentes de Virtual Network][VNet-FAQ] para resolver las dudas adicionales sobre redes virtuales y emparejamiento de redes virtuales. Consulte [P+F de ExpressRoute][ER-FAQ] para resolver las dudas adicionales sobre la conectividad de redes virtuales y ExpressRoute.

Global Reach se está dando a conocer país a país y región a región. Para ver si Global Reach está disponible en los países o regiones que quiere, consulte [Global Reach de ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Escenario de aplicación"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Tabla de rutas de ExpressRoute de Contoso antes de la fusión"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Tabla de rutas de ExpressRoute de Fabrikam antes de la fusión"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Rutas de máquina virtual de Contoso antes de la fusión"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Rutas de máquina virtual de Fabrikam antes de la fusión"
[6]: ./media/cross-network-connectivity/vnet-peering.png "La arquitectura después del emparejamiento de red virtual"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Rutas de máquina virtual de Contoso después del emparejamiento de red virtual"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Rutas de máquina virtual de Fabrikam después del emparejamiento de red virtual"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "La arquitectura después de la conexión cruzada de ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Tabla de rutas de ExpressRoute de Contoso después de realizar la conexión cruzada entre ExpressRoute y las redes virtuales"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Tabla de rutas de ExpressRoute de Fabrikam después de realizar la conexión cruzada entre ExpressRoute y las redes virtuales"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Rutas de máquina virtual de Contoso después de realizar la conexión cruzada entre ExpressRoute y las redes virtuales"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Rutas de máquina virtual de Fabrikam después de realizar la conexión cruzada entre ExpressRoute y las redes virtuales"
[14]: ./media/cross-network-connectivity/globalreach.png "La arquitectura después de configurar Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Tabla de rutas de ExpressRoute de Contoso después de Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Tabla de rutas de ExpressRoute de Fabrikam después de Global Reach"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq