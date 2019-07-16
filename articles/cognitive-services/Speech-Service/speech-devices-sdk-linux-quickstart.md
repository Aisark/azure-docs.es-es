---
title: 'Inicio rápido: Ejecución de Speech Devices SDK en Linux: Servicios de Voz'
titleSuffix: Azure Cognitive Services
description: Requisitos previos e instrucciones para comenzar a usar Speech Devices SDK de Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: d755f3388466369ee1edc3d9ff1e353173babc10
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723304"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Inicio rápido: Ejecución de la aplicación de ejemplo de Speech Devices SDK en Linux

En este inicio rápido, aprenderá a usar Speech Devices SDK para Linux para crear un producto habilitado para voz o para utilizarlo como un dispositivo de [transcripción de conversaciones](conversation-transcription-service.md). Actualmente, solo se admite [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/).

La aplicación se crea con el paquete de Speech SDK y el IDE de Java de Eclipse (v4) en Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Se ejecuta en un entorno de tiempo de ejecución de Java 8 (JRE) de 64 bits.

Para esta guía se requiere una cuenta de [Azure Cognitive Services](get-started.md) con un recurso de los servicios de Voz. Si no tiene una cuenta, puede usar la [evaluación gratuita](https://azure.microsoft.com/try/cognitive-services/) para obtener una clave de suscripción.

El código fuente de la [aplicación de ejemplo](https://aka.ms/sdsdk-download-JRE) se incluye con Speech Devices SDK. También está [disponible en GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Requisitos previos

Esta guía de inicio rápido requiere:

* Sistema operativo: Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [IDE de Java de Eclipse](https://www.eclipse.org/downloads/)
* Solo [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Una clave de suscripción de Azure para el servicio Voz. [Obtenga una gratis](get-started.md).
* Descargue la versión más reciente de [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) para Java y extraiga el archivo .zip en el directorio de trabajo.
   > [!NOTE]
   > El archivo JRE-Sample-Release.zip incluye la aplicación de ejemplo de JRE y en este inicio rápido se da por supuesto que ha extraído la aplicación en /home/wcaltest/JRE-Sample-Release.

Asegúrese de que estén instaladas estas dependencias antes de iniciar Eclipse.

* En Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* En Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Transcripción de conversaciones solo está disponible actualmente para "en-US" y "zh-CN", en las regiones "centralus" y "eastasia". Debe tener una clave de voz en una de esas regiones para usar Transcripción de conversaciones.

Si tiene previsto usar intenciones necesitará una suscripción a [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Para más información acerca de LUIS y el reconocimiento de intenciones, consulte [Reconocimiento de las intenciones de voz con LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Hay un [modelo de ejemplo de LUIS](https://aka.ms/sdsdk-luis) disponible para esta aplicación.

## <a name="create-and-configure-the-project"></a>Creación y configuración del proyecto

1. Inicie Eclipse.

1. En el **selector de IDE de Eclipse**, en el campo **Workspace** (Área de trabajo), escriba el nombre de un nuevo directorio de área de trabajo. Luego, seleccione **Launch** (Iniciar).

   ![Captura de pantalla del selector de Eclipse](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Al cabo de unos segundos, aparece la ventana principal del IDE de Eclipse. Cierre la pantalla de bienvenida si hay alguna.

1. En la barra de menús de Eclipse, cree un nuevo proyecto eligiendo **File (Archivo)**  > **New (Nuevo)**  > **Java Project (Proyecto de Java)** . Si no está disponible seleccione **Project** (Proyecto) y, a continuación, **Java Project** (Proyecto de Java).

1. Se inicia el asistente para **nuevo proyecto de Java**. **Busque** la ubicación del proyecto de ejemplo. Seleccione **Finalizar**.

   ![Captura de pantalla del asistente de nuevo proyecto de Java](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. En el **Explorador de paquetes**, haga clic con el botón derecho en el proyecto. Elija **Configure (Configurar)**  > **Convert to Maven Project (Convertir en proyecto de Maven)** en el menú contextual. Seleccione **Finalizar**.

   ![Captura de pantalla del Explorador de paquetes](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. En el **Explorador de paquetes**, haga clic con el botón derecho en el proyecto. Elija **Propiedades** y, a continuación, **Configuración de depuración/ejecución** > **Nuevo...** > **Aplicación Java**. 

1. Aparecerá la ventana **Edit configuration** (Editar configuración). En el campo **Nombre** escriba **Principal** y use **Búsqueda** para **Clase principal** para buscar y seleccionar **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Captura de pantalla de Edit Launch Configuration (Editar configuración de inicio)](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. También en la ventana **Edit configuration** (Editar configuración), seleccione la página **Entorno** y **Nuevo**. Aparecerá la ventana **New Environment Variable** (Nueva variable de entorno). En el campo **Nombre** escriba **LD_LIBRARY_PATH** y en el campo **Valor** especifique la carpeta que contiene los archivos *.so, por ejemplo **/home/wcaltest/JRE-Sample-Release**

1. Copie `kws.table` y `participants.properties` en la carpeta del proyecto **target\classes**


## <a name="configure-the-sample-application"></a>Configurar la aplicación de ejemplo

1. Agregue la clave de suscripción de Voz al código fuente. Si quiere probar el reconocimiento de intenciones, agregue también la clave de suscripción y el identificador de aplicación del [servicio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

   Para Voz y LUIS, la información se trasladará a `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Si usa la transcripción de conversaciones, la información de la clave y la región de Voz también se necesitará en `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. La palabra de activación predeterminada (palabra clave) es "Equipo". Además, puede probar una de las otras palabras de activación proporcionadas, como "Máquina" o "Asistente". Los archivos de recursos de estas palabras de activación alternativas pueden encontrarse en el SDK de dispositivos de voz en la carpeta de palabras clave. Por ejemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contiene los archivos utilizados para la palabra de reactivación "Equipo".

   > [!TIP]
   > También puede [crear una palabra de activación personalizada](speech-devices-sdk-create-kws.md).

    Para usar una nueva palabra de reactivación, actualice las dos líneas siguientes en `FunctionsList.java` y copie el paquete de palabras de reactivación en la aplicación. Por ejemplo, para usar la palabra de reactivación "Máquina" desde el paquete de palabras de reactivación `kws-machine.zip`:

   * Copie el paquete de palabras de reactivación en la carpeta de proyecto **target/classes**.

   * Actualice `FunctionsList.java` con la palabra clave y el nombre del paquete:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Ejecución de la aplicación de ejemplo de Eclipse

1. En la barra de menús de Eclipse seleccione, **Run (Ejecutar)**  > **Run (Ejecutar como)** . 

1. Se inicia la aplicación de ejemplo del SDK de dispositivos de voz y muestra las siguientes opciones:

   ![Opciones y aplicación de ejemplo del SDK de dispositivos de voz de ejemplo](media/speech-devices-sdk/java-sample-app-linux.png)

1. Pruebe la nueva demostración de **Transcripción de conversaciones**. Empiece a transcribir con **Sesión** > **Iniciar**. De forma predeterminada, todos los usuarios son invitados. Sin embargo, si dispone de las firmas de voz del participante, estas se pueden colocar en `participants.properties` en la carpeta del proyecto **target/classes**. Para generar la firma de voz, consulte [Transcripción de conversaciones (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplicación de demostración de transcripción de conversaciones](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Creación y ejecución de una aplicación independiente

1. En el **Explorador de paquetes**, haga clic con el botón derecho en el proyecto. Seleccione **Exportar**. 
1. Aparecerá la ventana **Exportar**. Expanda **Java** y seleccione **Runnable JAR file** (Archivo JAR ejecutable) y, a continuación, seleccione **Siguiente**.

   ![Captura de pantalla de la ventana Exportar](media/speech-devices-sdk/eclipse-export-linux.png) 

1. Aparece la ventana **Runnable JAR File Export** (Exportación de archivo JAR ejecutable). Elija un **Destino de exportación** para la aplicación y, a continuación, seleccione **Finalizar**.
 
   ![Captura de pantalla de la ventana Runnable JAR File Export (Exportación de archivo JAR ejecutable)](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Coloque `kws.table` y `participants.properties` en la carpeta de destino elegida anteriormente ya que estos archivos son necesarios para la aplicación.

1. Establezca LD_LIBRARY_LIB en la carpeta que contiene los archivos *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Ejecución de la aplicación independiente

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Revise las notas de la versión.](devices-sdk-release-notes.md)
