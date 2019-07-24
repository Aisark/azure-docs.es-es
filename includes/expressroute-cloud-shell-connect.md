---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186048"
---
 Si está usando Azure Cloud Shell, iniciará sesión automáticamente en su cuenta de Azure después de hacer clic en "Probar". Para iniciar sesión de forma local, abra la consola de PowerShell con privilegios elevados y ejecute el cmdlet para conectarse.

```azurepowershell
Connect-AzAccount
```

Si tiene más de una suscripción, obtenga una lista de las suscripciones de Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique la suscripción que desea usar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```