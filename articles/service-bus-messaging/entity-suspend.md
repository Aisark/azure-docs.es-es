---
title: Entidades de mensajería de suspensión de Azure Service Bus | Microsoft Docs
description: Suspenda y reactive entidades de mensajes de Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: e2ffda3141462d19557af3af26c117ee505c40ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170800"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspensión y reactivación de entidades de mensajería (deshabilitar)

Las colas, los temas y las suscripciones se pueden suspender temporalmente. La suspensión coloca la entidad en un estado deshabilitado, en el que todos los mensajes se mantienen en el almacenamiento. Sin embargo, no se quitan ni se agregan mensajes, y las operaciones del protocolo respectivo producen error.

Normalmente la suspensión de una entidad se realiza por motivos administrativos urgentes. Una de estas situaciones puede darse cuando se tiene un receptor defectuoso que saca mensajes de la cola, no realiza el procesamiento y encima completa incorrectamente los mensajes y los quita. Si se diagnostica este comportamiento, la cola se puede deshabilitar para las recepciones hasta que se implemente el código correcto y pueda impedirse que se sigan perdiendo datos debido a código incorrecto.

El usuario o el sistema pueden realizar la suspensión o la reactivación. El sistema solo suspende las entidades por motivos administrativos graves como haber alcanzado el límite de gasto de suscripción. El usuario no puede reactivar las entidades deshabilitadas por el sistema, pero se restauran cuando se ha solucionado la causa de la suspensión.

En el portal, la sección **Propiedades** de la entidad respectiva permite cambiar el estado; en la captura de pantalla siguiente se muestra la alternancia en una cola:

![][1]

El portal solo permite deshabilitar completamente las colas. También puede deshabilitar las operaciones de envío y recepción por separado mediante las API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) de Service Bus en el SDK de .NET Framework, o con una plantilla de Azure Resource Manager mediante la CLI de Azure o Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Estados de suspensión

Los estados que se pueden establecer para una cola son:

-   **Activa**: la cola está activa.
-   **Deshabilitado**: la cola se ha suspendido.
-   **SendDisabled**: la cola se suspende parcialmente y se permite la recepción.
-   **ReceiveDisabled**: la cola se suspende parcialmente y se permite el envío.

En las suscripciones y los temas, solo se pueden establecer los estados **Activo** y **Deshabilitado**.

La enumeración [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) también define un conjunto de estados de transición que solo puede establecer el sistema. El comando de PowerShell para deshabilitar una cola se muestra en el ejemplo siguiente. El comando de reactivación es equivalente a establecer `Status` en **Activo**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

