---
title: 'Supervisión y registro: Azure'
description: En este artículo se proporciona información general sobre la supervisión y el registro de Live Video Analytics on IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 8ae455a4157cd649f610620e486323ac2c0a5744
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401056"
---
# <a name="monitoring-and-logging"></a>Supervisión y registro

En este artículo se describe cómo se pueden recibir eventos del módulo Live Video Analytics on IoT Edge para la supervisión remota. 

También encontrará información sobre cómo se pueden controlar los registros que el módulo genera.

## <a name="taxonomy-of-events"></a>Taxonomía de eventos

Live Video Analytics on IoT Edge emite eventos o datos de telemetría según la siguiente taxonomía.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Taxonomía de eventos":::

* Operativo: eventos que se generan como parte de las acciones realizadas por un usuario o durante la ejecución de un [gráfico multimedia](media-graph-concept.md).
   
   * Volumen: se espera que sea bajo (unas cuantas veces por minuto, o incluso una tasa inferior).
   * Ejemplos:

      Grabación iniciada (abajo), grabación detenida.
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnóstico: eventos que ayudan a diagnosticar problemas o incidencias de rendimiento.

   * Volumen: puede ser elevado (varias veces por minuto).
   * Ejemplos:
   
      Información de [Session Description Protocol](https://en.wikipedia.org/wiki/Session_Description_Protocol) de RTSP (abajo) o saltos en la fuente de vídeo entrante.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Análisis: eventos que se generan como parte del análisis de vídeo.

   * Volumen: puede ser elevado (varias veces por minuto o más a menudo).
   * Ejemplos:
      
      Movimiento detectado (abajo), resultado de la inferencia.

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Los eventos emitidos por el módulo se envían al [centro de IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub) y, desde allí, se pueden enrutar a otros destinos. 

### <a name="timestamps-in-analytic-events"></a>Marcas de tiempo de los eventos de análisis

Como se indicó anteriormente, los eventos generados como parte del análisis de vídeo tienen una marca de tiempo asociada. Si [grabó el vídeo en directo](video-recording-concept.md) como parte de la topología de grafos, esta marca de tiempo le ayuda a encontrar dónde se produjo en el vídeo grabado ese evento determinado. A continuación, se muestran las instrucciones para asignar la marca de tiempo de un evento de análisis a la escala de tiempo del vídeo grabado en un [recurso de Azure Media Services](terminology.md#asset).

En primer lugar, extraiga el valor de `eventTime`. Use este valor en un [filtro de intervalo de tiempo](playback-recordings-how-to.md#time-range-filters) para recuperar una parte adecuada de la grabación. Por ejemplo, puede que quiera realizar una captura de vídeo que comience 30 segundos antes de `eventTime` y que termine 30 segundos después. En el ejemplo anterior, donde `eventTime` es 2020-05-12T23:33:09.381 Z, una solicitud de un manifiesto HLS para la ventana de +/-30 segundos sería similar a la siguiente:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

La dirección URL anterior devolvería la conocida como [lista de reproducción maestra](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming), que contiene las direcciones URL de las listas de reproducción de elementos multimedia. La lista de reproducción de elementos multimedia contiene entradas como la siguiente:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
En ella se informa que hay disponible un fragmento de vídeo que comienza en un valor de marca de tiempo de `143039375031270`. El valor de `timestamp` del evento de análisis usa la misma escala de tiempo que la lista de reproducción de elementos multimedia, y se puede usar para identificar el fragmento de vídeo pertinente y buscar el fotograma correcto.

Para más información, puede leer uno de los muchos [artículos](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) sobre la búsqueda precisa de fotogramas en HLS.

## <a name="controlling-events"></a>Control de eventos

Se pueden usar las siguientes propiedades de módulo gemelo (como se documenta en [Esquema JSON de módulo gemelo](module-twin-configuration-schema.md)) para controlar los eventos operativos y de diagnóstico publicados por el módulo Live Video Analytics on IoT Edge.

`diagnosticsEventsOutputName` : incluya y proporcione cualquier valor en esta propiedad para obtener eventos de diagnóstico del módulo. Omítalo o déjelo en blanco si prefiere que el módulo deje de publicar eventos de diagnóstico.
   
`operationalEventsOutputName` : incluya y proporcione cualquier valor en esta propiedad para obtener eventos operativos del módulo. Omítalo o déjelo en blanco si prefiere que el módulo deje de publicar eventos operativos.
   
Los eventos de análisis los generan nodos como, por ejemplo, el procesador de detección de movimiento o el procesador de extensión HTTP, y el receptor de IoT Hub se usa para enviarlos al centro de IoT Edge. 

Puede controlar el [enrutamiento de todos los eventos anteriores](../../iot-edge/module-composition.md#declare-routes) a través de una propiedad deseada del módulo gemelo $edgeHub (en el manifiesto de implementación):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Arriba, lvaEdge es el nombre del módulo Live Video Analytics on IoT Edge, y la regla de enrutamiento sigue el esquema definido en [Declaración de rutas](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Para garantizar que los eventos de análisis llegan al centro de IoT Edge, debe haber un nodo receptor de IoT Hub en sentido descendente con respecto a cualquier nodo procesador de detección de movimiento y/o nodo procesador de extensión HTTP.

## <a name="event-schema"></a>Esquema del evento

Los eventos se originan en el dispositivo Edge y se pueden consumir tanto en Edge como en la nube. Los eventos generados por Live Video Analytics on IoT Edge siguen el [patrón de mensajería de streaming](../../iot-hub/iot-hub-devguide-messages-construct.md) establecido por Azure IoT Hub, con propiedades de sistema, propiedades de la aplicación y un cuerpo.

### <a name="summary"></a>Resumen

Cada evento, cuando se observa a través de IoT Hub, tendrá un conjunto de propiedades comunes, como se describe a continuación.

|Propiedad   |Tipo de propiedad| Tipo de datos   |Descripción|
|---|---|---|---|
|message-id |sistema |guid|  Identificador de evento único|
|topic| applicationProperty |string|    Ruta de acceso de Azure Resource Manager de la cuenta de Media Services|
|subject|   applicationProperty |string|    Subruta de acceso a la entidad que emite el evento|
|eventTime| applicationProperty|    string| Hora a la que se generó el evento|
|eventType| applicationProperty |string|    Identificador de tipo de evento (ver más abajo)|
|body|body  |object|    Datos de evento concretos|
|dataVersion    |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>Propiedades

#### <a name="message-id"></a>message-id

Es el identificador único global (GUID) del evento.

#### <a name="topic"></a>topic

Representa la cuenta de Azure Media Services asociada al gráfico.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Es la entidad que emite el evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

La propiedad subject permite asignar eventos genéricos al módulo que lo ha generado. Por ejemplo, si el nombre de usuario o la contraseña de RTSP no son válidos, el evento generado sería `Microsoft.Media.Graph.Diagnostics.ProtocolError` en el nodo `/graphInstances/myGraph/sources/myRtspSource`.

#### <a name="event-types"></a>Tipos de eventos

Los tipos de evento se asignan a un espacio de nombres de acuerdo con el siguiente esquema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Clases de eventos

|Class Name (Nombre de clase)|Descripción|
|---|---|
|Análisis  |Eventos generados como parte del análisis de contenido|
|Diagnóstico    |Eventos que ayudan a diagnosticar problemas y el rendimiento|
|Operativos    |Eventos generados como parte de las operaciones de recursos|

Los tipos de evento son específicos de cada clase de evento.

Ejemplos:

* Microsoft.Media.Graph.Analytics.Inference
* Microsoft.Media.Graph.Diagnostics.AuthorizationError
* Microsoft.Media.Graph.Operational.GraphInstanceStarted

### <a name="event-time"></a>Hora del evento

La hora del evento se describe en la cadena ISO8601 y es la hora a la que dicho evento se produjo.

### <a name="azure-monitor-collection-using-telegraf"></a>Recopilación de Azure Monitor con Telegraf

Estas métricas se notificarán en Live Video Analytics en el módulo de IoT Edge:  

|Nombre de la métrica|Tipo|Etiqueta|Descripción|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Indicador|iothub, edge_device, module_name, graph_topology|Número total de grafos activos por topología.|
|lva_received_bytes_total|Contador|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Número total de bytes recibidos por un nodo. Solo se admite para orígenes RTSP|
|lva_data_dropped_total|Contador|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Contador de los datos anulados (eventos, medios, etc.)|

> [!NOTE]
> Un [punto de conexión de Prometheus](https://prometheus.io/docs/practices/naming/) se expone en el puerto **9600** del contenedor. Si denomina "IvaEdge" a las instancias de Live Video Analytics en el módulo de IoT Edge, podrían acceder a las métricas enviando una solicitud GET a http://lvaEdge:9600/metrics.   

Siga estos pasos para habilitar la recopilación de métricas de Live Video Analytics en el módulo de IoT Edge:

1. Cree una carpeta en la máquina de desarrollo y vaya a esa carpeta.

1. Una vez en ella, cree un archivo `telegraf.toml` con el siguiente contenido.
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > No olvide reemplazar las variables (marcadas con `{ }`) en el archivo de contenido.

1. En esa carpeta, cree un archivo `.dockerfile` con el siguiente contenido.
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Ahora, con el comando de la CLI de Docker, **cree el archivo de Docker** y publique la imagen en Azure Container Registry.
    1. Aprenda a [insertar y extraer imágenes de Docker con Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).  Puede encontrar más información sobre Azure Container Registry (ACR) [aquí](https://docs.microsoft.com/azure/container-registry/).


1. Una vez completada la inserción en ACR, en el archivo del manifiesto de implementación, agregue el siguiente nodo:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{ACR_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > No olvide reemplazar las variables (marcadas con `{ }`) en el archivo de contenido.


1. **Autenticación**
    1. Azure Monitor se puede [autenticar mediante una entidad de servicio](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        1. El complemento Telegraf de Azure Monitor expone [varios métodos de autenticación](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). Se deben establecer las siguientes variables de entorno para usar la autenticación mediante entidad de servicio.  
            •   AZURE_TENANT_ID: especifica el inquilino en el que se va a autenticar.  
            •   AZURE_CLIENT_ID: especifica el identificador de cliente de la aplicación que se va a usar.  
            •   AZURE_CLIENT_SECRET: especifica el secreto de la aplicación que se va a usar.  
    >[!TIP]
    > A la entidad de servicio se le puede otorgar el rol "**Publicador de métricas de supervisión**".

1. Una vez implementados los módulos, las métricas aparecerán en Azure Monitor bajo un solo espacio de nombres con los nombres de las métricas que coincidan con los emitidos por Prometheus. 
    1. En este caso, en Azure Portal, vaya a IoT Hub y haga clic en el vínculo "**Métricas**" en el panel de navegación izquierdo. Allí debería ver las métricas.
## <a name="logging"></a>Registro

Al igual que sucede con otros módulos de IoT Edge, también se pueden [examinar los registros de contenedor](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) en el dispositivo Edge. La información que se escribe en estos registros se puede controlar por medio de las [siguientes propiedades de módulo gemelo](module-twin-configuration-schema.md):

* logLevel

   * Los valores permitidos son Verbose, Information, Warning, Error y None.
   * El valor predeterminado es Information: los registros contendrán mensajes de error, de advertencia y de información.
   * Si el valor se establece en Warning, los registros contendrán mensajes de error y de advertencia.
   * Si el valor se establece en Error, los registros solo contendrán mensajes de error.
   * Si el valor se establece en None, no se generará ningún registro (no se recomienda).
   * La opción Verbose solo debe usarse en caso de que sea necesario compartir registros con Soporte técnico de Azure para diagnosticar un problema.
* logCategories

   * Lista separada por comas de uno o varios de los siguientes elementos: Application, Events, MediaPipeline.
   * Valor predeterminado: Application, Events.
   * Application: se trata de información de alto nivel del módulo, como mensajes de inicio del módulo, errores de entorno y llamadas a métodos directos.
   * Events: se trata de todos los eventos descritos anteriormente en este artículo.
   * MediaPipeline: se trata de una serie de registros de bajo nivel que pueden arrojar más información al solucionar problemas, como dificultades para establecer una conexión con una cámara compatible con RTSP.
   
### <a name="generating-debug-logs"></a>Generación de registros de depuración

Algunas veces, puede que tenga que generar registros más detallados que los descritos arriba para ayudar a Soporte técnico de Azure a resolver un problema. Para ello, es necesario realizar dos pasos.

En primer lugar, [vincular el almacenamiento del módulo al almacenamiento del dispositivo](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) a través de createOptions. Si examina una [plantilla de manifiesto de implementación](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) de los inicios rápidos, verá lo siguiente:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

El código anterior permite al módulo Edge escribir registros en la ruta de acceso de almacenamiento (del dispositivo) "/var/local/mediaservices/". Si agrega la siguiente propiedad deseada al módulo:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

El módulo escribirá registros de depuración en formato binario en la ruta de acceso de almacenamiento (del dispositivo) /var/local/mediaservices/debuglogs/, que puede compartir con Soporte técnico de Azure.

## <a name="faq"></a>Preguntas más frecuentes

[Preguntas más frecuentes](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Pasos siguientes

[Grabación de vídeo continua](continuous-video-recording-tutorial.md)
