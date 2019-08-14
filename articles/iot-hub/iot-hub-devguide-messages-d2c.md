---
title: Enrutamiento de mensajes de Azure IoT Hub | Microsoft Docs
description: 'Guía del desarrollador: cómo usar el enrutamiento de mensajes para enviar mensajes del dispositivo a la nube. Incluye información sobre el envío de datos de telemetría y datos que no son de telemetría.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d2d4d39cc7b330794094745851856365ef54b42f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828186"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

El enrutamiento de mensajes le permite enviar mensajes desde los dispositivos a los servicios en la nube de forma automatizada, escalable y confiable. El enrutamiento de mensajes se puede usar para: 

* **Enviar mensajes de telemetría del dispositivo y eventos**, es decir, eventos del ciclo de vida del dispositivo y eventos de cambio de dispositivo gemelo al punto de conexión integrado y a los puntos de conexión personalizados. Obtenga información sobre los [puntos de conexión de enrutamiento](#routing-endpoints).

* **Filtrar los datos antes de enrutarlos a los diferentes puntos de conexión** mediante la aplicación de consultas enriquecidas. El enrutamiento de mensajes le permite realizar consultas sobre las propiedades de los mensajes y el cuerpo del mensaje, así como las etiquetas del dispositivo gemelo y las propiedades del dispositivo gemelo. Más información sobre el uso de [consultas en el enrutamiento de mensajes](iot-hub-devguide-routing-query-syntax.md).

IoT Hub necesita acceso de escritura a estos puntos de conexión de servicio para que el enrutamiento de mensajes funcione. Si configura los puntos de conexión a través de Azure Portal, se agregan los permisos necesarios automáticamente. Asegúrese de configurar los servicios para admitir el rendimiento esperado. Al configurar la solución de IoT por primera vez, es posible que deba supervisar los puntos de conexión adicionales y realizar los ajustes necesarios para la carga real.

IoT Hub define un [formato común](iot-hub-devguide-messages-construct.md) para todos los mensajes del dispositivo a la nube a fin de generar interoperabilidad entre protocolos. Si un mensaje coincide con varias rutas que señalan al mismo punto de conexión, IoT Hub entrega el mensaje a ese punto de conexión solo una vez. Por lo tanto, no es necesario configurar la desduplicación en la cola o el tema de Service Bus. En las colas con particiones, la afinidad de partición garantiza el orden de los mensajes. Use este tutorial para aprender a [configurar el enrutamiento de mensajes](tutorial-routing.md).

## <a name="routing-endpoints"></a>Puntos de conexión de enrutamiento

Un centro de IoT tiene un punto de conexión integrado predeterminado (**mensajes y eventos**) que es compatible con Event Hubs. Puede crear [puntos de conexión personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) a los que enrutar mensajes vinculando otros servicios de la suscripción al centro de IoT. IoT Hub admite actualmente los siguientes servicios como puntos de conexión personalizados:

### <a name="built-in-endpoint"></a>Punto de conexión integrado

Puede usar la [integración y los SDK de Event Hubs](iot-hub-devguide-messages-read-builtin.md) estándar para recibir mensajes del dispositivo a la nube desde el punto de conexión integrado (**mensajes y eventos**). Una vez que se crea una ruta, los datos dejan de fluir al punto de conexión integrado, a menos que se cree una ruta a ese punto de conexión.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub admite la escritura de datos en Azure Blob Storage con los formatos [Apache Avro](https://avro.apache.org/) y JSON. La funcionalidad de codificar en formato JSON está disponible en general en todas las regiones donde IoT Hub está disponible. El valor predeterminado es AVRO. El formato de codificación solo se puede establecer cuando se configura el punto de conexión de Blob Storage. El formato no se puede editar para un punto de conexión existente. Cuando se usa la codificación JSON, debe establecer contentType en JSON y contentEncoding en UTF-8 en las [propiedades del sistema](iot-hub-devguide-routing-query-syntax.md#system-properties) del mensaje. Si no se establece, IoT Hub escribirá los mensajes en formato codificado de base 64. Puede seleccionar el formato de codificación mediante la API REST Crear o actualizar de IoT Hub, específicamente [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, la [CLI de Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). En el siguiente diagrama se muestra cómo seleccionar el formato de codificación en Azure Portal.

![Codificación de puntos de conexión de Blob Storage](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub también admite el enrutamiento de mensajes a cuentas de ADLS Gen2, que son cuentas de almacenamiento habilitadas para [espacios de nombres jerárquicos](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) que se basan en Blob Storage. Esta funcionalidad está en versión preliminar pública y está disponible para las nuevas cuentas de ADLS Gen2 en las regiones Oeste de EE. UU. 2 y Centro-oeste de EE. UU. Esta funcionalidad se implementará pronto en todas las regiones de la nube.

IoT Hub agrupa los mensajes por lotes y escribe los datos en un blob cuando el lote llega a cierto tamaño o después de transcurrir cierta cantidad de tiempo. IoT Hub asume como valor predeterminado la convención de nomenclatura de archivos siguiente:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Puede usar cualquier convención de nomenclatura de archivos, aunque debe usar todos los tokens de la lista. IoT Hub escribirá en un blob vacío si no hay datos que escribir.

Al enrutar a Blob Storage, se recomienda dar de alta los blobs e iterar sobre ellos para garantizar que se leen todos los contenedores sin pasar por alto ninguna partición. El intervalo de partición podría cambiar durante una [conmutación por error iniciada por Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) o una [conmutación por error manual](iot-hub-ha-dr.md#manual-failover-preview) de IoT Hub. Puede usar la [API Mostrar blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para enumerar la lista de blobs. Vea el siguiente ejemplo como guía.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Colas de Service Bus y temas de Service Bus

Las colas y los temas de Service Bus usados como puntos de conexión de IoT Hub no deben tener habilitadas las opciones **Sesiones** o **Detección de duplicados**. Si cualquiera de estas opciones está habilitada, el punto de conexión aparece como **Inaccesible** en Azure Portal.

### <a name="event-hubs"></a>Event Hubs

Aparte del punto de conexión compatible con Event Hubs integrado, también puede enrutar los datos a puntos de conexión personalizados de tipo Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Lectura de datos que se han enrutado

Para configurar una ruta, siga este [tutorial](tutorial-routing.md).

Use los siguientes tutoriales para obtener información sobre cómo leer un mensaje de un punto de conexión.

* Lectura desde el [punto de conexión integrado](quickstart-send-telemetry-node.md)

* Lectura desde [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Lectura desde [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lectura desde [colas de Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lectura desde [temas de Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Ruta de reserva

La ruta de reserva envía todos los mensajes que no cumplen las condiciones de la consulta en cualquiera de las rutas existentes al punto de conexión de Event Hubs integrado (**mensajes y eventos**), que es compatible con [Event Hubs](/azure/event-hubs/). Si el enrutamiento de mensajes está activado, puede habilitar la funcionalidad de ruta de reserva. Una vez que se crea una ruta, los datos dejan de fluir al punto de conexión integrado, a menos que se cree una ruta a ese punto de conexión. Si no hay ninguna ruta al punto de conexión integrado y está habilitada una ruta de reserva, solo se enviarán al punto de conexión integrado los mensajes que no coinciden con las condiciones de la consulta sobre rutas. Además, si se eliminan todas las rutas existentes, se debe habilitar la ruta de reserva para recibir todos los datos en el punto de conexión integrado.

Puede habilitar o deshabilitar la ruta de reserva en Azure Portal -> hoja Enrutamiento de mensajes. También puede usar Azure Resource Manager para [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para usar un punto de conexión personalizado para la ruta de reserva.

## <a name="non-telemetry-events"></a>Eventos que no son de telemetría

Además de los datos de telemetría del dispositivo, el enrutamiento de mensajes permite enviar eventos de cambio de dispositivo gemelo y eventos del ciclo de vida del dispositivo. Por ejemplo, si se crea una ruta con el origen de datos establecido en **eventos de cambio de dispositivo gemelo**, IoT Hub envía mensajes al punto de conexión que contienen el cambio en el dispositivo gemelo. De forma similar, si se crea una ruta con el origen de datos establecido en **eventos del ciclo de vida del dispositivo**, IoT Hub enviará un mensaje que indica si el dispositivo se ha eliminado o se ha creado. 

[IoT Hub también se integra con Azure Event Grid](iot-hub-event-grid.md) para publicar los eventos de dispositivo con el fin de admitir integraciones en tiempo real y la automatización de flujos de trabajo basados en estos eventos. Consulte las [diferencias principales entre el enrutamiento de mensajes y Event Grid](iot-hub-event-grid-routing-comparison.md) para obtener información sobre la mejor opción para su escenario.

## <a name="testing-routes"></a>Prueba de las rutas

Al crear una ruta nueva o al modificar una ruta existente, debe probar la consulta de ruta con un mensaje de ejemplo. Puede probar rutas individuales o probar todas las rutas a la vez y no se enruta ningún mensaje a los puntos de conexión durante la prueba. Puede usar Azure Portal, Azure Resource Manager, Azure PowerShell y la CLI de Azure para realizar las pruebas. Los resultados ayudan a identificar si el mensaje de ejemplo coincide con la consulta, el mensaje no coincide con la consulta o no se pudo ejecutar la prueba porque la sintaxis del mensaje de ejemplo o de la consulta son incorrectas. Para más información, consulte [Prueba de una ruta](/rest/api/iothub/iothubresource/testroute) y [Prueba de todas las rutas](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latencia

Al enrutar mensajes de telemetría del dispositivo a la nube mediante puntos de conexión integrados, aumenta ligeramente la latencia de un extremo a otro tras las creación de la primera ruta.

En la mayoría de los casos, el aumento medio de la latencia es inferior a 500 ms. Puede supervisar la latencia con **Enrutamiento: latencia de mensaje para mensajes y eventos** o con la métrica de IoT Hub **d2c.endpoints.latency.builtIn.events**. La creación o eliminación de una ruta tras la primera no afecta a la latencia de un extremo a otro.

## <a name="monitoring-and-troubleshooting"></a>Supervisión y solución de problemas

IoT Hub proporciona varias métricas relacionadas con el enrutamiento y los puntos de conexión para ofrecerle una visión general del mantenimiento del centro y los mensajes enviados. Puede combinar información de varias métricas para identificar la causa principal de los problemas. Por ejemplo, use la métrica **Enrutamiento: mensajes de telemetría eliminados** o **d2c.telemetry.egress.dropped** para identificar el número de mensajes que se eliminaron por no coincidir con las consultas en ninguna de las rutas y la ruta de reserva estaba deshabilitada. [Métricas de IoT Hub](iot-hub-metrics.md) enumera todas las métricas que están habilitadas de forma predeterminada para el centro de IoT.

Puede usar la API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obtener el [estado de mantenimiento](iot-hub-devguide-endpoints.md#custom-endpoints) de los puntos de conexión. Se recomienda usar las [métricas de IoT Hub](iot-hub-metrics.md) relativas a la latencia de mensajes de enrutamiento para identificar y depurar errores cuando el estado del punto de conexión sea inactivo o incorrecto. Por ejemplo, para el tipo de punto de conexión Event Hubs, puede supervisar **d2c.endpoints.latency.eventHubs**. El estado del punto de conexión incorrecto se actualizará a correcto cuando IoT Hub haya establecido finalmente un estado de mantenimiento coherente.

Mediante los registros de diagnóstico de **rutas** de la [configuración de diagnóstico](../iot-hub/iot-hub-monitor-resource-health.md) de Azure Monitor, puede realizar un seguimiento de los errores producidos durante la evaluación de una consulta de enrutamiento y del mantenimiento del punto de conexión según lo percibido por IoT Hub, por ejemplo, cuando un punto de conexión está inactivo. Estos registros de diagnóstico se pueden enviar a los registros de Azure Monitor, Event Hubs o Azure Storage para su procesamiento personalizado.

## <a name="next-steps"></a>Pasos siguientes

* Para aprender a crear rutas de mensajes, consulte [Procesamiento de mensajes del dispositivo a la nube de IoT Hub mediante rutas](tutorial-routing.md).

* [Envío de mensajes de dispositivo a nube](quickstart-send-telemetry-node.md).

* Para obtener información sobre los SDK que puede utilizar para enviar mensajes del dispositivo a la nube, consulte [SDK de Azure IoT](iot-hub-devguide-sdks.md).
