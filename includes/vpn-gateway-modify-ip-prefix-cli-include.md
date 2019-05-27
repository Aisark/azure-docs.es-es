---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b381c5ad8fd81cd9b7411e1f4679b3f5214e6de9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121069"
---
### <a name="noconnection"></a>Para modificar los prefijos de dirección IP de la puerta de enlace de red local (sin conexión de puerta de enlace)

Si no dispone de una conexión de puerta de enlace y desea agregar o quitar prefijos de direcciones IP, utilice el mismo comando que se usa para crear la puerta de enlace de red local, [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway). Este comando también se puede usar para actualizar la dirección IP de la puerta de enlace del dispositivo VPN. Para sobrescribir la configuración actual, utilice el nombre existente de la puerta de enlace de red local. Si usa otro nombre, creará una nueva puerta de enlace de red local, en lugar de sobrescribir la existente.

Cada vez que se realiza un cambio, debe especificarse toda la lista de prefijos, no solo los prefijos que se desea cambiar. Especifique solo los prefijos que desea conservar. En este caso, 10.0.0.0/24 y 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Para modificar los prefijos de dirección IP de la puerta de enlace de red local (conexión de puerta de enlace existente)

Si tiene una conexión de puerta de enlace y desea agregar o quitar prefijos de direcciones IP, estos se pueden actualizar mediante [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway). Esto tendrá como resultado un tiempo de inactividad para la conexión VPN. Al modificar los prefijos de direcciones IP, no preciso eliminar la puerta de enlace VPN.

Cada vez que se realiza un cambio, debe especificarse toda la lista de prefijos, no solo los prefijos que se desea cambiar. En este ejemplo, 10.0.0.0/24 y 20.0.0.0/24 ya están presentes. Agregamos los prefijos 30.0.0.0/24 y 40.0.0.0/24, y especificamos los cuatro prefijos al actualizar.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
