---
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346281"
---
> [!div class="op_single_selector"]
> * [Dispositivo: Servicio de Node.js: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Dispositivo: C#Servicio: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Dispositivo: Servicio de Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Dispositivo: Python Service: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Las aplicaciones back-end pueden usar primitivos de Azure IoT Hub, por ejemplo, [dispositivos gemelos][lnk-devtwin] y [métodos directos][lnk-c2dmethod], para iniciar y supervisar de forma remota las acciones de administración de dispositivos en los dispositivos. Este tutorial muestra cómo una aplicación back-end y un dispositivo pueden trabajar juntos para iniciar y supervisar el reinicio remoto de un dispositivo mediante IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]
Use un método directo para iniciar acciones de administración de dispositivos (por ejemplo, reinicio, restablecimiento de fábrica y actualización de firmware) desde una aplicación back-end en la nube. El dispositivo es responsable de:

* Controlar la solicitud del método enviada desde IoT Hub.
* Iniciar la acción específica del dispositivo correspondiente en el dispositivo.
* Proporcionar actualizaciones de estado mediante las *propiedades notificadas* a IoT Hub.

Puede usar una aplicación de back-end en la nube para ejecutar consultas de dispositivos gemelos para informar sobre el progreso de las acciones de administración de los dispositivos.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
