---
title: Esquema de suscripción de Azure Event Grid
description: Describe las propiedades de suscripción a un evento con Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 6129c7f498ce6c52fce4266f693c6a304642f8c3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845462"
---
# <a name="event-grid-subscription-schema"></a>Esquema de suscripción de Event Grid

Para crear una suscripción de Event Grid, se envía una solicitud a la operación de suscripción Crear evento. Utilice el siguiente formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por ejemplo, con el fin de crear una suscripción de eventos para una cuenta de almacenamiento denominada "`examplestorage`" en un grupo de recursos llamado `examplegroup`, utilice el formato siguiente:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

El nombre de la suscripción de eventos debe tener entre 3 y 64 caracteres de longitud y solo puede contener los siguientes caracteres: a-z, A-z, 0-9, y "-". En el artículo se describen las propiedades y el esquema del cuerpo de la solicitud.
 
## <a name="event-subscription-properties"></a>Propiedades de la suscripción de eventos

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| destination | object | El objeto que define el punto de conexión. |
| filter | object | Un campo opcional para filtrar los tipos de eventos. |

### <a name="destination-object"></a>Objeto de destino

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| endpointType | string | El tipo de punto de conexión de la suscripción (webhook/HTTP, centro de eventos o cola). | 
| endpointUrl | string | Dirección URL de destino de los eventos en esta suscripción a eventos. | 

### <a name="filter-object"></a>Objeto de filtro

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| includedEventTypes | array | Realiza la correspondencia cuando el tipo de evento del mensaje de evento es una coincidencia exacta con uno de estos nombres de tipo de evento. Genera un error cuando el nombre del evento no coincide con los nombres de tipo de evento registrados para el origen del evento. El valor predeterminado coincide con todos los tipos de evento. |
| subjectBeginsWith | string | Un filtro de coincidencia de prefijo en el campo de asunto del mensaje del evento. El valor predeterminado o una cadena vacía coincide con todos los tipos de evento. | 
| subjectEndsWith | string | Un filtro de coincidencia de sufijo en el campo de asunto del mensaje del evento. El valor predeterminado o una cadena vacía coincide con todos los tipos de evento. |
| isSubjectCaseSensitive | string | Controla la coincidencia que distingue mayúsculas de minúsculas en los filtros. |


## <a name="example-subscription-schema"></a>Esquema de suscripción de ejemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver una introducción a Event Grid, consulte el artículo acerca de [qué es Event Grid](overview.md).
