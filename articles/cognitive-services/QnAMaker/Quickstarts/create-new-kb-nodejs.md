---
title: 'Guía de inicio rápido: Creación de una base conocimiento: REST y Node.js en QnA Maker'
description: En esta guía de inicio rápido se explica de forma detallada cómo crear mediante programación un ejemplo de base de conocimiento de QnA Maker que aparecerá en el panel de Azure de su cuenta de API de Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 02ef954ba19b7d750f1b76c2e4f8643ed74cc82b
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254125"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Guía de inicio rápido: Creación de una base de conocimiento en QnA Maker mediante Node.js

Esta guía de inicio rápido le lleva por la creación y la publicación de una base de conocimiento de QnA Maker de ejemplo mediante programación. QnA Maker extrae automáticamente preguntas y respuestas a partir de contenido semiestructurado, como las preguntas frecuentes, y de [orígenes de datos](../Concepts/data-sources-supported.md). El modelo para la base de conocimiento se define en el JSON que se envía en el cuerpo de la solicitud de API. 

En esta guía de inicio rápido se llama a las siguientes API de QnA Maker:
* [Creación de una base de conocimiento](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obtención de los detalles de la operación](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Requisitos previos

* [Node.js 6+](https://nodejs.org/en/download/)
* Debe tener un [servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar la clave, seleccione **Claves** en **Administración de recursos** en el panel. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Creación de un archivo de base de conocimiento de Node.js

Cree un archivo llamado `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

En la parte superior de `create-new-knowledge-base.js`, agregue las líneas siguientes para agregar las dependencias necesarias al proyecto:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Incorporación de las constantes necesarias
Después de las dependencias necesarias anteriores, agregue las constantes necesarias para acceder a QnA Maker. Sustituya el valor de la variable `subscriptionKey` por su propia clave de QnA Maker.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Incorporación de la definición del modelo de la base de conocimiento

Después de las constantes, agregue la siguiente definición de la base de conocimiento. El modelo se convertirá en una cadena después de la definición.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Incorporación de funciones auxiliares

A continuación, agregue las siguientes funciones auxiliares.

1. Agregue la siguiente función para imprimir el código JSON en un formato legible:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Agregue las siguientes funciones para administrar la respuesta HTTP:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Incorporación de funciones para crear una base de conocimiento

Agregue las siguientes funciones para realizar una solicitud HTTP POST a fin de crear la base de conocimiento. `Ocp-Apim-Subscription-Key` es la clave del servicio QnA Maker, usada para la autenticación. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

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

## <a name="add-functions-to-determine-creation-status"></a>Incorporación de funciones para determinar el estado de creación

Agregue la siguiente función para realizar una solicitud HTTP GET para comprobar el estado de la operación. `Ocp-Apim-Subscription-Key` es la clave del servicio QnA Maker, usada para la autenticación. 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

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


## <a name="add-create-kb-function"></a>Incorporación de la función create-kb

La función siguiente es la función principal y permite crear la base de conocimiento y repetir las comprobaciones del estado. El **identificador de la operación** _create_ se devuelve en el campo **Location** del encabezado de la respuesta POST y luego se usa como parte de la ruta en la solicitud GET. Dado que la creación de la base de conocimiento puede tardar algún tiempo, deberá repetir las llamadas para comprobar el estado hasta que sea correcto o se produzca un error.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Ejecución del programa

Escriba el siguiente comando en una línea de comandos para ejecutar el programa. La solicitud se envía a QnA Maker API para crear la base de conocimiento y, luego, se sondean los resultados cada 30 segundos. Cada respuesta se imprime en la ventana de consola.

```bash
node create-new-knowledge-base.js
```

Una vez creada la base de conocimiento, puede verla en el portal de QnA Maker en la página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mis bases de conocimiento). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Referencia de QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
