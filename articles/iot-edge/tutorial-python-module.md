---
title: 'Creación del módulo personalizado de Python: Azure IoT Edge | Microsoft Docs'
description: En este tutorial, se explica cómo se crea un módulo IoT Edge con código Python y cómo se implementa en un dispositivo perimetral.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: d75e4c3e2b6f28dfbde670406854ac87790dd090
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850112"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Tutorial: Desarrollo e implementación de un módulo de Python de IoT Edge para dispositivos Linux

Use Visual Studio Code para desarrollar código de C e implementarlo en un dispositivo Linux que ejecute Azure IoT Edge. 

Los módulos Azure IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos IoT Edge. En este tutorial, se detallan los pasos para crear e implementar un módulo de IoT Edge que filtra los datos de sensor en el dispositivo IoT Edge que configuró en el inicio rápido. En este tutorial, aprenderá a:    

> [!div class="checklist"]
> * Usar Visual Studio Code para crear un módulo de Python para IoT Edge.
> * Utilizar Visual Studio Code y Docker para crear una imagen de Docker y publicarla en el Registro.
> * Implementar el módulo en el dispositivo IoT Edge.
> * Ver datos generados.


El módulo IoT Edge que creó en este tutorial filtra lo datos sobre la temperatura generados por el dispositivo. Solo envía mensajes a los niveles superiores si la temperatura sobrepasa el umbral especificado. Este tipo de análisis perimetral resulta útil para reducir la cantidad de datos que se comunican a la nube y se almacenan en ella. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Ámbito de la solución

Este tutorial muestra cómo desarrollar un módulo en **Python** mediante **Visual Studio Code** y cómo se implementa en un **dispositivo Linux**. IoT Edge no admite módulos de Python para dispositivos Windows. 

Utilice la tabla siguiente si desea conocer las opciones para desarrollar e implementar módulos de Python en Linux: 

| Python | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Usar VS Code para los módulos de Python en Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Usar VS Code para los módulos de Python en Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe haber realizado el anterior para configurar el entorno de desarrollo de contenedores de Linux: [Desarrollo de módulos de IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Al completar cualquiera de estos tutoriales, se deben cumplir los siguientes requisitos previos: 

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) de nivel estándar o gratis en Azure.
* Un [dispositivo Linux que ejecute Azure IoT Edge](quickstart-linux.md).
* Un registro de contenedor, como [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [CE de Docker](https://docs.docker.com/install/) configurado para ejecutar contenedores de Linux.

Para desarrollar un módulo de IoT Edge en Python, instale los siguientes requisitos previos adicionales en la máquina de desarrollo: 

* [Extensión de Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code. 
* [Python](https://www.python.org/downloads/)
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar paquetes de Python (normalmente, se incluye con la instalación de Python).

>[!Note]
>Asegúrese de que su carpeta `bin` se encuentra en la ruta de acceso de su plataforma. Normalmente `~/.local/` para UNIX y macOS, o `%APPDATA%\Python` en Windows.

## <a name="create-a-module-project"></a>Creación de un proyecto de módulo
En los siguientes pasos creará un módulo de Python para IoT Edge mediante Visual Studio Code y Azure IoT Tools.

### <a name="create-a-new-project"></a>Creación de un nuevo proyecto

Use Visual Studio Code para crear una plantilla de soluciones de Python con la que generar la solución. 

1. En Visual Studio Code, seleccione **Ver** > **Terminal** para abrir el terminal integrado de VS Code.

1. Seleccione **Ver** > **Paleta de comandos** para abrir la paleta de comandos de VS Code. 

1. En la paleta de comandos, escriba y ejecute el comando **Azure: Sign in** (Azure: iniciar sesión) y siga las instrucciones para iniciar sesión en la cuenta de Azure. Si ya ha iniciado sesión, puede omitir este paso.

1. En la paleta de comandos, escriba y ejecute el comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nueva solución de IoT Edge). Siga los mensajes y proporcione la siguiente información para crear la solución:

   | Campo | Valor |
   | ----- | ----- |
   | Seleccionar carpeta | Elija la ubicación en el equipo de desarrollo en la que VS Code creará los archivos de la solución. |
   | Proporcionar un nombre de la solución | Escriba un nombre descriptivo para la solución o acepte el valor predeterminado **EdgeSolution**. |
   | Seleccionar plantilla del módulo | Elija **Módulo de Python**. |
   | Proporcionar un nombre de módulo | Llame al módulo **PythonModule**. |
   | Proporcionar repositorio de imágenes de Docker del módulo | Un repositorio de imágenes incluye el nombre del registro de contenedor y el nombre de la imagen de contenedor. La imagen de contenedor se rellena previamente con el nombre que proporcionó en el último paso. Reemplace **localhost:5000** por el valor del servidor de inicio de sesión del registro de contenedor de Azure. Puede recuperar el servidor de inicio de sesión de la página de información general del registro de contenedor en Azure Portal. <br><br>El repositorio de imágenes final será similar a este: \<nombre del Registro\>.azurecr.io/pythonmodule. |
 
   ![Especificación del repositorio de imágenes de Docker](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Adición de las credenciales del Registro

El archivo del entorno almacena las credenciales del repositorio de contenedores y las comparte con el entorno de ejecución de IoT Edge. El entorno de ejecución necesita estas credenciales para extraer las imágenes privadas e insertarlas en el dispositivo IoT Edge. 

1. En el explorador de VS Code, abra el archivo **.env**. 
2. Actualice los campos con los valores de **nombre de usuario** y **contraseña** que ha copiado del Registro de contenedor de Azure. 
3. Guarde el archivo .env. 

### <a name="select-your-target-architecture"></a>Selección de la arquitectura de destino

Actualmente, Visual Studio Code puede desarrollar módulos de C para dispositivos Linux AMD64 y Linux ARM32v7. Debe seleccionar qué arquitectura tiene como destino con cada solución, dado que el contenedor se crea y ejecuta de manera diferente para cada tipo de arquitectura. La opción predeterminada es Linux AMD64. 

1. Abra la paleta de comandos y busque **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Establecer la plataforma de destino predeterminada para la solución perimetral), o bien seleccione el icono de acceso directo en la barra lateral en la parte inferior de la ventana. 

2. En la paleta de comandos, seleccione la arquitectura de destino en la lista de opciones. Para este tutorial, usamos una máquina virtual Ubuntu como dispositivo IoT Edge, por lo que mantendrá el valor predeterminado **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Actualización del módulo con código personalizado

Cada plantilla incluye un código de ejemplo, que toma los datos del sensor simulado del módulo **tempSensor** y los enruta al centro de IoT. En esta sección, agregue el código que expande **PythonModule** para analizar los mensajes antes de enviarlos. 

1. En el explorador de VS Code, abra **módulos**  > **PythonModule** > **main.py**.

2. En la parte superior del archivo **main.py**, importe la biblioteca **json**:

    ```python
    import json
    ```

3. Agregue las variables **TEMPERATURE_THRESHOLD** y **TWIN_CALLBACKS** bajo los contadores globales. El umbral de temperatura establece el valor que debe superar la temperatura de la máquina para que los datos se envíen al centro de IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Reemplace la función **receive_message_callback** por el código siguiente:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Agregue una nueva función llamada **module_twin_callback**. Esta función se invoca cuando se actualicen las propiedades deseadas.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. En la clase **HubManager**, agregue una nueva línea al método **__init__** para inicializar la función **module_twin_callback** que acaba de agregar:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Guarde el archivo main.py.

8. En el explorador de VS Code, abra el archivo **deployment.template.json** en el área de trabajo de la solución de IoT Edge. 

9. Agregue el módulo gemelo de **PythonModule** que se corresponde con el manifiesto de implementación. Inserte el siguiente contenido JSON en la parte inferior de la sección **moduleContent**, después del módulo gemelo **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adición de un módulo gemelo a una plantilla de implementación](./media/tutorial-python-module/module-twin.png)

10. Guarde el archivo deployment.template.json.

## <a name="build-and-push-your-module"></a>Compilación e inserción del módulo

En la sección anterior ha creado una solución IoT Edge y ha agregado código a PythonModule que va a filtrar los mensajes y a eliminar aquellos en los que se notifique que la temperatura del equipo está dentro de los límites aceptables. Ahora, tiene que compilar la solución como una imagen de contenedor e insertarla en el registro de contenedor.

1. Abra el terminal integrado de VS Code, para lo que debe seleccionar **View** > **Terminal** (Ver > Terminal).

1. Escriba el comando siguiente en el terminal para iniciar sesión en Docker. Inicie sesión con el nombre de usuario, la contraseña y el servidor de inicio de sesión de Azure Container Registry. Puede recuperar estos valores en la sección **Claves de acceso** del Registro en Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Puede recibir una advertencia de seguridad en la que se recomiende el uso de `--password-stdin`. Aunque ese procedimiento se recomienda para escenarios de producción, está fuera del ámbito de este tutorial. Para más información, consulte la referencia de [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. En el explorador de VS Code, haga clic con el botón derecho en el archivo **deployment.template.json** y seleccione **Build and Push IoT Edge solution** (Compilar e insertar solución de IoT Edge).

   El comando de compilación e inserción inicia tres operaciones. En primer lugar, se crea una nueva carpeta en la solución denominada **config** que contiene los archivos del manifiesto de la implementación completa, con la información de la plantilla de implementación y otros archivos de la solución. En segundo lugar, ejecuta `docker build` para generar la imagen de contenedor basándose en el Dockerfile adecuado para la arquitectura de destino. A continuación, ejecuta `docker push` para insertar el repositorio de imágenes en el registro de contenedor.


## <a name="deploy-modules-to-device"></a>Implementación de módulos en el dispositivo

Utilice el explorador de Visual Studio Code y la extensión Azure IoT Tools para implementar el proyecto de módulo en el dispositivo IoT Edge. Ya tiene un manifiesto de implementación preparado para su escenario, el archivo **deployment.json** de la carpeta config. Ahora todo lo que necesita hacer es seleccionar un dispositivo que reciba la implementación.

Asegúrese de que el dispositivo IoT Edge está en funcionamiento.

1. En el explorador de Visual Studio Code, expanda la sección **Azure IoT Hub Devices** (Dispositivos Azure IoT Hub) para ver la lista de dispositivos IoT.

2. Haga clic con el botón derecho en el nombre del dispositivo IoT Edge y seleccione **Create Deployment for IoT Edge device** (Crear una implementación para un dispositivo individual).

3. Seleccione el archivo **deployment.json** de la carpeta **config** y, a continuación, haga clic en **Select Edge Deployment Manifest** (Seleccionar manifiesto de implementación de Edge). No utilice el archivo deployment.template.json.

4. Haga clic en el botón Actualizar. Debería ver el nuevo **PythonModule** en ejecución junto con el módulo **TempSensor**, así como **$edgeAgent** y **$edgeHub**. 

## <a name="view-generated-data"></a>Visualización de datos generados

Una vez aplicado el manifiesto de implementación al dispositivo de IoT Edge, el entorno de ejecución de IoT Edge del dispositivo recopila la información de implementación nueva y comienza a ejecutarse con ella. Los módulos que se ejecuten en el dispositivo y que no están incluidos en el manifiesto de implementación se detienen. Los módulos que falten en el dispositivo se inician.

Puede ver el estado del dispositivo de IoT Edge con la sección **Azure IoT Hub Devices** (Dispositivos de Azure IoT Hub) del explorador de Visual Studio Code. Expanda los detalles del dispositivo para ver una lista de los módulos implementados y en ejecución.

1. En el explorador de Visual Studio Code, haga clic con el botón derecho en el nombre del dispositivo IoT Edge y seleccione **Start Monitoring Built-in Event Endpoint** (Iniciar supervisión del punto de conexión del evento integrado).

2. Vea los mensajes que llegan a IoT Hub. Los mensajes pueden tardar un rato en llegar, ya que el dispositivo IoT Edge tiene que recibir su nueva implementación e iniciar todos los módulos. Después, los cambios realizados en el código PythonModule esperan hasta que la temperatura de la máquina alcanza los 25 grados antes de enviar los mensajes. También agrega el tipo de mensaje **Alerta** a los mensajes que llegan a ese umbral de temperatura. 

## <a name="edit-the-module-twin"></a>Edición del módulo gemelo

Usamos el módulo gemelo PythonModule en el manifiesto de implementación para establecer el umbral de temperatura en 25 grados. Puede usar el módulo gemelo para cambiar la funcionalidad sin tener que actualizar el código del módulo.

1. En Visual Studio Code, expanda los detalles en el dispositivo IoT Edge para ver los módulos en ejecución. 

2. Haga clic con el botón derecho en **PythonModule** y seleccione **Editar módulo gemelo**. 

3. Busque **TemperatureThreshold** en las propiedades deseadas. Cambie su valor por una temperatura nueva de 5 a 10 grados más que la última temperatura registrada. 

4. Guarde el archivo del módulo gemelo.

5. Haga clic con el botón derecho en cualquier parte del panel de edición del módulo gemelo y seleccione **Actualizar módulo gemelo**. 

6. Supervise los mensajes entrantes del dispositivo a la nube. Debería ver que los mensajes se detienen hasta que se alcanza el nuevo umbral de temperatura. 

## <a name="clean-up-resources"></a>Limpieza de recursos 

Si prevé seguir con el siguiente artículo recomendado, puede mantener los recursos y las configuraciones que ya ha creado y volverlos a utilizar. También puede seguir usando el mismo dispositivo de IoT Edge como dispositivo de prueba. 

En caso contrario, para evitar gastos, puede eliminar las configuraciones locales y los recursos de Azure que creó en este artículo. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un módulo IoT Edge que contiene código para filtrar los datos sin procesar generados por el dispositivo IoT Edge. Cuando esté preparado para crear sus propios módulos, puede aprender más sobre cómo [desarrollar sus propios módulos IoT Edge](module-development.md) y sobre cómo [desarrollar módulos con Visual Studio Code](how-to-vs-code-develop-module.md). Puede continuar con los siguientes tutoriales para obtener información sobre cómo Azure IoT Edge puede ayudarle a implementar servicios en la nube de Azure para procesar y analizar datos en el perímetro.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)