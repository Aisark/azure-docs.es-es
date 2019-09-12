---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 104849557a8580e16fa1860b7919d1c0252debe9
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2019
ms.locfileid: "67186710"
---
En la tabla siguiente se enumeran los límites asociados a los diferentes niveles de servicio (S1, S2, S3 y F1). Para información sobre el costo de cada *unidad* en cada nivel, consulte los [Precios de IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Estándar | S2 Estándar | S3 Estándar | F1 Gratis |
| --- | --- | --- | --- | --- |
| Mensajes por día |400.000 |6\.000.000 |300.000.000 |8\.000 |
| Unidades máximas |200 |200 |10 |1 |

> [!NOTE]
> Si prevé usar más de 200 unidades con un centro de nivel S1 o S2, o 10 unidades con un centro de nivel S3, póngase en contacto con el servicio de soporte técnico de Microsoft.
> 
> 

En la tabla siguiente se enumeran los límites que se aplican a los recursos de IoT Hub:

| Recurso | Límite |
| --- | --- |
| Cantidad máxima de Centros de IoT de pago por suscripción a Azure |50 |
| Cantidad máxima de Centros de IoT gratis por suscripción a Azure |1 |
| Número máximo de caracteres incluido en un identificador de dispositivo | 128 |
| Número máximo de identidades del dispositivo<br/> devueltas en una sola llamada |1000 |
| Retención máxima de mensajes de IoT Hub para los mensajes de dispositivo a la nube |7 días |
| Tamaño máximo de mensaje del dispositivo a la nube |256 KB |
| Tamaño máximo de lote de dispositivo a la nube |AMQP y HTTP: 256 KB para todo el lote <br/>MQTT: 256 KB por cada mensaje |
| Número máximo de mensajes en lote del dispositivo a la nube |500 |
| Tamaño máximo de mensaje de nube a dispositivo |64 KB |
| TTL máximo para los mensajes de nube a dispositivo |2 días |
| Número máximo de entregas para los mensajes de nube a dispositivo <br/> messages |100 |
| Número máximo de entregas de mensajes de comentarios <br/> en respuesta a un mensaje de nube a dispositivo |100 |
| TTL máximo para los mensajes de comentarios en <br/> respuesta a un mensaje de nube a dispositivo |2 días |
| [Tamaño máximo del dispositivo gemelo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (etiquetas, propiedades notificadas y propiedades deseadas) | 8 KB |
| Tamaño máximo del valor de cadena del dispositivo gemelo | 4 KB |
| [Profundidad máxima del objeto en el dispositivo gemelo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Tamaño máximo de carga del método directo | 128 KB |
| Retención máxima del historial de trabajos | 30 días |
| Número máximo de trabajos simultáneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Número máximo de puntos de conexión adicionales | 10 (para S1, S2 y S3) |
| Número máximo de reglas de enrutamiento de mensajes | 100 (para S1, S2 y S3) |
| Número máximo de secuencias de dispositivos conectados simultáneamente | 50 (solo para S1, S2, S3 y F1) |
| Nivel máximo de transferencia de datos de secuencia de dispositivos | 300 MB por día (solo para S1, S2, S3 y F1) |

> [!NOTE]
> Si necesita más de 50 centros de IoT de pago en una suscripción de Azure, póngase en contacto con el servicio de soporte técnico de Microsoft.

> [!NOTE]
> Actualmente, el número máximo de dispositivos que pueden conectarse a un solo centro de IoT es de 1 000 000. Si quiere aumentar este límite, póngase en contacto con el [soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

El servicio IoT Hub limita las solicitudes cuando se superan las cuotas siguientes:

| Limitación | Valor por centro |
| --- | --- |
| Operaciones de registro de identidad <br/> (crear, recuperar, enumerar, actualizar, eliminar), <br/> importación y exportación masiva o individual |83,33/s/unidad (5000/min/unidad) (para S3). <br/> 1,67/s/unidad (100/min/unidad) (para S1 y S2). |
| Conexiones de dispositivos |6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de dispositivo a nube |6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. |
| Envíos de nube a dispositivo | 83,33/s/unidad (5000/min/unidad) (para S3), 1,67/s/unidad (100/min/unidad) (para S1 y S2). |
| Recepciones de nube a dispositivo |833,33/s/unidad (50 000/min/unidad) (para S3), 16,67/s/unidad (1000/min/unidad) (para S1 y S2). |
| Operaciones de carga de archivos |Notificaciones de carga de 83,33 archivos/s/unidad (5000/min/unidad) (para S3), notificaciones de carga de 1,67 archivos/s/unidad (100/min/unidad) (para S1 y S2). <br/> Puede haber 10 000 URI de SAS fuera para una cuenta de Azure Storage al mismo tiempo.<br/> 10 URI/dispositivo de SAS puede estar fuera al mismo tiempo. |
| Métodos directos | 24 MB/s/unidad (para S3), 480 KB/s/unidad (para S2), 160 KB/s/unidad (para S1).<br/> Según un tamaño de medidor de limitación de 8 KB. |
| Lecturas de dispositivos gemelos | 500/s/unidad (para S3), 100/s o 10/s/unidad como máximo (para S2), 100/s (para S1) |
| Actualizaciones de dispositivos gemelos | 250/s/unidad (para S3), 50/s o 5/s/unidad como máximo (para S2), 50/s (para S1) |
| Operaciones de trabajos <br/> (crear, actualizar, enumerar, eliminar) | 83,33/s/unidad (5000/min/unidad) (para S3), 1,67/s/unidad (100/min/unidad) (para S2), 1,67/s/unidad (100/min/unidad) (para S1). |
| Resultado de operaciones por dispositivo de trabajos | 50/s/unidad (para S3), 10/s o 1/s/unidad como máximo (para S2), 10/s (para S1). |
| Velocidad de iniciación de secuencia de dispositivos | 5 nuevas secuencias/s (solo para S1, S2, S3 y F1). |
