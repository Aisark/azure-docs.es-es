---
title: Esquema de eventos de Azure App Configuration con Azure Event Grid
description: Describe las propiedades que se proporcionan para los eventos de Azure App Configuration Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735787"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Esquema de eventos de Azure Event Grid para Azure App Configuration

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure App Configuration. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md).

Para una lista de scripts de ejemplo y tutoriales, consulte el [origen del evento de Azure App Configuration](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tipos de eventos disponibles

La configuración de Azure App Configuration emite los siguientes tipos de evento:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Se genera cuando se crea o se sustituye un valor de clave. |
| Microsoft.AppConfiguration.KeyValueDeleted | Se genera cuando se elimina un valor de clave. |

## <a name="example-event"></a>Evento de ejemplo

En el siguiente ejemplo se muestra el esquema de un evento modificado de valor clave: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

El esquema para un evento de eliminación de valor clave es similar: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
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
| data | objeto | Datos del evento de App Configuration. |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

El objeto data tiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| key | string | Clave del valor clave que se ha modificado o eliminado. |
| label | string | Etiqueta, si procede, del valor clave que se ha modificado o eliminado. |
| ETag | string | Para `KeyValueModified`, la etag del nuevo valor clave. Para `KeyValueDeleted`, la etag del valor clave que se ha eliminado. |
 
## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
* Para una introducción a cómo trabajar con eventos de Azure App Configuration, consulte [Enrutamiento de eventos de Azure App Configuration con la CLI de Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 