---
title: 'Inicio rápido: QnA Maker con API REST para Node.js'
titleSuffix: Azure Cognitive Services
description: Introducción a las API REST de QnA Maker para Node.js. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas.  QnA Maker le permite alimentar un servicio de preguntas y respuestas con su contenido semiestructurado, como documentos de preguntas frecuentes, direcciones URL y manuales de productos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: ad7986a0c4b0d59322ccebcaa6b1c70776164c48
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015707"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Inicio rápido: API REST de QnA Maker para Node.js

Introducción a las API REST de QnA Maker para Node.js. Siga estos pasos para probar el código de ejemplo para realizar tareas básicas.  QnA Maker le permite alimentar un servicio de preguntas y respuestas con su contenido semiestructurado, como documentos de preguntas frecuentes, direcciones URL y manuales de productos. 

Use las API REST de QnA Maker para Node.js para:

* Creación de una base de conocimientos
* Reemplazo de una base de conocimiento
* Publicación de una base de conocimientos
* Eliminación de una base de conocimiento
* Descarga de una base de conocimiento
* Obtención del estado de una operación

[Documentación de referencia](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Ejemplos de Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
* La versión actual de [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Instalación

### <a name="create-a-qna-maker-azure-resource"></a>Creación de un recurso de Azure en QnA Maker

Los servicios de Azure Cognitive Services se representan por medio de recursos de Azure a los que se suscribe. Cree un recurso para QnA Maker mediante [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o la [CLI de Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) en la máquina local. 

Después de obtener una clave del recurso, [cree las variables de entorno](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para el recurso, llamadas `QNAMAKER_RESOURCE_KEY` y `QNAMAKER_AUTHORING_ENDPOINT`. Use la clave y los valores de host que se encuentran en la página **Inicio rápido** del recurso en Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Creación de una aplicación Node.js

En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para la aplicación y vaya a él. 

```console
mkdir myapp && cd myapp
```

Ejecute el comando `npm init -y` para crear un archivo `package.json` del nodo. 

```console
npm init -y
```

Agregue los paquetes NPM `reqeuestretry` y `request`:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código muestran cómo realizar las siguientes acciones con las API REST de QnA Maker para Node.js:

* [Creación de una base de conocimientos](#create-a-knowledge-base)
* [Reemplazo de una base de conocimiento](#replace-a-knowledge-base)
* [Publicación de una base de conocimientos](#publish-a-knowledge-base)
* [Eliminación de una base de conocimiento](#delete-a-knowledge-base)
* [Descarga de una base de conocimiento](#download-the-knowledge-base)
* [Obtención del estado de una operación](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adición de las dependencias



Cree un archivo denominado `rest-apis.js` y agregue la siguiente instrucción _requires_ para crear solicitudes HTTP. 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Adición de información de recursos de Azure

Cree variables para el punto de conexión y la clave de Azure del recurso. Si ha creado la variable de entorno después de haber iniciado la aplicación, deberá cerrar y volver a abrir el editor, el IDE o el shell que lo ejecuta para acceder a la variable.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Creación de una base de conocimientos

Una base de conocimiento almacena pares de preguntas y respuestas, creados a partir de un objeto JSON de:

* **Contenido editorial**. 
* **Archivos**: archivos locales que no requieren permisos. 
* **Direcciones URL**: direcciones URL disponibles públicamente.

Use la [API REST para crear una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Reemplazo de una base de conocimiento

Use la [API REST para reemplazar una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publicación de una base de conocimientos

Publicar la base de conocimiento. Este proceso hace que la base de conocimiento esté disponible desde cualquier punto de conexión de predicción de consulta HTTP.

Use la [API REST para publicar una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Descarga de la base de conocimiento 

Use la [API REST para descargar una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Eliminación de una base de conocimiento

Cuando haya terminado de usar la base de conocimiento, elimínela.

Use la [API REST para eliminar una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obtención del estado de una operación

Los procesos de larga duración, como el proceso de creación, devuelven un identificador de operación, que debe comprobarse con una llamada a una API REST independiente. Esta función toma el cuerpo de la respuesta de creación. La clave importante es `operationState`, que determina si es necesario seguir realizando el sondeo.

Use la [API REST para supervisar las operaciones en una base de conocimiento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación con el comando `node rest-apis.js` desde el directorio de la aplicación.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI de Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Tutorial: Creación y respuesta desde una base de conocimiento](../tutorials/create-publish-query-in-portal.md)

* [¿Qué es QnA Maker API?](../Overview/overview.md)
* [Editar una base de conocimiento](../how-to/edit-knowledge-base.md)
* [Obtención de análisis de uso](../how-to/get-analytics-knowledge-base.md)
* El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).