---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133589"
---
Abra la consola de PowerShell con privilegios elevados.

Si ejecuta Azure PowerShell localmente, conéctese a la cuenta de Azure. El cmdlet *Connect-AzAccount* le pide las credenciales. Después de la autenticación, descarga la configuración de la cuenta para que esté disponible en Azure PowerShell. Si usa Azure Cloud Shell en su lugar, no es necesario ejecutar *Connect-AzAccount*. Azure Cloud Shell se conecta a su cuenta de Azure automáticamente.

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