---
title: Puertos y protocolos de comunicación de Azure IoT Hub | Microsoft Docs
description: 'Guía del desarrollador: describe los protocolos de comunicación compatibles para las comunicaciones de dispositivo a nube y de nube a dispositivo, además de los números de puerto que deben estar abiertos.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 7082ebc4ca3066f84ca9790797cfa04e437f78a3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051062"
---
# <a name="reference---choose-a-communication-protocol"></a>Referencia: elección de un protocolo de comunicación

IoT Hub permite que los dispositivos usen los protocolos de comunicaciones de dispositivo siguientes:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT sobre WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP sobre WebSockets
* HTTPS

Para información sobre cómo estos protocolos admiten características específicas de IoT Hub, consulte [Guía de comunicación de dispositivo a nube](iot-hub-devguide-d2c-guidance.md) y [Guía de comunicación de nube a dispositivo](iot-hub-devguide-c2d-guidance.md).

La tabla siguiente proporciona recomendaciones generales para la elección del protocolo:

| Protocolo | Cuándo elegir este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilice todos los dispositivos que no requieren conexión a varios dispositivos (cada uno con sus propias credenciales por servicio) sobre la misma conexión TLS. |
| AMQP <br> AMQP sobre WebSocket |Usar en puertas de enlace de campo y en la nube para aprovechar las ventajas de la multiplexación de la conexión entre dispositivos. |
| HTTPS |Usar con dispositivos que no admiten otros protocolos. |

Considere los siguientes aspectos a la hora de elegir el protocolo para las comunicaciones del dispositivo:

* **Patrón de nube a dispositivo**. HTTPS no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTPS, los dispositivos sondean los mensajes de nube a dispositivo en IoT Hub. Este enfoque es ineficaz tanto para el dispositivo como para IoT Hub. Según las directrices actuales de HTTPS, cada dispositivo sondeará si hay mensajes cada 25 minutos o más. AMQP y MQTT admiten la inserción de servidor cuando se reciben mensajes de la nube a dispositivo. Permiten inserciones inmediatas de mensajes desde IoT Hub en el dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar los protocolos MQTT o AMQP. Para dispositivos conectados en raras ocasiones, HTTPS funciona bien.

* **Puertas de enlace de campo**. Cuando se usan MQTT y HTTPS, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. En [escenarios de puerta de enlace de campo](iot-hub-devguide-endpoints.md#field-gateways) que requieren una conexión TLS entre la puerta de enlace de campo e IoT Hub para cada dispositivo conectado, estos protocolos no son óptimos.

* **Dispositivos con bajos recursos**. Las bibliotecas de MQTT y HTTPS tienen una huella menor que las bibliotecas de AMQP. Por ello, si el dispositivo tiene recursos limitados (por ejemplo, menos de 1 MB de RAM), estos protocolos pueden ser la única implementación de protocolo disponible.

* **Cruce seguro de red**. El protocolo AMQP estándar utiliza el puerto 5671 y MQTT escucha en el puerto 8883. El uso de estos puertos podría producir problemas en redes cerradas a protocolos que no sean HTTPS. Use MQTT sobre WebSockets, AMQP sobre WebSockets o HTTPS en este escenario.

* **Tamaño de carga**. MQTT y AMQP son protocolos binarios que producen cargas más compactas que HTTPS.

> [!WARNING]
> Cuando se usa HTTPS, cada dispositivo sondeará si hay mensajes de la nube a dispositivo cada 25 minutos o más. Sin embargo, durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.

## <a name="port-numbers"></a>Números de puerto

Los dispositivos pueden comunicarse con Azure IoT Hub en Azure mediante diversos protocolos. Normalmente, la elección del protocolo se basa en los requisitos específicos de la solución. En la tabla siguiente se indican los puertos de salida que deben estar abiertos para que un dispositivo pueda usar un protocolo concreto:

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTPS |443 |

Una vez creado un centro de IoT en una región de Azure, el centro de IoT mantiene la misma dirección IP durante toda su existencia. Sin embargo, si Microsoft mueve el centro de IoT a una unidad de escalado diferente para mantener la calidad de servicio, se le asigna una nueva dirección IP.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo IoT Hub implementa el protocolo MQTT, consulte [Comunicación con la instancia de IoT Hub mediante el protocolo MQTT](iot-hub-mqtt-support.md).
