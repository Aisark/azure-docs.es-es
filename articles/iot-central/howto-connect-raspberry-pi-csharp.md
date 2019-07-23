---
title: Conexión de un dispositivo Raspberry Pi a la aplicación de Azure IoT Central (C#) | Microsoft Docs
description: Como desarrollador de dispositivos, aprenda a conectar un dispositivo Raspberry Pi a su aplicación de Azure IoT Central mediante C#.
author: viv-liu
ms.author: viviali
ms.date: 04/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3e77494eacaf16ac23a531cb7a16fe8bf6117006
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714421"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Conexión de un dispositivo Raspberry Pi a su aplicación de Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

En este artículo se describe cómo conectar, en tanto que desarrollador de dispositivos, un dispositivo Raspberry Pi a una aplicación de Microsoft Azure IoT Central con el lenguaje de programación C#.

## <a name="before-you-begin"></a>Antes de empezar

Para completar los pasos descritos en este artículo, necesita los siguientes componentes:

* Una aplicación de Azure IoT Central creada a partir de la plantilla de aplicación **Ejemplo Devkits**. Para más información, consulte la [guía de inicio rápido para crear una aplicación](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi que ejecuta el sistema operativo Raspbian. Raspberry Pi debe poder conectarse a Internet. Para obtener más información, consulte [Configuración del dispositivo Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Aplicación **Ejemplo Devkits**

Una aplicación creada a partir de la plantilla de aplicación **Ejemplo Devkits** incluye una plantilla de dispositivo **Raspberry Pi** con las siguientes características:

- Datos de telemetría, lo que incluye las siguientes medidas que recopilará el dispositivo:
  - Humedad
  - Temperatura
  - Presión
  - Magnetómetro (X, Y, Z)
  - Acelerómetro (X, Y, Z)
  - Giroscopio (X, Y, Z)
- Configuración
  - Voltage (Voltaje)
  - Current
  - Fan Speed
  - Conmutador de infrarrojos.
- Properties (Propiedades)
  - Propiedad Die number (Número de chip) del dispositivo
  - Propiedad Location cloud (ubicación en la nube)

Para obtener detalles completos sobre la configuración de la plantilla de dispositivo, consulte los [detalles de la plantilla de dispositivo Raspberry Pi](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Adición de un dispositivo real

En su aplicación de Azure IoT Central, agregue un dispositivo real desde la plantilla de dispositivo **Raspberry Pi**. Anote los detalles de conexión del dispositivo (**Id. de ámbito**, **Id. de dispositivo** y **Clave principal**). Para más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un dispositivo real a la aplicación de Azure IoT Central).

### <a name="create-your-net-application"></a>Creación de su aplicación .NET

La aplicación de dispositivo se crea y se prueba en el equipo de escritorio.

Para completar los pasos siguientes, puede usar Visual Studio Code. Para obtener más información, vea [Working with C#](https://code.visualstudio.com/docs/languages/csharp) (Trabajo con C#).

> [!NOTE]
> Si lo prefiere, puede completar los pasos siguientes utilizando un editor de código diferente.

1. Para inicializar el proyecto de .NET y agregar los paquetes de NuGet necesarios, ejecute los siguientes comandos:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Abra la carpeta `pisample` en Visual Studio Code. A continuación, abra el archivo de proyecto **pisample.csproj**. Agregue la etiqueta `<RuntimeIdentifiers>` que se muestra en el fragmento de código siguiente:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Su número de versión del paquete **Microsoft.Azure.Devices.Client** puede ser mayor del que se muestra.

1. Guarde **pisample.csproj**. Si Visual Studio Code le pide que ejecute el comando de restauración, elija **Restaurar**.

1. Abra el archivo **Program.cs** y sustituya el contenido por el código siguiente:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Actualizará el marcador de posición `{your device connection string}` en un paso posterior.

## <a name="run-your-net-application"></a>Ejecución de su aplicación .NET

Agregue la cadena de conexión específica del dispositivo al código para que el dispositivo se autentique con Azure IoT Central. Siga estas instrucciones para [generar la cadena de conexión del dispositivo](howto-generate-connection-string.md) mediante el **Id. de ámbito**, el **Id. de dispositivo** y la **clave principal** que anotó anteriormente.

1. Reemplace `{your device connection string}` en el archivo **Program.cs** por la cadena de conexión que generó.

1. En el entorno de línea de comandos, ejecute el comando siguiente:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Copie la carpeta `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` al dispositivo Raspberry Pi. Puede usar el comando **scp** para copiar los archivos, por ejemplo:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Para obtener más información, consulte [Raspberry Pi remote access](https://www.raspberrypi.org/documentation/remote-access/) (Acceso remoto de Raspberry Pi).

1. Inicie sesión en el dispositivo Raspberry Pi y ejecute los siguientes comandos en un shell:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. En su Raspberry Pi, ejecute los comandos siguientes:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![El programa se inicia](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. En la aplicación de Azure IoT Central, puede ver cómo el código que se ejecuta en Raspberry Pi interactúa con la aplicación:

   * En la página **Measurements** (Medidas) para el dispositivo real, puede ver la telemetría.
   * En la página **Properties** (Propiedades), puede ver el valor de la propiedad **Die Number** (Número de chip) notificado.
   * En la página **Settings** (Configuración), puede cambiar distintos parámetros en el instalador de Raspberry Pi, como el voltaje y la velocidad del ventilador.

     En la captura de pantalla siguiente se muestra Raspberry Pi recibiendo el cambio de configuración:

     ![Raspberry Pi recibe el cambio de configuración](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Detalles de la plantilla de dispositivo Raspberry PI

Una aplicación creada a partir de la plantilla de aplicación **Ejemplo Devkits** incluye una plantilla de dispositivo **Raspberry Pi** con las siguientes características:

### <a name="telemetry-measurements"></a>Medidas de telemetría

| Nombre del campo     | Unidades  | Mínima | Máxima | Posiciones decimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humedad       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| presión       | hPa    | 260     | 1260    | 0              |
| magnetometerX (magnetómetro X)  | mgauss | -1000   | 1000    | 0              |
| magnetometerY (magnetómetro Y)  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ (magnetómetro Z)  | mgauss | -1000   | 1000    | 0              |
| accelerometerX (acelerómetro X) | mg     | -2000   | 2000    | 0              |
| accelerometerY (acelerómetro Y) | mg     | -2000   | 2000    | 0              |
| accelerometerZ (acelerómetro Z) | mg     | -2000   | 2000    | 0              |
| gyroscopeX (giróscopo X)     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY (giróscopo Y)     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ (giróscopo Z)     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Configuración

Valores numéricos

| Nombre para mostrar | Nombre del campo | Unidades | Posiciones decimales | Mínima | Máxima | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage (Voltaje)      | setVoltage | Voltios | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Fan Speed    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Cambiar configuración

| Nombre para mostrar | Nombre del campo | Texto activado | Texto desactivado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVAR      | Apagado      | Off     |

### <a name="properties"></a>Properties (Propiedades)

| Type            | Nombre para mostrar | Nombre del campo | Tipo de datos |
| --------------- | ------------ | ---------- | --------- |
| Propiedad de dispositivo | Die number   | dieNumber  | número    |
| Texto            | Ubicación     | location   | N/D       |

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a conectar un dispositivo Raspberry Pi a la aplicación Azure IoT Central, le sugerimos como siguiente paso aprender a [configurar una plantilla de dispositivo personalizada](howto-set-up-template.md) para su dispositivo IoT.
