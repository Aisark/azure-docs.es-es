---
title: Esquema de eventos de Blob Storage de Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de Blob Storage con Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614901"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos de Azure Event Grid para Blob Storage

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Blob Storage. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

Para obtener una lista de scripts de ejemplo y tutoriales, consulte [Origen de eventos de almacenamiento](event-sources.md#storage).

## <a name="available-event-types"></a>Tipos de eventos disponibles

Blob Storage emite los siguientes tipos de eventos:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Se genera cuando se crea un blob. |
| Microsoft.Storage.BlobDeleted | Se genera cuando se elimina un blob. |

## <a name="example-event"></a>Evento de ejemplo

En el ejemplo siguiente, se muestra el esquema de un evento de creación de un blob: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

El esquema para un evento de eliminación de un blob es similar: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Propiedades de evento

Un evento tiene los siguientes datos de nivel superior:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| topic | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| id | string | Identificador único para el evento |
| data | objeto | Datos de eventos de Blob Storage. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| api | string | Operación que desencadenó el evento. |
| clientRequestId | string | Valor opaco generado por el cliente con un límite de caracteres de 1 KB. Si se habilitó el registro de análisis de almacenamiento, se graba en los registros de análisis. |
| requestId | string | Identificador único de la solicitud. Úselo para solucionar problemas de la solicitud. |
| eTag | string | Valor que puede usar para realizar operaciones de manera condicional. |
| contentType | string | Tipo de contenido especificado para el blob. |
| contentLength | integer | Tamaño del blob en bytes. |
| blobType | string | El tipo de blob. Los valores válidos son "BlockBlob" o "PageBlob". |
| url | string | Ruta de acceso al blob. |
| sequencer | string | Valor controlado por el usuario que puede usar para realizar un seguimiento de las solicitudes. |
| storageDiagnostics | objeto | Información sobre los diagnósticos de almacenamiento. |
 
## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
* Para obtener una introducción acerca del trabajo con eventos de Blob Storage, consulte [Enrutamiento de eventos de Blob Storage: CLI de Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
