---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120535"
---
Para los datos en tránsito:

- Se utiliza TLS 1.2 Estándar para los datos que circulan entre el dispositivo y Azure. No hay ninguna reserva para TLS 1.1 y versiones anteriores. Si no se admite TLS 1.2, se bloqueará la comunicación del agente. TLS 1.2 también es necesario para la administración del portal y SDK.
- Cuando los clientes acceden al dispositivo mediante la interfaz web local de un explorador, se utiliza TLS 1.2 Estándar como protocolo seguro predeterminado.

    - El procedimiento recomendado consiste en configurar el explorador para usar TLS 1.2.
    - Si el explorador no admite TLS 1.2, puede usar TLS 1.1 o TLS 1.0.
- Se recomienda utilizar SMB 3.0 con cifrado para proteger los datos cuando se copien de los servidores de datos.
