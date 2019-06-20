---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186236"
---
## <a name="scenario"></a>Escenario
Para ilustrar mejor cómo crear grupos de seguridad de red, en este documento se usará el siguiente escenario:

![Escenario de red virtual](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

En este escenario, creará un grupo de seguridad de red para cada subred de la red virtual **TestVNet**, tal y como se describe a continuación: 

* **NSG-FrontEnd**. El grupo de seguridad de red de front-end se aplicará a la subred *FrontEnd* y contiene dos reglas:    
  * **rdp-rule**. Permite el tráfico RDP a la subred *FrontEnd*.
  * **web-rule**. Permite el tráfico HTTP a la subred *FrontEnd*.
* **NSG-BackEnd**. El grupo de seguridad de red de back-end se aplicará a la subred *BackEnd* y contiene dos reglas:    
  * **sql-rule**. Permite el tráfico SQL tan solo desde la subred *FrontEnd*.
  * **web-rule**. Deniega todo el tráfico ligado a Internet de la subred *BackEnd*.

La combinación de estas reglas crea un escenario similar a DMZ, donde la subred de back-end solo puede recibir tráfico entrante para SQL de la subred de front-end y no tiene acceso a Internet, mientras que la subred de front-end puede comunicarse con Internet y recibir solicitudes HTTP entrantes solamente.

