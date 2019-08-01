---
title: 'Inicio rápido: Creación de la base de conocimiento en QnA Maker con REST y Go'
titleSuffix: Azure Cognitive Services
description: Esta guía de inicio rápido basada en REST para Go le lleva por la creación de una base de conocimiento de QnA Maker de ejemplo mediante programación, que aparecerá en el panel de Azure de su cuenta de la API de Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a6ee10c998af564630ae1aefd01ec2f4e360abf8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563049"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Inicio rápido: Creación de una base de conocimiento en QnA Maker mediante Go

Esta guía de inicio rápido describe la creación mediante programación de una base de conocimiento de QnA Maker de ejemplo. QnA Maker extrae automáticamente preguntas y respuestas a partir de contenido semiestructurado, como las preguntas frecuentes, y de [orígenes de datos](../Concepts/data-sources-supported.md). El modelo para la base de conocimiento se define en el JSON que se envía en el cuerpo de la solicitud de API. 

En esta guía de inicio rápido se llama a las siguientes API de QnA Maker:
* [Creación de una base de conocimiento](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obtención de los detalles de la operación](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Requisitos previos

* [Go 1.10.1](https://golang.org/dl/)
* Debe tener un [servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar la clave, seleccione **Claves** en **Administración de recursos** en el panel. 

## <a name="create-a-knowledge-base-go-file"></a>Creación de un archivo de Go de base de conocimiento

Cree un archivo llamado `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

En la parte superior de `create-new-knowledge-base.go`, agregue las líneas siguientes para agregar las dependencias necesarias al proyecto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Incorporación de las constantes necesarias
Después de las dependencias necesarias anteriores, agregue las constantes necesarias para acceder a QnA Maker. Sustituya el valor de la variable `subscriptionKey` por su propia clave de QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Incorporación de la definición del modelo de la base de conocimiento
Después de las constantes, agregue la siguiente definición de la base de conocimiento. El modelo se convertirá en una cadena después de la definición.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Incorporación de estructuras y funciones auxiliares

A continuación, agregue las siguientes funciones auxiliares.

1. Agregue la estructura de una solicitud HTTP:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Agregue el siguiente método para controlar una solicitud POST a las API de QnA Maker. En esta guía de inicio rápido, POST se usa para enviar la definición de base de conocimiento a QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Agregue el siguiente método para controlar una solicitud GET a las API de QnA Maker. En esta guía de inicio rápido, la solicitud GET se usa para comprobar el estado de la operación de creación. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Incorporación de la función para crear una base de conocimiento

Agregue las siguientes funciones para realizar una solicitud HTTP POST a fin de crear la base de conocimiento. El **identificador de la operación** _create_ se devuelve en el campo **Location** del encabezado de la respuesta POST y luego se usa como parte de la ruta en la solicitud GET. `Ocp-Apim-Subscription-Key` es la clave del servicio QnA Maker, usada para la autenticación. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

Esta llamada API devuelve una respuesta JSON que incluye el identificador de operación. Use el identificador de operación para determinar si se ha creado correctamente la base de conocimiento. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Incorporación de una función para obtener el estado

Agregue la siguiente función para realizar una solicitud HTTP GET para comprobar el estado de la operación. `Ocp-Apim-Subscription-Key` es la clave del servicio QnA Maker, usada para la autenticación. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

Repita la llamada hasta obtener éxito o error: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```
## <a name="add-main-function"></a>Incorporación de una función principal

La función siguiente es la función principal y permite crear la base de conocimiento y repetir las comprobaciones del estado. Dado que la creación de la base de conocimiento puede tardar algún tiempo, deberá repetir las llamadas para comprobar el estado hasta que sea correcto o se produzca un error.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Compilación del programa
Escriba el siguiente comando para compilar el archivo. Si la compilación es correcta, el símbolo del sistema no devuelve ninguna información.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Ejecución del programa

Escriba el siguiente comando en una línea de comandos para ejecutar el programa. La solicitud se envía a QnA Maker API para crear la base de conocimiento y, luego, se sondean los resultados cada 30 segundos. Cada respuesta se imprime en la ventana de consola.

```bash
go run create-new-knowledge-base
```

Una vez creada la base de conocimiento, puede verla en el portal de QnA Maker en la página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mis bases de conocimiento). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Referencia de QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
