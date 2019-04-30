---
title: archivo de inclusión
description: archivo de inclusión
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 414bb0183e68cb46e52c379ea3f7aceda5d4170e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446138"
---
## <a name="the-parts-of-the-device-model-schema"></a>Partes del esquema de modelo del dispositivo

Cada modelo de dispositivo, por ejemplo, un refrigerador o camión, define un tipo de dispositivo que el servicio de simulación puede simular. Cada modelo de dispositivo se almacena en un archivo JSON con el siguiente esquema de nivel superior:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Puede ver los archivos de esquema de los dispositivos simulados predeterminados en la [carpeta devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) en GitHub.

En la tabla siguiente se describen las entradas de esquema de nivel superior:

| Entrada de esquema | DESCRIPCIÓN |
| -- | --- |
| `SchemaVersion` | La versión del esquema es siempre `1.0.0` y es específica del formato de este archivo. |
| `Id` | Id. único para este modelo de dispositivo. |
| `Version` | Identifica la versión del modelo de dispositivo. |
| `Name` | Nombre descriptivo para el modelo de dispositivo. |
| `Description` | Descripción del modelo de dispositivo. |
| `Protocol` | Protocolo de conexión que usa el dispositivo. Puede ser `AMQP`, `MQTT` y `HTTP`. |

En las siguientes secciones se describen las demás secciones del esquema JSON:

## <a name="simulation"></a>Simulation

En la sección `Simulation`, debe definir el estado interno del dispositivo simulado. Los valores de telemetría que envía el dispositivo deben formar parte de este estado de dispositivo.

La definición del estado del dispositivo tiene dos elementos:

* `InitialState` define los valores iniciales de todas las propiedades del objeto de estado del dispositivo.
* `Script` identifica un archivo de JavaScript que se ejecuta según una programación para actualizar el estado del dispositivo. Puede usar el archivo de script para que el dispositivo envíe valores de telemetría aleatorios.

Para obtener más información sobre el archivo de JavaScript dedicado a actualizar el objeto de estado del dispositivo, consulte [Understand the device model behavior](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md) (Descripción del comportamiento del modelo del dispositivo).

En el ejemplo siguiente se muestra la definición del objeto de estado del dispositivo en un dispositivo Chiller simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

El servicio de simulación ejecuta el archivo **chiller-01-state.js** cada cinco segundos para actualizar el estado del dispositivo. Puede ver los archivos de JavaScript de los dispositivos simulados predeterminados en la [carpeta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) en GitHub. Como es costumbre, estos archivos de JavaScript tienen el sufijo **-state** para poder distinguirlos de los archivos que implementan los comportamientos del método.

## <a name="properties"></a>Properties (Propiedades)

La sección `Properties` del esquema define los valores de propiedad que el dispositivo envía a la solución. Por ejemplo: 

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Cuando se inicia la solución, consulta a todos los dispositivos simulados para compilar una lista de valores `Type` para usarlos en la interfaz de usuario. La solución usa las propiedades `Latitude` y `Longitude` para agregar la ubicación del dispositivo a la asignación en el panel.

## <a name="telemetry"></a>Telemetría

La matriz `Telemetry` muestra todos los tipos de telemetría que el dispositivo simulado envía a la solución.

En el ejemplo siguiente se envía un mensaje de telemetría JSON cada 10 segundos con los datos `floor`, `vibration`, y `temperature` que recopilaron los sensores del elevador:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` define la estructura del mensaje JSON que envió el dispositivo simulado. Los marcadores de posición en `MessageTemplate` usan la sintaxis `${NAME}` donde `NAME` es una clave del [objeto de estado del dispositivo](#simulation). Las cadenas deben ir entrecomilladas, pero no los números.

`MessageSchema` define el esquema del mensaje que envió el dispositivo simulado. El esquema del mensaje también se publica en IoT Hub para habilitar las aplicaciones de back-end que reutilizarán la información para interpretar la telemetría entrante.

Actualmente, solo se pueden utilizar los esquemas de mensaje JSON. Los campos enumerados en el esquema pueden ser de los siguientes tipos:

* Objeto: serializado mediante JSON
* Binario: serializado mediante base64
* Text
* Boolean
* Entero
* Double
* DateTime

Para enviar mensajes de telemetría en diferentes intervalos, agregue varios tipos de telemetría a la matriz `Telemetry`. En el ejemplo siguiente se envían datos de temperatura y humedad cada 10 segundos y el estado de la luz cada minuto:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Un dispositivo simulado puede responder a métodos de tipo "de la nube al dispositivo" si se llaman desde un servicio IoT Hub. La sección `CloudToDeviceMethods` que se encuentra en el archivo de esquema de modelo del dispositivo se encarga de:

* Definir los métodos a los que el dispositivo simulado puede responder.
* Identificar el archivo de JavaScript que contiene la lógica que se va a ejecutar.

El dispositivo simulado envía la lista de métodos que admite el servicio IoT Hub al que está conectado.

Para obtener más información sobre el archivo de JavaScript dedicado a implementar el comportamiento del dispositivo, consulte [Understand the device model behavior](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md) (Descripción del comportamiento del modelo del dispositivo).

En el ejemplo siguiente se especifican los tres métodos admitidos y los archivos de JavaScript que implementan esos métodos:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Puede ver los archivos de JavaScript de los dispositivos simulados predeterminados en la [carpeta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) en GitHub. Como es costumbre, estos archivos de JavaScript tienen el sufijo **-method** para poder distinguirlos de los archivos que implementan los comportamientos de estado.