---
title: Información general Azure Virtual WAN | Microsoft Docs
description: Obtenga información acerca de la conectividad entre ramas escalable y automatizada de rama a rama de Virtual WAN, las regiones en que está disponible y sus asociados.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406358"
---
# <a name="what-is-azure-virtual-wan"></a>¿Qué es Azure Virtual WAN?

Azure Virtual WAN es un servicio de redes que ofrece conectividad entre ramas automatizada y optimizada a y mediante Azure. Las regiones de Azure sirven como centros que se pueden elegir para conectar las distintas ramas. Puede aprovechar la red troncal de Azure para conectar también ramas y disfrutar de la conectividad de rama a red virtual. Tenemos una lista de asociados que admiten la automatización de la conectividad con la VPN de Azure Virtual WAN. Para más información, consulte el artículo sobre [los asociados y las ubicaciones de Virtual WAN](virtual-wan-locations-partners.md).

Azure Virtual WAN reúne muchos servicios de conectividad en la nube de Azure como la VPN de sitio a sitio y ExpressRoute en una única interfaz operativa. La conectividad con las redes virtuales de Azure se establece mediante el uso de conexiones de red virtual.

ExpressRoute en Virtual WAN está actualmente en versión preliminar.

![Diagrama de Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

En este artículo se proporciona una vista rápida de la conectividad de red en Azure Virtual WAN. Virtual WAN ofrece las siguientes ventajas:

* **Soluciones de conectividad integrada en una topología en estrella tipo hub-and-stroke:** Configuración de sitio a sitio automatizada y conectividad entre sitios locales y un centro de Azure.
* **Instalación y configuración automatizadas de radio:** conecte sin problemas las cargas de trabajo y las redes virtuales al centro de Azure.
* **Solución intuitiva de problemas:** puede ver el flujo de un extremo a otro dentro de Azure y usar esta información para realizar las acciones necesarias.

## <a name="resources"></a>Recursos de Virtual WAN

Para configurar una red Virtual WAN de un extremo a otro, se deben crear los siguientes recursos:

* **virtualWAN:** El recurso virtualWAN representa una superposición virtual de la red de Azure y es una colección de varios recursos. Contiene vínculos a todos los concentradores virtuales que le gustaría tener dentro de Virtual WAN. Los recursos de Virtual WAN están aislados entre sí y no pueden contener un concentrador común. Los concentradores virtuales a través de Virtual WAN no se comunican entre sí. La propiedad "Permitir el tráfico entre ramas" permite tanto el tráfico entre sitios VPN como VPN en sitios habilitados para ExpressRoute (actualmente en versión preliminar).

* **Centro:** Un concentrador virtual es una red virtual administrada por Microsoft. El concentrador contiene varios puntos de conexión de servicio para habilitar la conectividad de la red local (vpnsite). El concentrador es el núcleo de la red en una región. Solo puede haber un único concentrador por cada región de Azure. Cuando se crea un concentrador mediante Azure Portal, se crea una red virtual del concentrador virtual y un elemento vpngateway de concentrador virtual.

  Una puerta de enlace de concentrador no es lo mismo que la puerta de enlace de red virtual que usó para ExpressRoute y VPN Gateway. Por ejemplo, cuando se usa Virtual WAN, no se crea una conexión de sitio a sitio desde un sitio local directamente a una red virtual. En su lugar, se crea una conexión de sitio a sitio en el centro. El tráfico siempre pasa a través de la puerta de enlace del concentrador. Esto significa que las redes virtuales no necesitan su propia puerta de enlace de red virtual. Virtual WAN permite que las redes virtuales obtengan provecho del escalado fácilmente mediante el concentrador virtual y su puerta de enlace.

* **Conexión de red virtual del centro:** el recurso de conexión de red virtual de centro se usa para conectar el centro sin problemas a una red virtual. En este momento, solo puede conectarse a las redes virtuales que están en la misma región central.

* **Tabla de rutas de centro:**  puede crear una ruta del centro virtual y aplicarla a la tabla de rutas del centro virtual. Puede aplicar varias rutas a la tabla de rutas del concentrador virtual.

**Recursos adicionales de Virtual WAN**

  * **Sitio:** este recurso se usa solo para las conexiones de sitio a sitio. El recurso del sitio es **vpnsite**. Representa el dispositivo VPN local y su configuración. Al trabajar con un asociado de Virtual WAN, tiene una solución integrada para exportar automáticamente esta información a Azure.

## <a name="connectivity"></a>Conectividad

Virtual WAN permite dos tipos de conectividad: Sitio a sitio y ExpressRoute (versión preliminar).

### <a name="s2s"></a>Conexión VPN de sitio a sitio

![Diagrama de Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Cuando se crea una conexión de sitio a sitio en Virtual WAN, se puede trabajar con un asociado disponible. Si no desea usar un asociado, puede configurar la conexión manualmente. Para más información, consulte [Creación de una conexión de sitio a sitio mediante Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Flujo de trabajo de asociados de Virtual WAN

Cuando se trabaja con un asociado de Virtual WAN, el flujo de trabajo es el siguiente:

1. El controlador del dispositivo de rama (VPN/SDWAN) se autentica para exportar información centrada en el sitio en Azure mediante el uso de una [entidad de servicio de Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. El controlador de dispositivo (VPN/SDWAN) de rama obtiene la configuración de conectividad de Azure y actualiza el dispositivo local. De esta forma se automatiza la descarga de la configuración, la edición y la actualización del dispositivo VPN local.
3. Una vez que el dispositivo tiene la configuración de Azure correcta, se establece una conexión de sitio a sitio (dos túneles activos) a Azure WAN. Azure admite tanto IKEv1 como IKEv2. BGP es opcional.

#### <a name="partners"></a>Asociados para conexiones de sitio a sitio de Virtual WAN

Para ver una lista de los asociados y las ubicaciones disponibles, consulte el artículo acerca [asociados y ubicaciones de Virtual WAN](virtual-wan-locations-partners.md).


### <a name="er"></a>Conexiones de ExpressRoute (versión preliminar)

ExpressRoute permite conectar una red local a Azure mediante una conexión privada. Para crear la conexión, consulte [Create an ExpressRoute connection using Virtual WAN](virtual-wan-expressroute-portal.md) (Creación de una conexión de ExpressRoute mediante Virtual WAN).

## <a name="locations"></a>Ubicaciones

Para obtener información de las ubicaciones, consulte el de ubicación, consulte el artículo acerca de los [asociados y ubicaciones de Virtual WAN](virtual-wan-locations-partners.md).

## <a name="faq"></a>P+F

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes

[Creación de una conexión de sitio a sitio mediante Virtual WAN](virtual-wan-site-to-site-portal.md)
