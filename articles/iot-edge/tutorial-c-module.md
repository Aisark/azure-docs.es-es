---
title: 'Tutorial: Creación del módulo personalizado de C: Azure IoT Edge | Microsoft Docs'
description: En este tutorial se muestra la creación de un módulo IoT Edge con código C y su implementación en un dispositivo perimetral
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: eeaff4769dba5b6e6951665d09cd12d13f22af07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273726"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desarrollo de un módulo IoT Edge con C en el dispositivo simulado

Los módulos de IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. En este tutorial, se detallan los pasos para crear e implementar un módulo de IoT Edge que filtra los datos de sensor. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar Visual Studio Code para crear un módulo de IoT Edge en C.
> * Utilizar Visual Studio Code y Docker para crear una imagen de Docker y publicarla en un contenedor de registro.
> * Implementación del módulo en el dispositivo IoT Edge
> * Consulta de los datos generados


El módulo IoT Edge que creó en este tutorial filtra lo datos sobre la temperatura generados por el dispositivo. Solo envía mensajes a los niveles superiores si la temperatura sobrepasa el umbral especificado. Este tipo de análisis perimetral resulta útil para reducir la cantidad de datos que se comunican a la nube y se almacenan en ella.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Requisitos previos

Un dispositivo de Azure IoT Edge:

* Puede usar una máquina de Azure como dispositivo de IoT Edge; para ello, siga los pasos que se indican en el artículo de inicio rápido para dispositivos [Linux](quickstart-linux.md) o [Windows](quickstart.md). 

   >[!TIP]
   >Este tutorial usa Visual Studio Code para desarrollar un módulo de C mediante contenedores de Linux. Si desea desarrollar en C para contenedores de Windows, debe usar Visual Studio 2017. Para más información, consulte [Uso de Visual Studio 2017 para desarrollar y depurar módulos para Azure IoT Edge](how-to-visual-studio-develop-module.md).

Recursos en la nube:

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) de nivel estándar o gratis en Azure.

Recursos de desarrollo:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Extensión de C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para Visual Studio Code.
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

En este tutorial, puede usar Azure IoT Tools para Visual Studio Code a fin de compilar un módulo y crear una **imagen de contenedor** a partir de los archivos. Después, insertará esta imagen en un **Registro** donde se almacenan y administran las imágenes. Por último, implementará la imagen en el Registro para que se ejecute en el dispositivo IoT Edge.

Puede usar cualquier registro compatible con Docker para almacenar las imágenes de contenedor. Dos de los servicios de registro de Docker más conocidos son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En este tutorial, utilizaremos Azure Container Registry.

Si no dispone de un registro de contenedores, siga estos pasos para crear uno nuevo en Azure:

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Contenedores** > **Container Registry**.

2. Especifique los siguientes valores para crear un registro de contenedor:

   | Campo | Valor |
   | ----- | ----- |
   | Nombre de registro | Especifique un nombre único. |
   | Subscription | Seleccione una suscripción en la lista desplegable. |
   | Grupos de recursos | Se recomienda usar el mismo grupo de recursos para todos los recursos de prueba que se crean en las guías de inicio rápido y los tutoriales de IoT Edge. Por ejemplo, **IoTEdgeResources**. |
   | Ubicación | Elija una ubicación cercana a usted. |
   | Usuario administrador | Seleccione **Habilitar**. |
   | SKU | Seleccione **Básica**. |

5. Seleccione **Crear**.

6. Una vez que se haya creado el Registro de contenedor, desplácese hasta él y seleccione **Claves de acceso**.

7. Copie los valores de **Servidor de inicio de sesión**, **Nombre de usuario** y **Contraseña**. Utilice estos valores más adelante en el tutorial para proporcionar acceso al registro de contenedor.

## <a name="create-an-iot-edge-module-project"></a>Creación de un proyecto de módulo IoT Edge
En los siguientes pasos puede ver cómo crear un proyecto de módulo IoT Edge basado en .NET Core 2.0 mediante Visual Studio Code y Azure IoT Tools.

### <a name="create-a-new-solution"></a>Creación de una nueva solución

Cree una plantilla de solución de C que pueda personalizar con su propio código.

1. Seleccione **Ver** > **Paleta de comandos** para abrir la paleta de comandos de VS Code.

2. En la paleta de comandos, escriba y ejecute el comando **Azure: Sign in** (Azure: iniciar sesión) y siga las instrucciones para iniciar sesión en la cuenta de Azure. Si ya ha iniciado sesión, puede omitir este paso.

3. En la paleta de comandos, escriba y ejecute el comando **Azure IoT Edge: New IoT Edge solution** (Nueva solución de IoT Edge). Para crear la solución, siga las indicaciones de la paleta de comandos.

   | Campo | Valor |
   | ----- | ----- |
   | Seleccionar carpeta | Elija la ubicación en el equipo de desarrollo en la que VS Code creará los archivos de la solución. |
   | Proporcionar un nombre de la solución | Escriba un nombre descriptivo para la solución o acepte el valor predeterminado **EdgeSolution**. |
   | Seleccionar plantilla del módulo | Elija **Módulo C**. |
   | Proporcionar un nombre de módulo | Llame al módulo **CModule**. |
   | Proporcionar repositorio de imágenes de Docker del módulo | Un repositorio de imágenes incluye el nombre del registro de contenedor y el nombre de la imagen de contenedor. La imagen de contenedor se rellena previamente con el nombre que proporcionó en el último paso. Reemplace **localhost:5000** por el valor del servidor de inicio de sesión del registro de contenedor de Azure. Puede recuperar el servidor de inicio de sesión de la página de información general del registro de contenedor en Azure Portal. <br><br> El repositorio de imágenes final será similar a \<nombre del Registro\>.azurecr.io/cmodule. |
 
   ![Especificación del repositorio de imágenes de Docker](./media/tutorial-c-module/repository.png)

La ventana de VS Code carga el área de trabajo de la solución de IoT Edge con sus cinco componentes de nivel superior. La carpeta **modules** contiene el código de C del módulo, así como archivos Dockerfile para compilar el módulo como una imagen de contenedor. El archivo **\.env** almacena las credenciales del registro de contenedor. El archivo **deployment.template.json** contiene la información que usa el entorno de ejecución de IoT Edge para implementar módulos en un dispositivo. Y el archivo **deployment.debug.template.json** contiene la versión de depuración de los módulos. En este tutorial no se editarán la carpeta **\.vscode** ni el archivo **\.gitignore**.

Si no especificó un registro de contenedor al crear la solución y aceptó el valor predeterminado localhost:5000, no tendrá un archivo \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adición de las credenciales del Registro

El archivo del entorno almacena las credenciales del registro de contenedor y las comparte con el runtime de IoT Edge. El entorno de ejecución necesita estas credenciales para extraer las imágenes privadas e insertarlas en el dispositivo IoT Edge.

1. En el explorador de VS Code, abra el archivo .env.
2. Actualice los campos con los valores de **nombre de usuario** y **contraseña** que ha copiado del Registro de contenedor de Azure.
3. Guarde este archivo.

### <a name="update-the-module-with-custom-code"></a>Actualización del módulo con código personalizado

Agregue al módulo C código que le permita comprobar si la temperatura que se ha notificado que tiene la máquina ha superado un umbral seguro. Si la temperatura es demasiado alta, el módulo agrega un parámetro de alerta al mensaje antes de enviar los datos a IoT Hub. 

1. En este escenario, los datos del sensor están en formato JSON. Para filtrar los mensajes en formato JSON, importe una biblioteca JSON para C. En este tutorial se usa Parson.

   1. Descargue el [repositorio de Parson GitHub](https://github.com/kgabis/parson). Copie los archivos **parson.c** y **parson.h** en la carpeta **CModule**.

   2. Abra los **módulos** > **CModule** > **CMakeLists.txt**. En la parte superior del archivo, importe los archivos de Parson como una biblioteca denominada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Agregue **my_parson** a la lista de bibliotecas en la función **target_link_libraries** de CMakeLists.txt.

   4. Guarde el archivo **CMakeLists.txt**.

   5. Abra los **módulos** > **CModule** > **main.c**. Al final de la lista de instrucciones include, agregue una nueva para incluir `parson.h` con el fin de lograr compatibilidad con JSON:

      ```c
      #include "parson.h"
      ```

1. En el archivo **main.c**, agregue una variable global denominada `temperatureThreshold` después de la sección de include. Esta variable establece el valor que debe superar la temperatura medida para que los datos se envíen a IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Reemplace toda la función `CreateMessageInstance` por el código siguiente. Esta función asigna un contexto para la devolución de llamada.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

1. Reemplace toda la función `InputQueue1Callback` por el código siguiente. Esta función implementa el filtro de mensajería real.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Agregue una función `moduleTwinCallback`. Este método recibe las actualizaciones sobre las propiedades que se quieren del módulo gemelo y actualiza la variable **temperatureThreshold** para que coincida. Todos los módulos tienen su propio módulo gemelo, que le permite configurar el código que se ejecuta dentro de un módulo directamente desde la nube.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Reemplace la función `SetupCallbacksForModule` por el código siguiente.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Guarde el archivo main.c.

1. En el explorador de VS Code, abra el archivo **deployment.template.json** en el área de trabajo de la solución de IoT Edge. Este archivo le indica al agente de IoT Edge qué módulos implementar, en este caso **tempSensor** y **CModule**, y a IoT Edge Hub cómo enrutar los mensajes entre ellos. La extensión de Visual Studio Code rellena de forma automática la mayor parte de la información que necesita en la plantilla de implementación, aunque comprueba que todo sea correcto para la solución: 

   1. La plataforma predeterminada de la instancia de IoT Edge está establecida en **amd64** en la barra de estado de VS Code, lo que significa que **CModule** está establecido en la versión amd64 de Linux de la imagen. Cambie la plataforma predeterminada en la barra de estado de **amd64** a **arm32v7** si esa es la arquitectura del dispositivo de IoT Edge. 

      ![Actualización de la plataforma de imagen del módulo](./media/tutorial-c-module/image-platform.png)

   2. Compruebe que la plantilla tiene el nombre de módulo correcto, no el nombre **SampleModule** predeterminado que cambió cuando creó la solución de IoT Edge.

   3. En la sección **registryCredentials** se almacenan las credenciales de Docker Registry, para que el agente de IoT Edge pueda extraer la imagen del módulo. Los pares reales de nombre de usuario y contraseña se almacenan en el archivo .env, que Git pasa por alto. Agregue sus credenciales al archivo .env si no lo ha hecho ya.  

   4. Si desea más información sobre los manifiestos de implementación, consulte [Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge](module-composition.md).

1. Agregue el módulo gemelo CModule al manifiesto de implementación. Inserte el siguiente contenido JSON en la parte inferior de la sección `moduleContent`, después del módulo gemelo `$edgeHub`:

   ```json
       "CModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adición de un gemelo de CModule gemelo a una plantilla de implementación](./media/tutorial-c-module/module-twin.png)

1. Guarde el archivo **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Compilación e inserción de la solución

En la sección anterior ha creado una solución IoT Edge y ha agregado código a CModule que va a filtrar los mensajes y a eliminar aquellos en los que se notifique que la temperatura del equipo está dentro de los límites aceptables. Ahora, tiene que compilar la solución como una imagen de contenedor e insertarla en el registro de contenedor.

1. Abra el terminal integrado de VS Code, para lo que debe seleccionar **View** > **Terminal** (Ver > Terminal).

1. Escriba el comando siguiente en el terminal integrado de Visual Studio Code para iniciar sesión en Docker. Debe iniciar sesión con sus credenciales de Azure Container Registry, con el fin de que pueda insertar la imagen del módulo en el registro.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Utilice el nombre de usuario, la contraseña y el servidor de inicio de sesión que copió de Azure Container Registry en la primera sección. También puede recuperarlos de nuevo en la sección **Claves de acceso** del registro mediante Azure Portal.

2. En el explorador de VS Code, haga clic con el botón derecho en el archivo **deployment.template.json** y seleccione **Build and Push IoT Edge solution** (Compilar e insertar solución de IoT Edge).

Cuando le indica a Visual Studio Code que compile la solución, lo primero que hace esta herramienta es generar un archivo `deployment.json` en una nueva carpeta **config**. La información del archivo deployment.json se recopila del archivo de plantilla que ha actualizado, el archivo .env que ha usado para almacenar las credenciales del registro de contenedor y el archivo module.json de la carpeta CModule.

Después, Visual Studio Code ejecuta dos comandos en el terminal integrado: `docker build` y `docker push`. Estos dos comandos compilan el código, empaquetan `CModule.dll` en contenedores y lo insertan en el registro de contenedor que especificó cuando inicializó la solución.

Puede ver la dirección de la imagen de contenedor completa con la etiqueta en el terminal integrado de VS Code. La dirección de la imagen se crea a partir de la información del archivo `module.json`, con el formato **\<repositorio\>:\<versión\>-\<plataforma\>**. Para este tutorial, debería ser similar a la siguiente: **registryname.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Si recibe un error al intentar compilar e insertar su módulo, realice las siguientes comprobaciones:
>* ¿Ha iniciado sesión en Docker en Visual Studio Code con las credenciales de su registro de contenedor? Estas credenciales son diferentes a las que se utilizan para iniciar sesión en Azure Portal.
>* ¿Es el repositorio de contenedor correcto? Abra **Módulos** > **cmodule** > **module.json** y busque el campo **repositorio**. El repositorio de imágenes será similar a **\<nombre del Registro\>.azurecr.io/cmodule**. 
>* ¿Va a compilar el mismo tipo de contenedores que está ejecutando la máquina de desarrollo? Visual Studio Code establece de forma predeterminada contenedores de Linux amd64. Si la máquina de desarrollo ejecuta contenedores de Linux arm32v7, actualice la plataforma en la barra de estado azul en la parte inferior de la ventana de VS Code para que coincida con la plataforma del contenedor. Los módulos de C no pueden compilarse como contenedores de Windows. 

## <a name="deploy-and-run-the-solution"></a>Implementación y ejecución de la solución

En el artículo de la guía de inicio rápido que siguió para configurar el dispositivo de IoT Edge, implementó un módulo mediante Azure Portal. También puede implementar módulos con la extensión Azure IoT Hub Toolkit (anteriormente, extensión Azure IoT Toolkit) para Visual Studio Code. Ya tiene un manifiesto de implementación preparado para su escenario, el archivo **deployment.json**. Ahora todo lo que necesita hacer es seleccionar un dispositivo que reciba la implementación.

1. En la paleta de comandos de VS Code, ejecute **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: seleccione IoT Hub).

2. Elija la suscripción y la instancia de IoT Hub que contienen el dispositivo IoT Edge que desea configurar.

3. En el explorador de VS Code, expanda la sección **Azure IoT Hub Devices** (Dispositivos de Azure IoT Hub).

4. Haga clic con el botón derecho en el nombre del dispositivo IoT Edge y seleccione **Create Deployment for IoT Edge device** (Crear una implementación para un dispositivo individual).

   ![Crear una implementación para un dispositivo individual](./media/tutorial-c-module/create-deployment.png)

5. Seleccione el archivo **deployment.json** de la carpeta **config** y, a continuación, haga clic en **Select Edge Deployment Manifest** (Seleccionar manifiesto de implementación de Edge). No utilice el archivo deployment.template.json.

6. Haga clic en el botón Actualizar. Debería ver el nuevo **CModule** en ejecución junto con el módulo **TempSensor**, **$edgeAgent** y **$edgeHub**.

## <a name="view-generated-data"></a>Visualización de datos generados

Una vez aplicado el manifiesto de implementación al dispositivo de IoT Edge, el entorno de ejecución de IoT Edge del dispositivo recopila la información de implementación nueva y comienza a ejecutarse con ella. Los módulos que se ejecuten en el dispositivo y que no están incluidos en el manifiesto de implementación se detienen. Los módulos que falten en el dispositivo se inician.

Puede ver el estado del dispositivo de IoT Edge con la sección **Azure IoT Hub Devices** (Dispositivos de Azure IoT Hub) del explorador de Visual Studio Code. Expanda los detalles del dispositivo para ver una lista de los módulos implementados y en ejecución.

En el propio dispositivo de IoT Edge puede ver el estado de los módulos de implementación mediante el comando `iotedge list`. Debería ver cuatro módulos: los dos módulos personalizados del entorno de ejecución de IoT Edge, tempSensor y el sensor personalizado que ha creado en este tutorial. Los módulos pueden tardar unos minutos en comenzar; si no los ve inicialmente, vuelva a ejecutar el comando.

Los mensajes que generan los módulos se ven con el comando `iotedge logs <module name>`.

Puede ver los mensajes conforme llegan a IoT Hub mediante Visual Studio Code.

1. Para supervisar los datos que llegan al centro de IoT, haga clic en **...** y seleccione **Start Monitoring D2C Messages** (Iniciar supervisión de mensajes de D2C).
2. Para supervisar el mensaje de D2C de un determinado dispositivo, haga clic con el botón derecho en un dispositivo de la lista y seleccione **Start Monitoring D2C Messages** (Iniciar supervisión de mensajes de D2C).
3. Para detener la supervisión de datos, ejecute el comando **Azure IoT Hub: Stop Monitoring D2C Message** (Detener supervisión de mensaje de D2C) en la paleta de comandos.
4. Para ver o actualizar el módulo gemelo, haga clic con el botón derecho en el módulo de la lista y seleccione **Edit module twin** (Editar módulo gemelo). Para actualizar el módulo gemelo, guarde el archivo JSON gemelo y haga clic con el botón derecho en el área del editor y seleccione **Update Module Twin** (Actualizar módulo gemelo).
5. Para ver los registros de Docker, instale [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code y busque en el explorador de Docker los módulos que se ejecutan localmente. En el menú contextual, haga clic en **Show Logs** (Mostrar registros) en la vista del terminal integrado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si prevé seguir con el siguiente artículo recomendado, puede mantener los recursos y las configuraciones que ya ha creado y volverlos a utilizar. También puede seguir usando el mismo dispositivo de IoT Edge como dispositivo de prueba.

En caso contrario, para evitar gastos, puede eliminar las configuraciones locales y los recursos de Azure que creó en este artículo.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un módulo IoT Edge que contiene código para filtrar los datos sin procesar generados por el dispositivo IoT Edge. Cuando esté listo para compilar sus propios módulos, consulte [Develop a C# module with Azure IoT Edge for Visual Studio Code](how-to-develop-c-module.md) (Desarrollo de un módulo de C con Azure IoT Edge para Visual Studio Code). Puede continuar con los siguientes tutoriales para aprender otras formas en las que Azure IoT Edge puede ayudarle a convertir los datos en información empresarial en el perímetro.

> [!div class="nextstepaction"]
> [Almacenamiento de datos en el perímetro con bases de datos de SQL Server](tutorial-store-data-sql-server.md)

