---
title: 'Inicio rápido: Obtención de respuestas de la base de conocimiento en QnA Maker con REST y Java'
titleSuffix: Azure Cognitive Services
description: Esta guía de inicio rápido basada en REST de Java le guiará a través del proceso para obtener una respuesta de una base de conocimiento mediante programación.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 2b888a275b08c7011c6e0b60ff1cd1d70b42f465
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559840"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Obtención de respuestas a una pregunta desde una base de conocimiento con Java

Esta guía de inicio rápido le conduce por la obtención mediante programación de una respuesta de una base de conocimiento de QnA Maker publicada. La base de conocimiento contiene preguntas y respuestas de [orígenes de datos](../Concepts/data-sources-supported.md), como preguntas frecuentes. La [pregunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se envía al servicio QnA Maker. La [respuesta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) incluye la respuesta prevista en primer lugar. 

## <a name="prerequisites"></a>Requisitos previos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de desarrollo de Java, Standard Edition)
* Este ejemplo utiliza el [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) Apache desde HTTP Components. Es preciso agregar las siguientes bibliotecas de cliente HTTP Apache al proyecto: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Debe tener un [servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar la clave, seleccione **Claves** en **Administración de recursos** en el panel de Azure del recurso de QnA Maker. 
* Configuración de la página **Publicar**. Si no tiene una base de conocimiento publicada, cree una vacía, importe una base de conocimiento en la página **Configuración** y, después, publíquela. Puede descargar y usar [esta base de conocimiento básica](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    La configuración de la página de publicación incluye el valor de la ruta de POST, el valor de Host y el valor de EndpointKey. 

    ![Configuración Publicar](../media/qnamaker-quickstart-get-answer/publish-settings.png)

El código de esta guía de inicio rápido se encuentra en el repositorio [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-java-file"></a>Creación de un archivo Java

Abra VSCode y cree un nuevo archivo denominado `GetAnswer.java` y agregue la siguiente clase:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

Esta guía de inicio rápido usa clases de Apache para las solicitudes HTTP. En la clase GetAnswer, al principio del archivo `GetAnswer.java`, agregue las dependencias necesarias al proyecto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Incorporación de las constantes necesarias

Al principio de la clase `GetAnswer.java`, agregue las constantes necesarias para acceder a QnA Maker. Estos valores se encuentran en la página **Publicar** después de publicar la base de conocimiento.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Incorporación de una solicitud POST para enviar una pregunta

El código siguiente realiza una solicitud HTTPS a QnA Maker API para enviar la pregunta a la base de conocimiento y recibe la respuesta:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

El valor del encabezado `Authorization` incluye la cadena `EndpointKey`. 

Más información sobre la [solicitud](../how-to/metadata-generateanswer-usage.md#generateanswer-request) y la [respuesta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar y ejecutar el programa

Compile y ejecute el programa desde la línea de comandos. Este enviará automáticamente la solicitud a QnA Maker API y luego la imprimirá en la ventana de la consola.

1. Compile el archivo:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Ejecute el archivo:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Referencia de QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
