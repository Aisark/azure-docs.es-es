---
title: Envío o recepción de eventos desde Azure Event Hubs mediante Node.js (más reciente)
description: Este artículo es un tutorial para crear una aplicación de Node.js que envíe eventos a Azure Event Hubs, o los reciba de él, mediante el paquete azure/event-hubs versión 5 más reciente.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906374"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Envío o recepción de eventos en Event Hubs mediante Node.js (azure-eventhub versión 5)

Azure Event Hubs es una plataforma de streaming de macrodatos y un servicio de ingesta de eventos que puede recibir y procesar millones de eventos por segundo. Los centros de eventos pueden procesar y almacenar los eventos, los datos y la telemetría que generan los dispositivos y el software distribuido. Los datos que se envían a un centro de eventos se pueden transformar y almacenar mediante cualquier proveedor de análisis en tiempo real o adaptadores de almacenamiento/procesamiento por lotes. Para más información, consulte [Información general de Event Hubs](event-hubs-about.md) y [Características de Event Hubs](event-hubs-features.md).

En este inicio rápido se describe cómo crear aplicaciones de Node.js que envíen o reciban eventos de un centro de eventos.

> [!IMPORTANT]
> Este inicio rápido usa la versión 5 del SDK de JavaScript de Azure Event Hubs. Para ver un inicio rápido en el que se usa la versión 2 del SDK del JavaScript, consulte [este artículo](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Prerequisites

Para completar este tutorial de inicio rápido, debe cumplir los siguientes requisitos previos:

- Suscripción a Azure. Si no tiene una, [cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.  
- Node.js, versión 8.x o posteriores. Descargue la versión más reciente del [soporte técnico a largo plazo (LTS)](https://nodejs.org).  
- Visual Studio Code (se recomienda) o cualquier otro entorno de desarrollo integrado (IDE).  
- Un espacio de nombres de Event Hubs y un centro de eventos activos. Para crearlos, siga estos pasos: 

   1. En [Azure Portal](https://portal.azure.com), cree un espacio de nombres del tipo *Event Hubs* y obtenga las credenciales de administración que la aplicación necesita para comunicarse con el centro de eventos. 
   1. Para crear tanto el espacio de nombres como el centro de eventos, siga las instrucciones que encontrará en [Inicio rápido: Creación de un centro de eventos desde Azure Portal](event-hubs-create.md).
   1. Para continuar, siga las instrucciones de este inicio rápido. 
   1. Para obtener la cadena de conexión del espacio de nombres del centro de eventos, siga las instrucciones de [Obtención de una cadena de conexión](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Registre la cadena de conexión para usarla más adelante en este inicio rápido.

### <a name="install-the-npm-package"></a>Instalación del paquete npm
Para instalar el [paquete del administrador de paquetes de nodos (npm) para Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), abra un símbolo del sistema que tenga *npm* en su ruta de acceso, cambie el directorio a la carpeta en la que quiere tener los ejemplos y después ejecute este comando:

```shell
npm install @azure/event-hubs
```

En el lado del receptor, tiene que instalar dos paquetes más. En este inicio rápido se usar Azure Blob Storage para conservar los puntos de control, con el fin de que el programa no lea los eventos que ya ha leído. Ejecuta los puntos de comprobación de metadatos en los mensajes recibidos a intervalos regulares en un blob. Este enfoque facilita la tarea de seguir recibiendo posteriormente mensajes desde donde lo dejó.

Ejecute los comandos siguientes:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Envío de eventos

En esta sección se crea una aplicación de Node.js que envía eventos a un centro de eventos.

1. Abra el editor que prefiera, como [Visual Studio Code](https://code.visualstudio.com).
1. Cree un archivo denominado *send.js* y pegue en él el código siguiente:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. En el código, use valores reales para reemplazar los siguientes:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Ejecute `node send.js` para ejecutar este archivo. Este comando envía un lote de tres eventos al centro de eventos.
1. En Azure Portal, compruebe que el centro de eventos ha recibido los mensajes. En la sección **Métricas**, cambie a la vista **Mensajes**. Actualice la página para actualizar el gráfico. Pueden pasar unos segundos hasta que aparezca que los mensajes se han recibido.

    [![Comprobación de que el centro de eventos ha recibido los mensajes](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para ver todo el código fuente, incluidos los comentarios informativos adicionales, vaya a la [página sendEvents.js de GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Felicidades. Ha enviado eventos a un centro de eventos.


## <a name="receive-events"></a>Recepción de eventos
En esta sección recibe eventos de un centro de eventos mediante un almacén de puntos de comprobación de Azure Blob Storage en una aplicación de Node.js. Ejecuta los puntos de comprobación de metadatos en los mensajes recibidos a intervalos regulares en Azure Storage Blob. Este enfoque facilita la tarea de seguir recibiendo posteriormente mensajes desde donde lo dejó.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Creación de una cuenta de Azure Storage y un contenedor de blobs
Para crear una cuenta de Azure Storage y un contenedor de blobs en ella, realice estas acciones:

1. [Creación de una cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Creación de un contenedor de blobs en la cuenta de almacenamiento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtención de la cadena de conexión para una cuenta de almacenamiento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Asegúrese de registrar la cadena de conexión y el nombre del contenedor para usarlo posteriormente en el código de recepción.

### <a name="write-code-to-receive-events"></a>Escritura de código para recibir eventos

1. Abra el editor que prefiera, como [Visual Studio Code](https://code.visualstudio.com).
1. Cree un archivo denominado *receive.js* y pegue en él el código siguiente:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. En el código, use valores reales para reemplazar los siguientes:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Ejecute `node receive.js` en un símbolo del sistema para ejecutar este archivo. La ventana debe mostrar mensajes sobre los eventos recibidos.

    > [!NOTE]
    > Para ver todo el código fuente, incluidos los comentarios informativos adicionales, vaya a la [página receiveEventsUsingCheckpointStore.js de GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Felicidades. Ya ha recibido eventos procedentes del centro de eventos. El programa del destinatario recibirá los eventos de todas las particiones del grupo de consumidores predeterminado en el centro de eventos.

## <a name="next-steps"></a>Pasos siguientes
Consulte estos ejemplos de código en GitHub:

- [Ejemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ejemplos de TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
