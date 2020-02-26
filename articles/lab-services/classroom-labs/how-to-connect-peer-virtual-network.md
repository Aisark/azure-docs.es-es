---
title: Conectarse a una red del mismo nivel en Azure Lab Services | Microsoft Docs
description: Aprenda a conectar la red de laboratorio a otra red como una red del mismo nivel. Por ejemplo, conecte la red local de la escuela o la universidad a la red virtual de laboratorio en Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443211"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conexión de la red del laboratorio con una red virtual del mismo nivel en Azure Lab Services 
En este artículo encontrará información sobre cómo emparejar su red de laboratorio con otra red. 

## <a name="overview"></a>Información general
El emparejamiento de redes virtuales permite conectar sin problemas redes virtuales de Azure. Una vez emparejadas, a efectos de conectividad las redes virtuales aparecen como una sola. El tráfico entre las máquinas virtuales de las redes virtuales emparejadas se enruta a través de la infraestructura de la red troncal de Microsoft, de forma muy parecida a como se enruta el tráfico entre máquinas virtuales de la misma red virtual a través únicamente de direcciones IP privadas. Para más información, consulte [Emparejamiento de redes virtuales](../../virtual-network/virtual-network-peering-overview.md).

Puede que necesite conectar la red de laboratorio con una red virtual del mismo nivel en algunos escenarios, como los siguientes:

- Las máquinas virtuales del laboratorio tienen software que se conecta a servidores de licencias locales para adquirir una licencia.
- Las máquinas virtuales del laboratorio necesitan acceder a conjuntos de datos (o a cualquier otro archivo) en recursos compartidos de red de la universidad. 

Algunas redes locales están conectadas a Azure Virtual Network ya sea a través de [ExpressRoute](../../expressroute/expressroute-introduction.md) o de una [puerta de enlace de red virtual](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Estos servicios se deben configurar fuera de Azure Lab Services. Para obtener más información sobre cómo conectar una red local a Azure mediante ExpressRoute, consulte [Información general sobre ExpressRoute](../../expressroute/expressroute-introduction.md). Para lograr la conectividad local mediante una puerta de enlace de red virtual, tanto la puerta de enlace como la red virtual y la cuenta de laboratorio deben estar en la misma región.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configuración al crear la cuenta de laboratorio
Durante la creación de la cuenta de laboratorio, puede elegir una red virtual existente de la lista desplegable **Asociar red virtual** en la pestaña **Avanzado**. La red virtual seleccionada se conecta (empareja) a laboratorios creados con la cuenta de laboratorio. Todas las máquinas virtuales en los laboratorios que se creen después de hacer este cambio tendrían acceso a los recursos en la red virtual emparejada. 

![Selección de una red virtual para emparejamiento](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Para obtener instrucciones detalladas para crear una cuenta de laboratorio, vea [Configuración de una cuenta de laboratorio con Azure Lab Services](tutorial-setup-lab-account.md).


## <a name="configure-after-the-lab-is-created"></a>Configuración después de crear el laboratorio
La misma propiedad se puede habilitar desde la pestaña **Configuración de laboratorios** de la página **Cuenta de laboratorio** si no configuró una red del mismo nivel cuando creó la cuenta de laboratorio. El cambio realizado en esta configuración tendrá efecto únicamente en los laboratorios creados después de dicho cambio. Como puede ver en la imagen, la opción **Asociar red virtual** se puede habilitar o deshabilitar en los laboratorios de la cuenta de laboratorio. 

![Habilitar o deshabilitar la asociación de red virtual después de crear el laboratorio](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Al seleccionar una red virtual en el campo **Asociar red virtual**, se deshabilita la opción **Permitir al creador del laboratorio seleccionar la ubicación correspondiente**. Esto se debe a que los laboratorios de la cuenta de laboratorio deben estar en la misma región que dicha cuenta para que se puedan conectar con los recursos de la red virtual del mismo nivel. 

> [!IMPORTANT]
> Este cambio de configuración tiene efecto únicamente en los laboratorios creados después de realizar el cambio, no en los laboratorios que ya existían. 


## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Permiso para que el creador del laboratorio seleccione su ubicación](allow-lab-creator-pick-lab-location.md)
- [Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services](how-to-attach-detach-shared-image-gallery.md)
- [Cómo agregar un usuario como propietario de un laboratorio de clase en Azure Lab Services](how-to-add-user-lab-owner.md)
- [Configuración del firewall para Azure Lab Services](how-to-configure-firewall-settings.md)
- [Configuración de cuentas de laboratorio en Azure Lab Services](how-to-configure-lab-accounts.md)