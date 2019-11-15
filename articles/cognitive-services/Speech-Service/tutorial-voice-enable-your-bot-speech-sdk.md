---
title: 'Tutorial: Habilitación del bot con voz mediante el SDK de voz'
titleSuffix: Azure Cognitive Services
description: En este tutorial, creará un bot de eco con Microsoft Bot-Framework, lo implementará en Azure y lo registrará en el canal Direct Line Speech de Bot-Framework. A continuación, configurará una aplicación cliente de ejemplo para Windows que le permita hablar con el bot y oír su respuesta.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: 839bb24996ab782a386d7d28bcc1c06c686e6cd5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578034"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Tutorial: Habilitación del bot con voz mediante el SDK de voz

Ahora puede usar la potencia de los servicios de voz para habilitar fácilmente un bot de chat.

En este tutorial, creará un bot de eco con Microsoft Bot-Framework, lo implementará en Azure y lo registrará en el canal Direct Line Speech de Bot-Framework. A continuación, configurará una aplicación cliente de ejemplo para Windows que le permita hablar con el bot y oír su respuesta.

Este tutorial está pensado para desarrolladores que empiezan a trabajar con Azure, con bots de Bot-Framework, con Direct Line Speech o con el SDK de voz y quieren crear rápidamente un sistema de trabajo con codificación limitada. No es necesario tener experiencia ni estar familiarizado con estos servicios.

Al final de este ejercicio, habrá configurado un sistema que funcionará de la siguiente manera:

1. La aplicación cliente de ejemplo está configurada para conectarse al canal Direct Line Speech y al bot de eco.
1. El audio se graba a través del micrófono predeterminado al presionar el botón (o se graba continuamente si se activa la palabra clave personalizada)
1. Opcionalmente, se produce la detección de la palabra clave, que canaliza el streaming de audio a la nube.
1. Con el SDK de voz, la aplicación se conecta al canal Direct Line Speech y transmite el audio.
1. Opcionalmente, se produce una comprobación de la palabra clave de mayor precisión en el servicio.
1. El audio se pasa al servicio de reconocimiento de voz y se transcribe en texto.
1. El texto reconocido se pasa al bot de eco como una actividad de Bot Framework. 
1. El texto de la respuesta se convierte en audio mediante el servicio de conversión de texto a voz (TTS) y se vuelve a transmitir a la aplicación cliente para su reproducción.

![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "El flujo de canales de voz")

> [!NOTE]
> Los pasos de este tutorial no requieren un servicio de pago. Como nuevo usuario de Azure, podrá usar créditos de su suscripción gratuita de Azure y del nivel gratis de los servicios de voz para completar este tutorial.

Este tutorial abarca lo siguiente:
> [!div class="checklist"]
> * Crear nuevos recursos de Azure
> * Compilar, probar e implementar el ejemplo de bot de eco en una instancia de Azure App Service
> * Registrar el bot con el canal Direct Line Speech
> * Compilar y ejecutar el cliente de Direct Line Speech para interactuar con el bot de eco
> * Agregar la activación de la palabra clave personalizada
> * Aprender cómo cambiar el idioma de la voz hablada y reconocida

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

- Un equipo con Windows 10 con un micrófono y altavoces (o auriculares) que funcionen
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) o cualquier versión posterior
- [.NET Core SDK](https://dotnet.microsoft.com/download) versión 2.1 o posterior.
- Una cuenta de Azure. [Regístrese gratis](https://azure.microsoft.com/free/ai/).
- Una cuenta [GitHub](https://github.com/)
- [Git para Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

La aplicación cliente que creará en este tutorial utiliza una serie de servicios de Azure. Para reducir el tiempo de ida y vuelta de las respuestas del bot, deberá asegurarse de que estos servicios se encuentran en la misma región de Azure. En esta sección, se crea un grupo de recursos en la región **Oeste de EE. UU.** Este grupo de recursos se usará al crear recursos individuales para Bot-Framework, el canal Direct Line Speech y los Servicios de voz.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
1. En el panel de la izquierda, seleccione **Grupos de recursos**. Después, haga clic en **Agregar** para agregar un nuevo grupo de recursos.
1. Se le pedirá que proporcione algo de información:
   * Establezca **Suscripción** en **Evaluación gratuita** (también puede usar una suscripción existente).
   * Escriba un nombre para el **grupo de recursos**. Se recomienda **SpeechEchoBotTutorial-ResourceGroup**.
   * En la lista desplegable **Región**, seleccione **Oeste de EE. UU.**
1. Haga clic en **Revisar y crear**. Debería ver un banner que indica **Validación superada**.
1. Haga clic en **Create**(Crear). La creación del grupo de recursos tardará unos minutos.
1. Al igual que con los recursos que creará más adelante en este tutorial, es una buena idea anclar este grupo de recursos al panel para facilitar el acceso. Si desea anclar este grupo de recursos, haga clic en el icono de anclaje en la esquina superior derecha del panel.

### <a name="choosing-an-azure-region"></a>Elección de una región de Azure

Si quiere usar una región diferente para este tutorial, estos factores pueden limitar las opciones:

* Asegúrese de que usa una [región de Azure compatible](regions.md#voice-assistants).
* El canal Direct Line Speech utiliza el servicio de conversión de texto a voz, que tiene voces estándar y neuronales. Las voces neuronales se [limitan a regiones específicas de Azure](regions.md#standard-and-neural-voices).
* Las claves de la evaluación gratuitas pueden estar restringidas a una región específica.

Para más información sobre las regiones, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Crear recursos

Ahora que tiene un grupo de recursos en la región **Oeste de EE. UU.** , el siguiente paso es crear recursos individuales para cada servicio que usará en este tutorial.

### <a name="create-a-speech-services-resource"></a>Creación de un recurso de Servicios de voz

Para crear un recurso de voz, siga estas instrucciones:

1. En el panel de navegación izquierdo, seleccione **Crear un recurso**.
2. En la barra de búsqueda, escriba **Voz**.
3. Seleccione **Voz** y, a continuación, haga clic en **Crear**.
4. Se le pedirá que proporcione algo de información:
   * Asigne un **nombre** al recurso. Se recomienda **SpeechEchoBotTutorial-Speech**.
   * En **Suscripción**, asegúrese de que esté seleccionada la opción **Evaluación gratuita**.
   * En **Ubicación**, seleccione **Oeste de EE. UU.**
   * En **Plan de tarifa**, seleccione **F0**. Es el plan gratuito.
   * En **Grupo de recursos**, seleccione **SpeechEchoBotTutorial-ResourceGroup**.
5. Después de haber escrito la información necesaria, haga clic en **Crear**. La creación del recurso puede tardar unos minutos.
6. Más adelante en este tutorial necesitará las claves de suscripción para este servicio. Puede acceder a estas claves en cualquier momento desde **Información general** del recurso (Administrar claves) o **Claves**.

En este momento, compruebe que el grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tiene un recurso de voz:

| NOMBRE | TIPO  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | Oeste de EE. UU. |

### <a name="create-an-azure-app-service-plan"></a>Crear un plan de Azure App Service

El primer paso consiste en crear un plan de App Service. Un plan de App Service define un conjunto de recursos de proceso para que una aplicación web se ejecute.

1. En el panel de navegación izquierdo, seleccione **Crear un recurso**.
2. En la barra de búsqueda, escriba **Plan de App Service**. A continuación, busque y seleccione la tarjeta **Plan del servicio de aplicaciones** en los resultados de la búsqueda.
3. Haga clic en **Create**(Crear).
4. Se le pedirá que proporcione algo de información:
   * Establezca **Suscripción** en **Evaluación gratuita** (también puede usar una suscripción existente).
   * En **Grupo de recursos**, seleccione **SpeechEchoBotTutorial-ResourceGroup**.
   * Asigne un **nombre** al recurso. Se recomienda **SpeechEchoBotTutorial-AppServicePlan**
   * En **Sistema operativo**, seleccione **Windows**.
   * En **Región**, seleccione **Oeste de EE. UU.**
   * En **Plan de tarifa**, asegúrese de que está seleccionado **Estándar S1**. Este debería ser el valor predeterminado.
5. Haga clic en **Revisar y crear**. Debería ver un banner que indica **Validación superada**.
6. Haga clic en **Create**(Crear). La creación del grupo de recursos tardará unos minutos.

En este momento, compruebe que el grupo de recursos (**SpeechEchoBotTutorial-ResourceGroup**) tiene dos recursos:

| NOMBRE | TIPO  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plan de servicio de aplicación | Oeste de EE. UU. |
| SpeechEchoBotTutorial-Speech | Cognitive Services | Oeste de EE. UU. |

## <a name="build-an-echo-bot"></a>Creación de un bot de eco

Ahora que ha creado algunos recursos, vamos a crear un bot. Vamos a empezar con el ejemplo del bot de eco, que, como su nombre implica, muestra el texto que ha escrito como respuesta. No se preocupe, el código de ejemplo está listo para su uso sin precisar ningún cambio. Está configurado para funcionar con el canal Direct Line Speech, al que nos conectaremos después de haber implementado el bot en Azure.

> [!NOTE]
> Las instrucciones siguientes, así como información adicional sobre el bot de eco, están disponibles en el [archivo Léame del ejemplo en GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="download-and-run-the-sample"></a>Descarga y ejecución del ejemplo

1. Clone el repositorio de ejemplos:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Inicie Visual Studio.
3. En la barra de herramientas, seleccione **Archivo** > **Abrir** > **Proyecto o solución** y abra el archivo de proyecto del bot de eco que se ha configurado para usarse con el canal Direct Line Speech:

   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```

4. Una vez cargado el proyecto, presione `F5` para ejecutar el proyecto.

### <a name="test-with-the-bot-framework-emulator"></a>Prueba con Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) es una aplicación de escritorio que permite que los desarrolladores de bots prueben y depuren sus bots de manera local o remota mediante un túnel. Siga estos pasos para usar Bot Framework Emulator para probar el bot de eco.

1. Instalación de [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versión 4.3.0 o superior
2. Inicie Bot Framework Emulator y abra el bot:
   * **Archivo** -> **Open Bot** (Abrir bot).
3. Escriba la dirección URL del bot. Por ejemplo:

   ```
   http://localhost:3978/api/messages
   ```

4. Use la interfaz de usuario para comunicarse con el bot mediante el texto escrito. Confirme que obtiene una respuesta.

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Implementación del bot en Azure App Service

El siguiente paso es implementar el bot de eco en Azure. Hay varias maneras de implementar un bot, pero en este tutorial nos centraremos en publicarlo directamente desde Visual Studio.

> [!NOTE]
> También, puede implementar un bot con la [CLI de Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) y las [plantillas de implementación](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. En Visual Studio, abra el bot de eco que se ha configurado para su uso con el canal Direct Line Speech:

   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la solución **EchoBot** y seleccione **Publicar...**
1. Se abrirá una nueva ventana denominada **Elegir un destino de publicación**.
1. Seleccione **App Service** en el panel de navegación izquierdo, seleccione **Crear nuevo** y haga clic en **Publicar**.
1. Cuando aparece la ventana **Crear servicio de aplicaciones**:
   * Haga clic en **Agregar una cuenta** e inicie sesión con las credenciales de la cuenta de Azure. Si ya ha iniciado sesión, seleccione la cuenta que desee en la lista desplegable.
   * Para el **nombre de la aplicación**, deberá escribir un nombre único global para el bot. Este nombre se usa para crear una dirección URL única del bot. Se rellenará un valor predeterminado que incluye la fecha y la hora (por ejemplo: "EchoBot20190805125647"). En este tutorial, utilice los valores predeterminados.
   * En **Suscripción**, establézcala en **Evaluación gratuita**.
   * En **Grupo de recursos**, seleccione **SpeechEchoBotTutorial-ResourceGroup**.
   * En **Plan de hospedaje**, seleccione **SpeechEchoBotTutorial-AppServicePlan**.
1. Haga clic en **Crear**
1. Debería ver un mensaje de operación correcta en Visual Studio que tiene el siguiente aspecto:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Debería abrirse el explorador predeterminado y mostrar una página que diga: "El bot está listo".
1. En este punto, compruebe el grupo de recursos **SpeechEchoBotTutorial-ResourceGroup** en Azure Portal y confirme que hay tres recursos:

| NOMBRE | TIPO  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Oeste de EE. UU. |
| SpeechEchoBotTutorial-AppServicePlan | Plan de App Service | Oeste de EE. UU. |
| SpeechEchoBotTutorial-Speech | Cognitive Services | Oeste de EE. UU. |

## <a name="enable-web-sockets"></a>Habilitación de sockets web

Tendrá que hacer un pequeño cambio de configuración para que el bot pueda comunicarse con el canal Direct Line Speech mediante sockets web. Siga estos pasos para habilitar los sockets web:

1. Vaya a [Azure Portal](https://portal.azure.com) y busque la instancia de App Service. El recurso debe tener un nombre similar a **EchoBot20190805125647** (el nombre único de la aplicación).
2. En el panel de navegación izquierdo, seleccione **Configuración** y, después, haga clic en **Configuración**.
3. Seleccione la pestaña **Configuración general**.
4. Busque el botón de alternancia para **Sockets web** y establézcalo en **Activado**.
5. Haga clic en **Save**(Guardar).

> [!TIP]
> Puede usar los controles que se encuentran en la parte superior de la página de Azure App Service para detener o reiniciar el servicio. Esto puede resultar útil para solucionar problemas.

## <a name="create-a-channel-registration"></a>Creación de un registro de canal

Ahora que ha creado una instancia de Azure App Service para hospedar el bot, el siguiente paso es crear un **registro de canales de bot**. La creación de un registro de canal es un requisito previo para registrar el bot con canales de Bot-Framework, incluido el canal Direct Line Speech.

> [!NOTE]
> Para más información sobre cómo los bots aprovechan los canales, consulte [Conexión de un bot a canales](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. El primer paso consiste en crear un nuevo recurso para el registro. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
2. En la barra de búsqueda, escriba **bot**; después de que aparezcan los resultados, seleccione **Bot Channels Registration** (Registro de canales de bot).
3. Haga clic en **Create**(Crear).
4. Se le pedirá que proporcione algo de información:
   * En **Nombre del bot**, escriba **SpeechEchoBotTutorial-BotRegistration**.
   * En **Suscripción**, seleccione **Evaluación gratuita**.
   * En **Grupo de recursos**, seleccione **SpeechEchoBotTutorial-ResourceGroup**.
   * En **Ubicación**, seleccione **Oeste de EE. UU.**
     * En **Plan de tarifa**, seleccione **F0**.
     * En **Messaging endpoint** (Punto de conexión de mensajería), escriba la dirección URL de la aplicación web con la ruta de acceso `/api/messages` anexada al final. Por ejemplo, si el nombre de la aplicación globalmente único era **EchoBot20190805125647**, el punto de conexión de mensajería sería `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * En **Application Insights**, puede establecerlo en **Desconectar**. Para más información, consulte [Análisis del bot](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignore **Auto create App ID and password** (Creación automática del identificador y contraseña de la aplicación).
5. Vuelva a **Bot Channels Registration** (Registro de canales de bot) y haga clic en **Crear**.

En este punto, compruebe el grupo de recursos **SpeechEchoBotTutorial-ResourceGroup** en Azure Portal. Ahora debería mostrar cuatro recursos:

| NOMBRE | TIPO  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Oeste de EE. UU. |
| SpeechEchoBotTutorial-AppServicePlan | Plan de App Service | Oeste de EE. UU. |
| SpeechEchoBotTutorial-BotRegistration | Registro de canales de bot | Global |
| SpeechEchoBotTutorial-Speech | Cognitive Services | Oeste de EE. UU. |

> [!IMPORTANT]
> El recurso del registro de canales de bot mostrará la región global aunque haya seleccionado Oeste de EE. UU. Se espera que esto sea así.

## <a name="register-the-direct-line-speech-channel"></a>Registro del canal Direct Line Speech

Ahora es el momento de registrar el bot con el canal Direct Line Speech. Este canal es lo que se usa para crear una conexión entre el bot de eco y una aplicación cliente compilada con el SDK de voz.

1. Busque y abra el recurso **SpeechEchoBotTutorial-BotRegistration** en [Azure Portal](https://portal.azure.com).
1. En el panel de navegación izquierdo, seleccione **Canales**.
   * Busque **Más canales**, busque y haga clic en **Direct Line Speech**.
   * Revise el texto de la página titulada **Configurar Direct Line Speech** y, a continuación, expanda el menú desplegable con la etiqueta de la cuenta de servicio de Cognitive.
   * Seleccione el recurso de voz que creó anteriormente (por ejemplo, **SpeechEchoBotTutorial-Speech**) en el menú para asociar el bot a la clave de suscripción de voz.

1. En la barra de navegación izquierda, haga clic en **Configuración**.
   * Active la casilla de verificación **Enable Streaming Endpoint** (Habilitar punto de conexión de streaming). Esto es necesario para habilitar un protocolo de comunicación basado en sockets web entre el bot y el canal Direct Line Speech.
   * Haga clic en **Save**(Guardar).

> [!TIP]
> Si quiere más información, consulte [Conexión de un bot a Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). En esta página se incluye información adicional así como problemas conocidos.

## <a name="build-the-direct-line-speech-client"></a>Creación del cliente de Direct Line Speech

En este paso, va a crear el cliente de Direct Line Speech. El cliente es una aplicación de Windows Presentation Foundation (WPF) en C# que usa el [SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) para administrar la comunicación con el bot mediante el canal Direct Line Speech. Úselo para interactuar con el bot y probarlo antes de escribir una aplicación cliente personalizada.

El cliente de Direct Line Speech tiene una interfaz de usuario simple que le permite configurar la conexión al bot, ver la conversación de texto, ver las actividades de Bot-Framework en formato JSON y mostrar las tarjetas adaptables. También admite el uso de palabras clave personalizadas. Va a utilizar este cliente para hablar con el bot y recibir una respuesta de voz.

Antes de continuar, asegúrese de que el micrófono y los altavoces estén habilitados y en perfecto funcionamiento.

1. Vaya al repositorio de GitHub para buscar el [cliente de Direct Line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Siga las instrucciones proporcionadas para clonar el repositorio, compilar el proyecto, configurar el cliente e iniciarlo.
3. Haga clic en **Reconnect** (Volver a conectar) y asegúrese de que ve el mensaje **Press the mic button, or type to start talking to your bot** (Presione el botón MIC o escriba para empezar a hablar con el bot).
4. Vamos a probarlo. Haga clic en el botón de micrófono y diga algunas palabras en inglés. El texto reconocido aparecerá mientras habla. Cuando haya terminado de hablar, el bot responderá en su propia voz, diciendo "echo" (eco) seguido de las palabras reconocidas.
5. También puede utilizar el texto para comunicarse con el bot. Simplemente escriba el texto en la barra inferior. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Solución de errores en el cliente de Direct Line Speech

Si recibe un mensaje de error en la ventana principal de la aplicación, use esta tabla para identificarlo y solucionarlo:

| Error | ¿Qué debería hacer? |
|-------|----------------------|
|App Error (see log for details): Microsoft.CognitiveServices.Speech.csharp: Value cannot be null. Parameter name: speechConfig [Error de aplicación (ver el registro para más detalles): Nombe de parámetro: speechConfig] | Se trata de un error de la aplicación cliente. Asegúrese de que tiene un valor no vacío para el *secreto del bot* en la ventana principal de la aplicación (consulte la sección [Registro del bot con el canal Direct Line Speech](#register-the-direct-line-speech-channel)). |
|Error AuthenticationFailure: WebSocket Upgrade failed with an authentication error (401). Check for correct subscription key (or authorization token) and region name [Error AuthenticationFailure: No se pudo actualizar el socket web con un error de autenticación (401)]| En la página Configuración de la aplicación, asegúrese de que ha especificado correctamente la clave de suscripción de voz y su región.<br>Asegúrese de que el secreto del bot se ha escrito correctamente. |
|Error ConnectionFailure: Connection was closed by the remote host. Código de error: 1011. Detalles del error: We could not connect to the bot before sending a message (Error ConnectionFailure: El host remoto cerró la conexión. Código de error: 1011. Detalles del error: No se pudo conectar al bot antes de enviar un mensaje) | Asegúrese de [activar la casilla "Enable Streaming Endpoint"](#register-the-direct-line-speech-channel) (Habilitar el punto de conexión de streaming) o de [alternar los **sockets web**](#enable-web-sockets) en Activado.<br>Asegúrese de que la instancia de Azure App Service se ejecuta. Si es así, pruebe a reiniciar la instancia de App Service.|
|Error ConnectionFailure: Connection was closed by the remote host. Código de error: 1011. Detalles del error: Response status code does not indicate success: 500 (InternalServerError) [Error ConnectionFailure: El host remoto cerró la conexión. Código de error: 1011. Detalles del error: El código de estado de respuesta no indica una operación correcta]| El bot especificó una voz neuronal en el campo [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) (Hablar) de salida, pero la región de Azure asociada a la clave de suscripción de voz no es compatible con las voces neuronales. Consulte [Voces estándares y neuronales](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Error ConnectionFailure: Connection was closed by the remote host. Código de error: 1000. Detalles del error: Exceeded maximum web socket connection idle duration(> 300000 ms) [Error ConnectionFailure: El host remoto cerró la conexión. Código de error: 1000. Detalles del error: Se superó la duración máxima de inactividad de la conexión de socket web (> 300000 MS)]| Este es un error esperado cuando una conexión al canal se deja abierta y está inactiva durante más de cinco minutos. |

Si el problema no se soluciona en la tabla, consulte [Asistentes de voz: Frequently asked questions](faq-voice-assistants.md) (inicio de sesión único de conexión directa de Azure Active Directory: preguntas más frecuentes).

### <a name="view-bot-activities"></a>Ver actividades de bot

Cada bot envía y recibe mensajes de **actividad**. En la ventana **Activity Log** (Registro de actividad) del cliente de Direct line Speech, verá los registros con marca de tiempo con cada actividad en la que el cliente ha recibido del bot. También puede ver las actividades que el cliente ha enviado al bot mediante el método [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync). Al seleccionar un elemento de registro, se mostrarán los detalles de la actividad asociada como JSON.

Este es un ejemplo de JSON de una actividad que el cliente ha recibido:
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Para obtener más información sobre lo que se devuelve en la salida JSON, consulte los [campos de la actividad](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Para este tutorial, puede centrarse en los campos [Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) (Texto) y [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) (Hablar).

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Visualización del código fuente de cliente para las llamadas al SDK de voz

El cliente de Direct Line Speech usa el paquete NuGet [Microsoft.CognitiveServices Account.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), que contiene el SDK de voz. Un buen lugar para empezar a revisar el código de ejemplo es el método InitSpeechConnector() en el archivo [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), que crea estos dos objetos del SDK de voz:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig): para los valores de configuración (clave de suscripción de voz, región de la clave, secreto del bot)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor): para administrar la conexión del canal y los eventos de la suscripción de cliente para controlar las respuestas de voz y bot reconocidas.

## <a name="add-custom-keyword-activation"></a>Agregar la activación de la palabra clave personalizada

El SDK de Voz admite la activación de palabras clave personalizadas. De forma similar a "Hola Cortana" del ayudante de Microsoft, puede escribir una aplicación que escuche continuamente la palabra clave que prefiera. Tenga en cuenta que una palabra clave puede ser una sola palabra o una frase con varias palabras.

> [!NOTE]
> El término *palabra clave* se suele usar indistintamente con el término *palabra de reactivación*, y verá que ambos se usan en la documentación de Microsoft.

La detección de la palabra clave se realiza en la aplicación cliente. Si se usa una palabra clave, el audio solo se transmite al canal de Direct Line Speech si se detecta dicha palabra clave. El canal Direct Line Speech incluye un componente denominado *comprobación de palabras clave (KWV)* , que realiza un procesamiento más complejo en la nube para comprobar que la palabra clave que ha elegido está al principio de la secuencia de audio. Si la comprobación de palabras clave se realiza correctamente, el canal se comunicará con el bot.

Siga estos pasos para crear un modelo de palabra clave, configurar el cliente de Direct Line Speech para usar este modelo y, por último, probarlo con el bot.

1. Siga estas instrucciones para [crear una palabra clave personalizada mediante el servicio Voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Descomprima el archivo de modelo que descargó en el paso anterior. Debe tener el nombre de la palabra clave. Está buscando un archivo denominado `kws.table`.
3. En el cliente de Direct Line Speech, busque el menú de **configuración** (busque el icono de engranaje en la parte superior derecha). Busque la **ruta de acceso del archivo de modelo** y escriba el nombre de la ruta de acceso completa del archivo `kws.table` del paso 2.
4. Asegúrese de activar la casilla denominada **Enabled** (Habilitada). Debería ver este mensaje junto a la casilla: "Will listen for the keyword upon next connection" (Escuchará la palabra clave después de la siguiente conexión). Si ha proporcionado un archivo incorrecto o una ruta de acceso no válida, debería ver un mensaje de error.
5. Escriba la **clave de suscripción** de voz, la **región de clave de suscripción** y, a continuación, haga clic en **Aceptar** para cerrar el menú de **configuración**.
6. Seleccione un **secreto del bot** y, después, haga clic en **Reconnect** (Volver a conectar). Verá un mensaje que indica lo siguiente: "New conversation started - type, press the microphone button, or say the keyword" (Nueva conversación iniciada: escriba, presione el botón de micrófono o la palabra clave). La aplicación ahora escucha continuamente.
7. Diga cualquier frase que empiece con la palabra clave. Por ejemplo: " **[su palabra clave]** , what time is it?". No es necesario hacer una pausa después de pronunciar la palabra clave. Cuando termine, sucederán dos cosas:
   * Verá una transcripción de lo que ha hablado.
   * Poco después, oirá la respuesta del bot.
8. Continúe experimentando con los tres tipos de entradas que admite el bot:
   * Escribir texto en la barra inferior
   * Presionar el icono del micrófono y hablar
   * Decir una frase que empiece con la palabra clave

### <a name="view-the-source-code-that-enables-keyword"></a>Ver el código fuente que habilita la palabra clave

En el código fuente de Direct Line Client, eche un vistazo a estos archivos para revisar el código que se usa para habilitar la detección de la palabra clave:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) incluye una llamada al método [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-) del SDK de voz, que se usa para crear una instancia del modelo a partir de un archivo local en el disco.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) incluye una llamada al método [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) del SDK de Voz, que activa la detección continua de la palabra clave.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>(Opcional) Cambio del idioma y nueva implementación del bot

El bot que ha creado escuchará y responderá en inglés. Sin embargo, no está limitado a usar el idioma inglés. En esta sección, aprenderá a cambiar el idioma en el que el bot escuchará y responderá, y volverá a implementar el bot.

### <a name="change-the-language"></a>Cambio del idioma

1. Comencemos por abrir `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`.
2. A continuación, busque el SSML. Es fácil de encontrar, ya que se incluye en las etiquetas `<speak></speak>`.
3. En la cadena SSML, busque la etiqueta `<voice name>`, reemplácela por `<voice name='de-DE-Stefan-Apollo'>` y guárdela. Esta cadena con formato indica al servicio de conversión de texto a voz que devuelva una respuesta de voz sintetizada mediante la voz `de-DE-Stefan-Apollo`, que está optimizada para alemán.

>[!NOTE]
> No está limitado al alemán y puede elegir otro idioma en la lista de voces disponibles del [servicio de voz](language-support.md#text-to-speech).

### <a name="redeploy-your-bot"></a>Reimplementación del bot

Ahora que ha realizado el cambio necesario en el bot, el paso siguiente consiste en volver a publicarlo en la instancia de Azure App Service y probarlo:

1. Compile la solución en Visual Studio y corrija los errores de compilación.
2. En la ventana Explorador de soluciones, haga clic con el botón derecho en el proyecto **EchoBot** y seleccione **Publicar**.
3. La configuración de implementación anterior ya se ha cargado como valor predeterminado. Simplemente haga clic en **Publicar** junto a **EchoBot20190805125647 - Web Deploy**.
4. El mensaje **Publicación correcta** aparecerá en la ventana de salida de Visual Studio y se iniciará una página web con el mensaje "Your bot is ready!" (El bot está listo).
5. Abra la aplicación cliente de Direct Line Speech, haga clic en el botón de configuración (icono de engranaje en la parte superior derecha) y escriba `de-de` en el campo Language (Idioma). Esto establece el idioma hablado para que se reconozca y se sustituya el valor predeterminado `en-us`.
6. Siga las instrucciones de [Creación del cliente de Direct Line Speech](#build-the-direct-line-speech-client) para volver a conectar con el bot recién implementado, hablar en el nuevo idioma y escuchar la respuesta del bot en ese idioma con la nueva voz.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando el bot eco que ha implementado en este tutorial, puede quitarlo y todos los recursos de Azure asociados simplemente eliminando el grupo de recursos de Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. En [Azure Portal](https://portal.azure.com), seleccione **Grupos de recursos** en el panel de navegación izquierdo.
2. Busque el grupo de recursos denominado: **SpeechEchoBotTutorial-ResourceGroup**. Haga clic en los tres puntos (...).
3. Seleccione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de la propia aplicación cliente con el SDK de voz](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Otras referencias

* Implementación en una [región de Azure próxima](https://azure.microsoft.com/global-infrastructure/locations/) para ver la mejora del tiempo de respuesta de bot
* Implementación en una [región de Azure que admite voces TTS neuronales de alta calidad](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Precios asociados al canal Direct Line Speech:
  * [Precios de Bot Service](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Servicios de Voz](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Creación e implementación del propio bot habilitado para voz:
  * Creación de un [bot de Bot-Framework](https://dev.botframework.com/). Registro con el [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) y [personalización del bot para voz](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Exploración de las [soluciones de Bot-Framework](https://github.com/microsoft/botframework-solutions) existentes: Creación de un [asistente de voz personalizado](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-assistants) y [habilitarlo para voz](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md)
