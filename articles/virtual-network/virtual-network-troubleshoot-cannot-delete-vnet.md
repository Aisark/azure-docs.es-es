---
title: No se puede eliminar una red virtual en Azure | Microsoft Docs
description: Obtenga información acerca de cómo solucionar el problema por el que no se puede eliminar una red virtual en Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c2152b872c82c224c786e56db0318c9df994ac25
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801593"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Solución de problemas: No se pudo eliminar una red virtual en Azure
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=a13a0908-28e0-f9f7-c6c7-301fcd187560" target='_blank'>Iniciar</a></span><span class="has-padding-small">Solucione el problema rápidamente con el agente virtual para ejecutar <b>diagnósticos automáticos.</b></span><span class="has-padding-small"><a href="https://privacy.microsoft.com/privacystatement" target='_blank'><div align="right"><sub>Declaración de privacidad</sub></div></a></span></p>
Podría recibir errores al intentar eliminar una red virtual en Microsoft Azure. Este artículo proporciona pasos de solución de problemas para ayudarle a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Guía de solución de problemas 

1. [Compruebe si se está ejecutando una puerta de enlace de red virtual en la red virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Compruebe si se está ejecutando una puerta de enlace de aplicación en la red virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Compruebe si Azure Active Directory Domain Service está habilitado en la red virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Compruebe si la red virtual está conectada a otro recurso](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Compruebe si aún se está ejecutando una máquina virtual en la red virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Compruebe si la red virtual se quedó bloqueada en la migración](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Compruebe si se está ejecutando una puerta de enlace de red virtual en la red virtual

Para quitar la red virtual, primero debe quitar la puerta de enlace de red virtual.

Para las redes virtuales clásicas, vaya a la página **Información general** de la red virtual clásica en Azure Portal. En la sección **Conexiones VPN**, si la puerta de enlace se está ejecutado en la red virtual, verá la dirección IP de la puerta de enlace. 

![Compruebe si la puerta de enlace se está ejecutando](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

En las redes virtuales, vaya a la página **Información general** de la red virtual. Compruebe si hay **Dispositivos conectados** para la puerta de enlace de red virtual.

![Compruebe el dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Para poder quitar la puerta de enlace, quite primero todos los objetos **Conexión** de la puerta de enlace. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Compruebe si se está ejecutando una puerta de enlace de aplicación en la red virtual

Vaya a la página **Información general** de la red virtual. Compruebe si hay **Dispositivos conectados** para la puerta de enlace de aplicación.

![Compruebe el dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Si hay una puerta de enlace de aplicación, debe quitarla para poder eliminar la red virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Compruebe si Azure Active Directory Domain Services está habilitado en la red virtual

Si Active Directory Domain Services está habilitado y conectado a la red virtual, no se puede eliminar esta red virtual. 

![Compruebe el dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para deshabilitar el servicio, consulte [Deshabilitar Azure Active Directory Domain Services con Azure Portal](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Compruebe si la red virtual está conectada a otro recurso

Busque vínculos de circuito, conexiones y emparejamientos de red virtual. Cualquiera de ellos puede ocasionar errores en la operación de eliminación de una red virtual. 

El orden de eliminación recomendado es como sigue:

1. Conexiones de puerta de enlace
2. Puertas de enlace
3. Direcciones IP
4. Emparejamientos de red virtual
5. App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Compruebe si aún se está ejecutando una máquina virtual en la red virtual

Asegúrese de que ninguna máquina virtual está en la red virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Compruebe si la red virtual se quedó bloqueada en la migración

Si la red virtual se quedó bloqueada en estado de migración, no se puede eliminar. Ejecute el siguiente comando para anular la migración y, a continuación, elimine la red virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Pasos siguientes

- [Azure Virtual Network](virtual-networks-overview.md)
- [Preguntas más frecuentes (P+F) acerca de Azure Virtual Network](virtual-networks-faq.md)
