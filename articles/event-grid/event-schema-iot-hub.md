---
title: Esquema de Azure Event Grid para IoT Hub | Microsoft Docs
description: Página de referencia sobre el formato y las propiedades del esquema de eventos para IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393200"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Esquema de eventos de Azure Event Grid para IoT Hub

En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure IoT Hub. Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md). 

Para una lista de scripts de ejemplo y tutoriales, consulte los [orígenes de eventos de IoT Hub](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tipos de eventos disponibles

Azure IoT Hub emite los siguientes tipos de eventos:

| Tipo de evento | DESCRIPCIÓN |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Se publica cuando se registra un dispositivo en una instancia de IoT Hub. |
| Microsoft.Devices.DeviceDeleted | Se publica cuando se elimina un dispositivo de una instancia de IoT Hub. | 
| Microsoft.Devices.DeviceConnected | Se publica cuando se conecta un dispositivo a una instancia de IoT Hub. |
| Microsoft.Devices.DeviceDisconnected | Se publica cuando se desconecta un dispositivo de una instancia de IoT Hub. | 
| Microsoft.Devices.DeviceTelemetry | Se publica cuando se envía un mensaje de telemetría a IoT hub. |

Todos los eventos de dispositivo, excepto los eventos de telemetría de dispositivo están disponibles con carácter general en todas las regiones compatibles con Event Grid. Evento de telemetría de dispositivo se encuentra en versión preliminar pública y está disponible en todas las regiones excepto este de Estados Unidos, oeste de Estados Unidos, Europa occidental, [Azure Government](/azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome.md), y [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Evento de ejemplo

El esquema para los eventos de dispositivo conectado y dispositivo desconectado tiene la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se conecta un dispositivo a una instancia de IoT Hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

El evento DeviceTelemetry se genera cuando se envía un evento de telemetría a IoT Hub. A continuación se muestra un esquema de ejemplo para este evento.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Los esquemas de los eventos DeviceCreated y DeviceDeleted tienen la misma estructura. Este evento de ejemplo muestra el esquema de un evento que se genera cuando se registra un dispositivo en una instancia de IoT Hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Propiedades de evento

Todos los eventos contienen los mismos datos de nivel superior: 

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| id | string | Identificador único para el evento |
| topic | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| subject | string | Ruta al asunto del evento definida por el anunciante. |
| eventType | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| eventTime | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| data | objeto | Datos del evento de IoT Hub.  |
| dataVersion | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| metadataVersion | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |

Para todos los eventos de IoT Hub, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| hubName | string | Nombre de la instancia de IoT Hub en que se creó o eliminó el dispositivo. |
| deviceId | string | Identificador único del dispositivo. Una cadena que distingue mayúsculas de minúsculas puede tener una longitud de hasta 128 caracteres alfanuméricos ASCII de 7 bits más los siguientes caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

El contenido del objeto de datos es diferente para cada publicador de eventos. 

Para los eventos de IoT Hub de **Dispositivo conectado** y **Dispositivo desconectado**, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| moduleId | string | Identificador único del módulo. Este campo solo es la salida de los dispositivos del módulo. Una cadena que distingue mayúsculas de minúsculas puede tener una longitud de hasta 128 caracteres alfanuméricos ASCII de 7 bits más los siguientes caracteres especiales: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objeto | Información de evento del estado de conexión del dispositivo
| sequenceNumber | string | Un número que indica el orden de los eventos de dispositivo conectado o dispositivo desconectado. El evento más reciente tendrá un número de secuencia mayor que el del evento anterior. Este número puede variar en más de 1, pero el orden es siempre ascendente. Consulte [cómo usar el número de secuencia](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para **telemetría de dispositivo** eventos de IoT Hub, el objeto de datos contiene el mensaje de dispositivo a nube en [formato de mensaje de IoT hub](../iot-hub/iot-hub-devguide-messages-construct.md) y tiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| Cuerpo | string | El contenido del mensaje desde el dispositivo. |
| properties | string | Las propiedades de la aplicación son cadenas definidas por el usuario que se pueden agregar al mensaje. Estos campos son opcionales. |
| Propiedades del sistema | string | [Propiedades del sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ayudar a identificar el contenido y el origen de los mensajes. Mensaje de telemetría de dispositivo debe estar en un formato JSON válido con el tipo de contenido establecido en JSON y contentEncoding establecido en UTF-8 en las propiedades de mensaje del sistema. Si no se establece, IoT Hub escribirá los mensajes en formato codificado de base 64.  |

Para los eventos de IoT Hub de **Dispositivo creado** y **Dispositivo eliminado**, el objeto de datos contiene las siguientes propiedades:

| Propiedad | Escriba | DESCRIPCIÓN |
| -------- | ---- | ----------- |
| twin | objeto | Información sobre el dispositivo gemelo, que es la representación en la nube de metadatos de dispositivo de la aplicación. | 
| deviceID | string | Identificador único del dispositivo gemelo. | 
| eTag | string | Un validador para garantizar la coherencia de las actualizaciones de un dispositivo gemelo. Se garantiza que cada etiqueta de entidad sea exclusiva de cada dispositivo gemelo. |  
| deviceEtag| string | Un validador para garantizar la coherencia de las actualizaciones de un registro de dispositivo. Se garantiza que cada deviceEtag sea exclusiva de cada registro de dispositivo. |
| status | string | Si el dispositivo gemelo está habilitado o deshabilitado. | 
| statusUpdateTime | string | La marca de tiempo ISO8601 de la última actualización de estado del dispositivo gemelo. |
| connectionState | string | Si el dispositivo está conectado o desconectado. | 
| lastActivityTime | string | La marca de tiempo ISO8601 de la última actividad. | 
| cloudToDeviceMessageCount | integer | Número de mensajes de la nube al dispositivo enviados a este dispositivo. | 
| authenticationType | string | Tipo de autenticación usado para este dispositivo: `SAS`, `SelfSigned`o `CertificateAuthority`. |
| x509Thumbprint | string | La huella digital es un valor exclusivo del certificado x509, que suele usarse para buscar un certificado concreto en un almacén de certificados. La huella digital se genera de forma dinámica con el algoritmo SHA1 y no existe físicamente en el certificado. | 
| primaryThumbprint | string | Huella digital principal del certificado x509. |
| secondaryThumbprint | string | Huella digital secundaria del certificado x509. | 
| version | integer | Un entero que se incrementa en uno cada vez que se actualiza el dispositivo gemelo. |
| desired | objeto | Una parte de las propiedades que solo el back-end de la aplicación puede escribir y que el dispositivo puede leer. | 
| reported | objeto | Una parte de las propiedades que solo el dispositivo puede escribir y que el back-end de la aplicación puede leer. |
| lastUpdated | string | La marca de tiempo ISO8601 de la última actualización de propiedades del dispositivo gemelo. | 

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).
* Para más información sobre cómo IoT Hub y Event Grid funcionan de forma conjunta, vea [Reacción a eventos de IoT Hub mediante Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md).