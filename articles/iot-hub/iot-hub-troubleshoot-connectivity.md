---
title: Diagnóstico y solución de problemas de desconexiones con Azure IoT Hub
description: Información sobre cómo diagnosticar y solucionar errores comunes de conectividad de dispositivos para Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440155"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Detección y solución de problemas de desconexiones con Azure IoT Hub

Los problemas de conectividad de los dispositivos IoT pueden ser difíciles de solucionar porque existen varios puntos de error posibles. La lógica de aplicación de los dispositivos, las redes físicas, los protocolos, el hardware y Azure IoT Hub pueden causar problemas. En este artículo se proporcionan recomendaciones sobre cómo detectar y solucionar problemas de conectividad de dispositivos desde la nube (en vez de hacerlo desde los propios dispositivos).

## <a name="get-alerts-and-error-logs"></a>Recepción de alertas y registros de errores

Use Azure Monitor para recibir alertas y escribir registros cuando se anulen conexiones de dispositivos.

### <a name="turn-on-diagnostic-logs"></a>Activación de los registros de diagnósticos

Para registrar errores y eventos de conexión de dispositivos, active los diagnósticos de IoT Hub.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Vaya a su instancia de IoT Hub.

3. Seleccione **Configuración de diagnóstico**.

4. Seleccione **Activar diagnósticos**.

5. Habilite los registros de **Conexiones** para que se recopilen.

6. Para facilitar el análisis, debe activar **Enviar a Log Analytics** ([consulte los precios](https://azure.microsoft.com/pricing/details/log-analytics/)). Consulte el ejemplo en la sección dedicada a [resolver los errores de conectividad](#resolve-connectivity-errors).

   ![Configuración recomendada](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para obtener más información, consulte [Supervisión del mantenimiento de Azure IoT Hub y diagnóstico de problemas rápidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Configuración de alertas de la métrica de recuento de _dispositivos conectados_

Para obtener alertas cuando los dispositivos se desconectan, configurar alertas en el **(versión preliminar) de los dispositivos conectados** métrica.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Vaya a su instancia de IoT Hub.

3. Seleccione **Alertas**.

4. Seleccione **nueva regla de alerta**.

5. Seleccione **Agregar condición**, a continuación, seleccione "Conectado los dispositivos (versión preliminar)".

6. Fin de configurar los umbrales deseados y las opciones de alertas por indicaciones siguientes.

Para obtener más información, consulte [¿Qué son las alertas clásicas en Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="resolve-connectivity-errors"></a>Resolución de errores de conectividad

Cuando active las alertas y los registros de diagnóstico de los dispositivos conectados, recibirá alertas cuando se produzca algún error. En esta sección se describe cómo resolver problemas comunes cuando reciba una alerta. Los pasos siguientes se supone que ha configurado los registros de Azure Monitor para los registros de diagnóstico.

1. Vaya a su área de trabajo de **Log Analytics** en Azure Portal.

2. Seleccione **Búsqueda de registros**.

3. Para aislar los registros de errores de conectividad de IoT Hub, escriba esta consulta y pulse **Ejecutar**:

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Si hay resultados, busque `OperationName`, `ResultType` (código de error) y `ResultDescription` (mensaje de error) para obtener más detalles sobre el error.

   ![Ejemplo de registro de errores](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Use esta tabla para comprender y resolver errores comunes.

    | Error | Causa principal | Resolución |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Se cerró la conexión con el dispositivo, pero IoT Hub no ha identificado el motivo. Entre las causas comunes se encuentran el tiempo de expiración de MQTT/AMQP y la pérdida de conectividad de Internet. | Asegúrese de que el dispositivo puede conectarse a IoT Hub [probando la conexión](tutorial-connectivity.md). Si la conexión es correcta, pero el dispositivo se desconecta intermitentemente, asegúrese de implementar la lógica de dispositivo de conexión persistente adecuada para el protocolo elegido (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub no pudo autenticar la conexión. | Asegúrese de que la firma de acceso compartido u otro token de seguridad que usa no ha expirado. Los [SDK de IoT de Azure](iot-hub-devguide-sdks.md) generan automáticamente tokens sin requerir ninguna configuración especial. |
    | 409002 LinkCreationConflict | Un dispositivo tiene más de una conexión. Cuando un dispositivo recibe una nueva solicitud de conexión, IoT Hub cierra la anterior con este error. | En el caso más común, un dispositivo detecta una desconexión e intenta restablecerla, pero IoT Hub todavía considera que el dispositivo está conectado. IoT Hub cierra la conexión anterior y registra el error. Este error normalmente aparece como un efecto secundario de un problema transitorio diferente, así que busque otros errores en los registros para solucionar el problema. En caso contrario, asegúrese de emitir una nueva solicitud de conexión solo si la conexión se anula. |
    | 500001 ServerError | IoT Hub genera un error en el servidor. Probablemente, el problema es transitorio. Aunque el equipo de IoT Hub trabaja duro para mantener [el SLA (Acuerdo de Nivel de Servicio)](https://azure.microsoft.com/support/legal/sla/iot-hub/), hay pequeños subconjuntos de nodos de IoT Hub que, en ocasiones, pueden experimentar errores transitorios. Cuando el dispositivo trata de conectarse a un nodo que está teniendo problemas, recibirá este error. | Para mitigar los errores transitorios, ejecute un reintento desde el dispositivo. Para [administrar automáticamente los reintentos](iot-hub-reliability-features-in-sdks.md#connection-and-retry), asegúrese de usar la versión más reciente de los [SDK de Azure IoT](iot-hub-devguide-sdks.md).<br><br>Consulte [Control de errores transitorios](/azure/architecture/best-practices/transient-faults) para ver procedimientos recomendados sobre reintentos y cómo controlar errores transitorios.  <br><br>Si el problema persiste después de los reintentos, compruebe [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) y [Estado de Azure](https://azure.microsoft.com/status/history/) para ver si IoT Hub tiene un problema conocido. Si no hay ningún problema conocido y el problema continúa, [póngase en contacto con el equipo de soporte técnico](https://azure.microsoft.com/support/options/) para que lo investigue. |
    | 500008 GenericTimeout | IoT Hub no pudo completar la solicitud de conexión antes de agotar el tiempo de expiración. Igual que sucede con el error 500001 ServerError, este error probablemente es transitorio. | Siga los pasos para la solución de problemas de 500001 ServerError para encontrar la causa principal del error y resolverlo.|

## <a name="other-steps-to-try"></a>Otros pasos para probar

Si los pasos anteriores no le son de utilidad, puede intentar lo siguiente:

* Si tiene acceso a los dispositivos problemáticos, tanto físicamente como en remoto (por ejemplo, SSH), siga la [Guía de solución de problemas de dispositivos](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar solucionando los problemas.

* Compruebe que los dispositivos están **habilitados** en Azure Portal > su instancia de IoT Hub > Dispositivos IoT.

* Obtenga ayuda del [foro de Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o [el soporte técnico de Azure](https://azure.microsoft.com/support/options/).

Con el fin de mejorar la documentación para los usuarios, deje un comentario a continuación si esta guía no le ha ayudado.

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de cómo resolver problemas transitorios, consulte [Control de errores transitorios](/azure/architecture/best-practices/transient-faults).

* Para más información sobre el SDK de Azure IoT y la administración de reintentos, consulte [Administración de la conectividad y mensajería confiable mediante los SDK de dispositivo de Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
