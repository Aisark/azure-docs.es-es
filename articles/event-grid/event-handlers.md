---
title: Controladores de eventos de Azure Event Grid
description: Describe los controladores de eventos compatibles con Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 915d1284d66438219fc9aba893512e5f6a5b02b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305041"
---
# <a name="event-handlers-in-azure-event-grid"></a>Controladores de eventos de Azure Event Grid

Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos. También puede usar cualquier webhook para controlar los eventos. El webhook no necesita estar hospedado en Azure para controlar los eventos. Event Grid solo admite puntos de conexión de webhook HTTPS.

Este artículo contiene vínculos a contenido para cada controlador de eventos.

## <a name="azure-automation"></a>Azure Automation

Use Azure Automation para procesar los eventos con runbooks automatizados.

|Título  |DESCRIPCIÓN  |
|---------|---------|
|[Tutorial: Integración de Azure Automation con Event Grid y Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Cree una máquina virtual, que envía un evento. El evento desencadena un runbook de Automation que etiqueta la máquina virtual y genera un mensaje que se envía a un canal de Equipos de Microsoft. |

## <a name="azure-functions"></a>Azure Functions

Use Azure Functions para responder sin servidor a los eventos.

Cuando use Azure Functions como controlador, utilice el desencadenador de Event Grid en lugar de los desencadenadores HTTP genéricos. Event Grid valida automáticamente los desencadenadores Function de Event Grid. Con desencadenadores HTTP genéricos, debe implementar la [respuesta de validación](security-authentication.md#webhook-event-delivery).

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Desencadenador de Event Grid para Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Información general sobre el uso del desencadenador de Event Grid en Functions. |
| [Tutorial: Automatización del cambio de tamaño de las imágenes cargadas mediante Event Grid](resize-images-on-storage-blob-upload-event.md) | Los usuarios cargan imágenes a través de la aplicación web en la cuenta de almacenamiento. Cuando se crea un blob de almacenamiento, Event Grid envía un evento a la aplicación de función, que cambia el tamaño de la imagen cargada. |
| [Tutorial: transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md) | Cuando Event Hubs crea un archivo de captura, Event Grid envía un evento a una aplicación de función. La aplicación recupera el archivo de captura y migra los datos a un almacenamiento de datos. |
| [Tutorial: Ejemplos de integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |

## <a name="event-hubs"></a>Event Hubs

Use Event Hubs cuando la solución obtenga eventos más rápido de lo que puede procesarlos. La aplicación procesa los eventos de Event Hubs según su propia programación. Puede escalar el procesamiento de eventos para controlar los eventos entrantes.

Event Hubs puede actuar como origen de eventos o como controlador de eventos. En el siguiente artículo, se explica cómo utilizar Event Hubs como controlador.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Guía de inicio rápido: Enrutamiento de eventos personalizados a Azure Event Hubs con la CLI de Azure y Event Grid](custom-event-to-eventhub.md) | Envía un evento personalizado a un centro de eventos para que lo procese una aplicación. |
| [Plantilla de Resource Manager: tema personalizado y punto de conexión a Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Plantilla de Resource Manager que crea una suscripción a un tema personalizado. Envía eventos a una instancia de Azure Event Hubs. |

Para obtener ejemplos de Event Hubs como origen, consulte este artículo sobre el [origen de Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>conexiones híbridas

Use las conexiones híbridas de Azure Relay para enviar eventos a las aplicaciones que están dentro de una red empresarial y no tienen un punto de conexión de acceso público.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Tutorial: Envío de eventos a una conexión híbrida](custom-event-to-hybrid-connection.md) | Envía un evento personalizado a una conexión híbrida existente para su procesamiento mediante una aplicación de escucha. |

## <a name="logic-apps"></a>Logic Apps

Use Logic Apps para automatizar los procesos de negocios para responder a eventos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Tutorial: supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Una aplicación lógica supervisa los cambios realizados en una máquina virtual y envía mensajes de correo electrónico sobre dichos cambios. |
| [Tutorial: envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Una aplicación lógica envía un correo electrónico de notificación cada vez que se agrega un dispositivo al centro de IoT. |
| [Tutorial: Ejemplos de integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |

## <a name="service-bus-queue-preview"></a>Cola de Service Bus (versión preliminar)

Usar Service Bus como un controlador de eventos para enrutar los eventos de Event Grid directamente a las colas de Service Bus para su uso en escenarios de almacenamiento en búfer o comando y control de aplicaciones empresariales. La versión preliminar no funciona con los temas de Service Bus y las sesiones, pero funciona con todos los niveles de colas de Service Bus.

Tenga en cuenta, al Bus de servicio como un controlador se encuentra en versión preliminar pública, debe instalar la extensión de CLI o PowerShell cuando las usa para crear las suscripciones de eventos.

### <a name="using-cli"></a>Uso de CLI

Para la CLI de Azure, el ejemplo siguiente se suscribirá un se conecta y tema de Event Grid a una cola de Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Queue Storage

Use Queue Storage para recibir eventos que deben extraerse. Puede usar Queue Storage cuando tenga un proceso de ejecución prolongada que tarde demasiado tiempo en responder. Al enviar eventos a Queue Storage, la aplicación puede extraer y procesar eventos siguiendo su propia programación.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Guía de inicio rápido: Enrutamiento de eventos personalizados a Azure Queue Storage con la CLI de Azure y Event Grid](custom-event-to-queue-storage.md) | Describe cómo enviar eventos personalizados a una instancia de Queue Storage. |

## <a name="webhooks"></a>WebHooks

Use webhooks para puntos de conexión personalizables que respondan a eventos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| Guía de inicio rápido: Creación y enrutamiento de eventos personalizados con la [CLI de Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) y [Azure Portal](custom-event-quickstart-portal.md). | Explica cómo enviar eventos personalizados a un webhook. |
| Guía de inicio rápido: Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado con la [CLI de Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) y [Azure Portal](blob-event-quickstart-portal.md). | Explica cómo enviar eventos de Blob Storage a un webhook. |
| [Guía de inicio rápido: Envío de eventos de Container Registry](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Explica cómo utilizar la CLI de Azure para enviar eventos de Container Registry. |
| [Introducción: Recepción de eventos en un punto de conexión de HTTP](receive-events.md) | Se describe cómo validar un punto de conexión de HTTP para recibir eventos de una suscripción a eventos y, a continuación, recibir y deserializar los eventos. |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
