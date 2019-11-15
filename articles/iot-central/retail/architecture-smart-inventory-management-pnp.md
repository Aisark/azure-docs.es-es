---
title: Arquitectura de la administración inteligente del inventario de IoT | Microsoft Docs
description: Una arquitectura de la plantilla de administración inteligente del inventario de IoT para IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d0bf023be9c34f449c40ff74ba9e68ea5e197e2e
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615362"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arquitectura de la plantilla de la aplicación de administración inteligente del inventario de IoT Central

Los partners y clientes pueden aprovechar la plantilla de la aplicación y las siguientes instrucciones para desarrollar soluciones de **administración de inventarios inteligente**.

> [!div class="mx-imgBorder"]
> ![administración inteligente del inventario](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Conjunto de sensores de IoT que envían datos de telemetría a un dispositivo de puerta de enlace.
2. Dispositivos de puerta de enlace que envían datos de telemetría y conclusiones agregadas a IoT Central.
3. Los datos se enrutan al servicio de Azure deseado para su manipulación.
4. Se pueden aprovechar los servicios de Azure (como ASA o Azure Functions) para volver a dar formato a las secuencias de datos y enviarlas a las cuentas de almacenamiento deseadas. 
5. Los datos procesados se almacenan en el almacenamiento de acceso frecuente para las acciones casi en tiempo real, o en el almacenamiento en frío para las mejoras de información adicionales basadas en Machine Learning o el análisis por lotes. 
6. Logic Apps puede usarse para potenciar varios flujos de trabajo empresariales en aplicaciones empresariales de usuario final.

## <a name="details"></a>Detalles
En la siguiente sección se describen las partes de la ingesta de datos de telemetría de la arquitectura conceptual a partir de las etiquetas de identificación por radiofrecuencia (RFID) y las etiquetas de Bluetooth de baja energía (BLE).

## <a name="rfid-tags"></a>Etiquetas RFID
Las etiquetas RFID transmiten datos sobre un elemento a través de ondas de radio. Normalmente, las etiquetas RFID no tienen una batería a menos que se especifique. Las etiquetas reciben energía de las ondas de radio generadas por el lector y transmiten una señal de vuelta hacia el lector RFID.

## <a name="ble-tags"></a>Etiquetas BLE
La baliza de energía emite paquetes de datos a intervalos regulares. Los lectores de BLE o los servicios instalados en los smartphones detectan los datos de la baliza y, a continuación, los transmiten a la nube.

## <a name="rfid--ble-readers"></a>Lectores de BLE y RFID
El lector de RFID convierte las ondas de radio en una forma de datos más utilizable. La información recopilada de las etiquetas se almacena en el servidor perimetral local o se envía a la nube mediante un JSON-RPC 2.0 a través de MQTT.
Los lectores de BLE, también conocidos como puntos de acceso (AP), son similares al lector de RFID. Se usan para detectar señales Bluetooth cercanas y retransmitir su mensaje a una instancia de Azure IoT Edge local o en la nube mediante un JSON-RPC 2.0 a través de MQTT.
Muchos lectores son capaces de leer RFID y señales de baliza, así como de proporcionar una función adicional de sensor relacionada con la temperatura, la humedad, el acelerómetro y el giroscopio.

## <a name="azure-iot-edge-gateway"></a>Puerta de enlace de Azure IoT Edge
El servidor de Azure IoT Edge proporciona un lugar para preprocesar los datos localmente antes de enviarlos a la nube. También podemos implementar cargas de trabajo en la nube de inteligencia artificial, Azure y servicios de terceros o de lógica de negocios a través de contenedores estándar.

## <a name="device-management-with-iot-central"></a>Administración de dispositivos con IoT Central 
Azure IoT Central es una plataforma de desarrollo de soluciones que simplifica la conectividad, configuración y administración de dispositivos IoT. La plataforma reduce significativamente la carga y los costos de la administración, las operaciones y los desarrollos relacionados con los dispositivos IoT. Los clientes y partners pueden crear soluciones empresariales integrales para lograr un bucle de comentarios digital en la administración del inventario.

## <a name="business-insights--actions-via-data-egress"></a>Información y acciones empresariales a través de la salida de datos 
La plataforma de IoT Central proporciona opciones de extensibilidad enriquecidas a través de la exportación continua de datos (CDE) y las API. La información empresarial basada en el procesamiento de datos de telemetría o la telemetría sin procesar se suele exportar a una aplicación de línea de negocio preferida. Esto se puede lograr a través de un webhook, bus de servicio, centro de eventos o almacenamiento de blobs para crear, entrenar e implementar modelos de Machine Learning y mejorar aún más la información.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a implementar una [plantilla de administración inteligente del inventario](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Más información sobre las [plantillas de venta minorista de IoT Central](./overview-iot-central-retail-pnp.md).
* Para obtener más información acerca de IoT Central, consulte [Introducción a IoT Central](../core/overview-iot-central-pnp.md).
