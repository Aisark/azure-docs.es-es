---
title: Ingesta de datos de telemetría históricos
description: En este artículo se describe cómo ingerir datos de telemetría históricos.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 11dcf5dc0f05e51f3f427b09745cb581cc0d3780
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513939"
---
# <a name="ingest-historical-telemetry-data"></a>Ingesta de datos de telemetría históricos

En este artículo se describe cómo ingerir datos de históricos del sensor en Azure FarmBeats.

Uno de los escenarios comunes de FarmBeats es la ingesta de datos históricos de Internet de las cosas (IoT) en recursos como dispositivos y sensores. Se crean metadatos para dispositivos y sensores y, luego, se ingieren los datos históricos en FarmBeats en formato canónico.

## <a name="before-you-begin"></a>Antes de empezar

Antes de continuar con este artículo, asegúrese de que ha instalado FarmBeats y ha recopilado datos históricos de los dispositivos IoT.
También tendrá que habilitar el acceso de los asociados como se indica en los pasos siguientes.

## <a name="enable-partner-access"></a>Habilitación del acceso de asociados

Deberá habilitar la integración de asociados en la instancia de Azure FarmBeats. En este paso se crea un cliente que tendrá acceso a su instancia de Azure FarmBeats como asociado de dispositivo, y se proporcionan los siguientes valores que son necesarios en los pasos posteriores:

- Punto de conexión de API: esta es la dirección URL del centro de datos, por ejemplo, https://\<centro de datos>.azurewebsites.net.
- Id. de inquilino
- Id. de cliente
- Secreto del cliente
- Cadena de conexión de EventHub

Siga estos pasos.

>[!NOTE]
> Para realizar los pasos siguientes, debe ser administrador.

1. Descargue el [archivo zip](https://aka.ms/farmbeatspartnerscriptv2) y extráigalo en la unidad local. Habrá un archivo dentro del archivo ZIP.
2. Inicie sesión en https://portal.azure.com/ y vaya a Azure Active Directory> Registros de aplicaciones.

3. Haga clic en el registro de la aplicación que se ha creado como parte de la implementación de FarmBeats. Tendrá el mismo nombre que el centro de datos de FarmBeats.

4. Haga clic en "Exponer una API" -> "Agregar una aplicación cliente", escriba **04b07795-8ddb-461a-bbee-02f9e1bf7b46** y active "Authorize Scope" (Autorizar ámbito). De esta forma, se proporciona acceso a la CLI de Azure (Cloud Shell) para realizar los pasos siguientes.

5. Abra Cloud Shell. Esta opción está disponible en la barra de herramientas de la esquina superior derecha de Azure Portal.

    ![Barra de herramientas de Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Asegúrese de que el entorno esté establecido en **PowerShell**. De forma predeterminada, se establece en Bash.

    ![Valor de la barra de herramientas de PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Cargue el archivo del paso 1 en la instancia de Cloud Shell.

    ![Botón Cargar de la barra de herramientas](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Vaya al directorio donde se ha cargado el archivo. De forma predeterminada, los archivos se cargan en el directorio particular bajo el nombre de usuario.

9. Ejecute el siguiente script. El script solicita el identificador de inquilino, que se puede obtener en la página Azure Active Directory -> Información general.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Siga las instrucciones en pantalla para capturar los valores del **punto de conexión de API**, el **identificador de inquilino**, el **identificador de cliente**, el **secreto de cliente** y la **cadena de conexión de EventHub**.
## <a name="create-device-or-sensor-metadata"></a>Creación de metadatos de dispositivo o sensor

 Ahora que tiene las credenciales necesarias, puede definir el dispositivo y los sensores. Para ello, cree los metadatos mediante una llamada a las API de FarmBeats. Tenga en cuenta que deberá llamar a las API como la aplicación cliente que creó en la sección anterior

 El centro de datos de FarmBeats tiene las siguientes API que permiten la creación y administración de los metadatos de dispositivos o sensores.

- /**DeviceModel**: DeviceModel se corresponde con los metadatos del dispositivo, como el fabricante o el tipo de dispositivo, que es puerta de enlace o nodo.
- /**Device**: Device se corresponde con un dispositivo físico presente en la granja.
- /**SensorModel**: SensorModel se corresponde con los metadatos del sensor, por ejemplo, el fabricante, el tipo de sensor (analógico o digital) o la medida de sensor (como temperatura ambiente y presión).
- /**Sensor**: El sensor corresponde a un sensor físico que registra valores. Un sensor normalmente se conecta a un dispositivo con un identificador de dispositivo.  


|        DeviceModel   |  Sugerencias   |
| ------- | -------             |
|     Type (nodo, puerta de enlace)        |          Tipo de dispositivo: nodo o puerta de enlace      |
|          Fabricante            |         Nombre del fabricante    |
|  ProductCode                    |  Código de producto del dispositivo o nombre o número de modelo. Por ejemplo, EnviroMonitor#6800.  |
|            Puertos          |     Nombre y tipo de puerto, que es digital o analógico.
|     Nombre                 |  Nombre para identificar el recurso. Por ejemplo, nombre del modelo o nombre del producto.
      Descripción     | Proporciona una descripción significativa del modelo.
|    Propiedades          |    Propiedades adicionales del fabricante.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     Identificador del modelo de dispositivo asociado.  |
|  HardwareId          | Identificador único del dispositivo, como la dirección MAC.
|  ReportingInterval        |   Intervalo de informes en segundos.
|  Location            |  Latitud (de -90 a +90), longitud (de -180 a 180) y elevación (en metros) del dispositivo.   
|ParentDeviceId       |    Identificador del dispositivo primario al que está conectado este dispositivo. Por ejemplo, un nodo conectado a una puerta de enlace. Un nodo tiene parentDeviceId como puerta de enlace.  |
|    Nombre            | Nombre para identificar el recurso. Los asociados de dispositivo deben enviar un nombre que sea coherente con el nombre del dispositivo del asociado. Si el nombre del dispositivo del asociado está definido por el usuario, este mismo nombre se debe propagar a FarmBeats.|
|     Descripción       |      Proporciona una descripción significativa. |
|     Propiedades    |  Propiedades adicionales del fabricante.
|     **SensorModel**        |          |
|       Type (analog, digital)          |      Tipo de sensor, ya sea analógico o digital.       |
|          Fabricante            |       Fabricante del sensor.     |
|     ProductCode| Código de producto o nombre o número de modelo. Por ejemplo, RS-CO2-N01. |
|       SensorMeasures > Name       | Nombre de la medida del sensor. Solo se admiten minúsculas. Para medidas desde diferentes profundidades, especifique la profundidad. Por ejemplo, soil_moisture_15cm. Este nombre debe ser coherente con los datos de telemetría.  |
|          SensorMeasures > DataType       |Tipo de datos de telemetría. Actualmente se admite doble.|
|    SensorMeasures > Type    |Tipo de medida de los datos de telemetría del sensor. Los tipos definidos por el sistema son AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration y PAR. Para agregar más, consulte /ExtendedType API.|
|        SensorMeasures > Unit              | Unidad de datos de telemetría del sensor. Las unidades definidas por el sistema son NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para agregar más, consulte /ExtendedType API.|
|    SensorMeasures > AggregationType    |  Los valores pueden ser none, average, maximum, minimum o StandardDeviation.  |
|          Nombre            | Nombre para identificar un recurso. Por ejemplo, nombre del modelo o nombre del producto.  |
|    Descripción        | Proporciona una descripción significativa del modelo.  |
|   Propiedades       |  Propiedades adicionales del fabricante.  |
|    **Sensor**      |          |
| HardwareId          |   Identificador único del sensor establecido por el fabricante. |
|  SensorModelId     |    Identificador del modelo de sensor asociado.   |
| Location          |  Latitud (de -90 a +90), longitud (de -180 a 180) y elevación (en metros) del sensor.|
|   Port > Name        |  Nombre y tipo de puerto al que está conectado el sensor en el dispositivo. Debe ser el mismo nombre que el definido en el modelo de dispositivo. |
|    DeviceID  |    Identificador del dispositivo al que está conectado el sensor.     |
| Nombre            |   Nombre para identificar el recurso. Por ejemplo, el nombre del sensor o del producto, y el número de modelo o el código del producto.|
|    Descripción      | Proporciona una descripción significativa. |
|    Propiedades        |Propiedades adicionales del fabricante. |

Para más información sobre los objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Solicitud de API para crear metadatos

Para realizar una solicitud de API, se combina el método HTTP (POST), la dirección URL al servicio de API y el URI a un recurso para consultar y enviar datos para crear o eliminar una solicitud. A continuación, se agregan uno o más encabezados de solicitud HTTP. La dirección URL al servicio de API es el punto de conexión de API, es decir, la dirección URL del centro de datos (https://\<sucentrodedatos>.azurewebsites.net).  

### <a name="authentication"></a>Authentication

El centro de datos de FarmBeats usa la autenticación del portador, que necesita las siguientes credenciales que se generaron en la sección anterior:

- Id. de cliente
- Secreto del cliente
- Id. de inquilino

Con estas credenciales, el autor de la llamada puede solicitar un token de acceso. El token se debe enviar en las solicitudes de API posteriores en la sección de encabezado, de la siguiente manera:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

El ejemplo de código de Python siguiente proporciona el token de acceso, que se puede usar para las posteriores llamadas API a FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**Encabezados de solicitud HTTP**

Estos son los encabezados de solicitud más comunes que deben especificarse al realizar una llamada API al centro de datos de FarmBeats:

- **Content-Type**: application/json
- **Autorización**: Bearer <Access-Token>
- **Accept**: application/json

### <a name="input-payload-to-create-metadata"></a>Carga de entrada para crear metadatos

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Dispositivo

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensor

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
La siguiente solicitud de ejemplo crea un dispositivo. Esta solicitud tiene un código JSON de entrada como carga con el cuerpo de la solicitud.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Las API devuelven identificadores únicos para cada instancia creada. Estos identificadores se deben conservar para enviar los mensajes de telemetría correspondientes.

### <a name="send-telemetry"></a>Envío de datos de telemetría

Ahora que ha creado los dispositivos y sensores en FarmBeats, puede enviar los mensajes de telemetría asociados.

### <a name="create-a-telemetry-client"></a>Creación de un cliente de telemetría

La telemetría se debe enviar a Azure Event Hubs, donde se procesará. Azure Event Hubs es un servicio que permite la ingesta de datos en tiempo real (telemetría) de aplicaciones y dispositivos conectados. Para enviar datos de telemetría a FarmBeats, cree un cliente que envíe mensajes a un centro de eventos en FarmBeats. Para más información sobre el envío de telemetría, consulte [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Envío de un mensaje de telemetría como cliente

Una vez que tenga establecida una conexión como cliente de Event Hubs, puede enviar mensajes al centro de eventos como JSON.

Este es un ejemplo de código de Python que envía telemetría como un cliente a un centro de eventos especificado:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Convierta el formato de datos históricos del sensor en un formato canónico que entienda Azure FarmBeats. El formato de mensaje canónico es así:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Después de agregar los dispositivos y sensores correspondientes, obtenga los valores de identificador de dispositivo y sensor del mensaje de telemetría, como se describe en la sección anterior.

A continuación se incluye un mensaje de telemetría de ejemplo:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la integración basada en la API REST, consulte [API REST](references-for-azure-farmbeats.md#rest-api).
