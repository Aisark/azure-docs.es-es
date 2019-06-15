---
title: 'Azure Front Door Service: seguimiento de estado de back-end | Microsoft Docs'
description: Este artículo le ayuda a comprender cómo Azure Front Door Service supervisa el estado de los servidores back-end
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736588"
---
# <a name="health-probes"></a>Sondeos de estado

Con el fin de determinar el estado de cada back-end, cada entorno de Front Door envía periódicamente una solicitud HTTP/HTTPS sintética a cada uno de los servidores back-end configurados. Front Door usa entonces las respuestas de estos sondeos para determinar los "mejores" servidores back-end a los que se deben enrutar las solicitudes de cliente reales.


## <a name="supported-protocols"></a>Protocolos admitidos

Front Door admite el envío de sondeos a través de los protocolos HTTP o HTTPS. Estos sondeos se envían a través de los mismos puertos TCP configurados para enrutar las solicitudes de cliente y no se pueden reemplazar.

## <a name="health-probe-responses"></a>Respuestas de sondeo de estado

| Respuestas  | DESCRIPCIÓN | 
| ------------- | ------------- |
| Determinación del estado  |  Un código de estado 200 - Correcto indica que el back-end está en buen estado. Todo lo demás se considera un error. Si por algún motivo (incluidos los errores de red) no se recibe una respuesta HTTP válida de un sondeo, este se considera un error.|
| Medida de la latencia  | La latencia es el tiempo de reloj medido desde el momento inmediatamente antes de enviar la solicitud de sondeo hasta el momento de recibir el último byte de la respuesta. Como se usa una nueva conexión TCP para cada solicitud, esta medida no está orientada a los servidores back-end con conexiones parcialmente activas existentes.  |

## <a name="how-front-door-determines-backend-health"></a>Cómo determina Front Door el mantenimiento de back-end

Para determinar el mantenimiento, Azure Front Door Service usa el mismo proceso de tres pasos que se describe a continuación.

1. Excluye los servidores back-end deshabilitados.

2. Excluye los servidores back-end que tienen errores de sondeo de estado:
    * Esta selección se realiza examinando las últimas _n_ respuestas de sondeo de estado. Si al menos _x_ están en buen estado, el back-end se considera correcto.

    * Para configurar _n_, se cambia la propiedad SampleSize de la configuración de equilibrio de carga.

    * Para configurar _x_, se cambia la propiedad SuccessfulSamplesRequired de la configuración de equilibrio de carga.

3. Aparte del conjunto de servidores back-end con un estado correcto del grupo de back-end, Front Door mide y mantiene también la latencia (tiempo de ida y vuelta) de cada back-end.


## <a name="complete-health-probe-failure"></a>Error de sondeo de estado completo

Si se produce un error en los sondeos de estado de cada back-end de un grupo de back-end, Front Door considera que todos los servidores back-end están en buen estado y enruta el tráfico entre ellos en una distribución round robin.

Cuando alguno de los back-end vuelve a un estado correcto, Front Door reanuda el algoritmo de equilibrio de carga normal.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
