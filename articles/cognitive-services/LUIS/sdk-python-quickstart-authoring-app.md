---
title: 'Inicio rápido: Biblioteca cliente de creación de Language Understanding (LUIS) para Python'
titleSuffix: Azure Cognitive Services
description: Empiece a trabajar con la biblioteca cliente de LUIS para Python con este inicio rápido. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 398220306fad4dc3459446fa5f77371ac669dd7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381501"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>Inicio rápido: Biblioteca cliente de creación de Language Understanding (LUIS) para Python

Introducción a la biblioteca cliente de creación de Language Understanding (LUIS) para Python. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas.  Language Understanding (LUIS) permite aplicar inteligencia de aprendizaje automático personalizado a una conversación o un texto de lenguaje natural de un usuario para predecir el significado global y extraer información pertinente y detallada. 

La biblioteca cliente de creación de Language Understanding (LUIS) para Python puede usarse con los siguientes fines:

* Crear una aplicación.
* Agregar intenciones, entidades y expresiones de ejemplo.
* Agregar características, como una lista de frases.
* Entrenar y publicar una aplicación.

[Documentación de referencia](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Paquete de creación (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Ejemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Prerequisites

* Cuenta del portal de Language Understanding (LUIS): [cree una de forma gratuita](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instalación

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtención de la clave de inicio de Language Understanding (LUIS)

Para obtener la [clave de inicio](luis-how-to-azure-subscription.md#starter-key), cree un recurso de creación de LUIS. Conserve la clave y la región de la clave para el siguiente paso.

### <a name="create-an-environment-variable"></a>Creación de una variable de entorno

Con la clave y la región de la clave, cree dos variables de entorno para la autenticación:

* `LUIS_AUTHORING_KEY`: la clave de recurso para autenticar las solicitudes.
* `LUIS_REGION`: es la región asociada a la clave. Por ejemplo, `westus`.

Siga las instrucciones adecuadas para su sistema operativo.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Después de agregar la variable de entorno, reinicie la ventana de la consola.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Después de agregar la variable de entorno, ejecute `source ~/.bashrc` desde la ventana de consola para que los cambios surtan efecto.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite `.bash_profile` y agregue la variable de entorno:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key> 
export LUIS_REGION=<replace-with-your-luis-region>
```

Después de agregar la variable de entorno, ejecute `source .bash_profile` desde la ventana de consola para que los cambios surtan efecto.
***

### <a name="install-the-python-library-for-luis"></a>Instalación de la biblioteca de Python para LUIS

En el directorio de la aplicación, instale la biblioteca cliente de creación de Language Understanding (LUIS) para Python con el siguiente comando:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objetos

El cliente de creación de Language Understanding (LUIS) es un objeto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) que se autentica en Azure, que contiene la clave de creación.

Una vez creado el cliente, úselo para acceder a la funcionalidad, entre la que se incluye:

* Aplicaciones: [creación](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [eliminación](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publicación](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Expresiones de ejemplo: [adición por lote](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [eliminación por identificador](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* Características: administración de [listas de frases](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* Modelo: administración de [intenciones](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) y entidades
* Patrón: administración de [patrones](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Entrenamiento: [entrenamiento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) de la aplicación y sondeo del [estado del entrenamiento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versiones](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python): administración con clonación, exportación y eliminación


## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código muestran cómo realizar las siguientes acciones con la biblioteca cliente de creación de Language Understanding (LUIS) para Python:

* [Creación de una aplicación](#create-a-luis-app)
* [Adición de entidades](#create-entities-for-the-app)
* [Adición de intenciones](#create-intent-for-the-app)
* [Add example utterances](#add-example-utterance-to-intent) (Adición de expresiones de ejemplo)
* [Entrenamiento de la aplicación](#train-the-app)
* [Publicación de la aplicación](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

Cree una aplicación de Python en el IDE o editor que prefiera. A continuación, importe las bibliotecas siguientes. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Cree variables para el punto de conexión y la clave de Azure del recurso. Si ha creado la variable de entorno después de haber iniciado la aplicación, deberá cerrar y volver a abrir el editor, el IDE o el shell que lo ejecuta para acceder a la variable.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Autenticar el cliente

Cree un objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la clave y úselo con el punto de conexión para crear un objeto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python).

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Creación de una aplicación de LUIS

1. Cree una aplicación de LUIS que incluya el modelo de procesamiento de lenguaje natural que contiene intenciones, entidades y expresiones de ejemplo. 

1. Cree el método [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) de un objeto [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) para crear la aplicación. El nombre y la referencia cultural del idioma son propiedades necesarias. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Creación de una intención para la aplicación
El objeto principal en el modelo de una aplicación LUIS es la intención. Esta se alinea con una agrupación de _intenciones_ de expresiones de usuario. Un usuario puede formular una pregunta o realizar una declaración buscando una respuesta _intencionada_ concreta de un bot (u otra aplicación cliente). Ejemplos de intenciones pueden ser la reserva de un vuelo, preguntar sobre el tiempo en una ciudad de destino o pedir la información de contacto del servicio de atención al cliente.   

Use el método [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) con el nombre de la intención única y, después, pase el identificador de la aplicación, el identificador de la versión y el nuevo nombre de la intención. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Creación de entidades para la aplicación

Si bien las entidades no son necesarias, se encuentran en la mayoría de las aplicaciones. La entidad extrae información de la expresión del usuario, necesaria para cumplir la intención del usuario. Hay varios tipos de entidades [precompiladas](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) y personalizadas, cada una con sus propios modelos de objeto de transformación de datos.  Las entidades precompiladas comunes que se pueden agregar a una aplicación incluyen [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md) y [ordinal](luis-reference-prebuilt-ordinal.md). 

Este método **add_entities** crea una entidad simple `Location` con dos roles, una entidad simple `Class`, una entidad compuesta `Flight` y agrega varias entidades precompiladas.

Es importante saber que las entidades no se marcan con una intención. Pueden aplicarse, y así suelen hacerlo, a muchas intenciones. Solo las expresiones de usuario de ejemplo se marcan para una única intención específica.

Los métodos de creación de entidades forman parte de la clase [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Cada tipo de entidad tiene su propio modelo de objeto de transformación de datos (DTO). 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Adición de expresiones de ejemplo a la intención

Con el fin de determinar la intención de una expresión y extraer las entidades, la aplicación necesita ejemplos de expresiones. Los ejemplos deben tener como objetivo una única intención específica y deben marcar todas las entidades personalizadas. No es necesario marcar las entidades precompiladas. 

Para agregar expresiones de ejemplo, cree una lista de objetos [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python), un objeto para cada expresión de ejemplo. Cada ejemplo debe marcar todas las entidades con un diccionario de pares nombre-valor de nombre de entidad y valor de entidad. El valor de la entidad debe reflejar exactamente lo que aparece en el texto de la expresión de ejemplo. 

Llame a [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) con el identificador de la aplicación, el identificador de la versión y la lista de ejemplos. La llamada responde con una lista de resultados. Debe comprobar el resultado de cada ejemplo, para asegurarse de que se ha agregado correctamente al modelo. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>Entrenamiento de la aplicación

Una vez creado el modelo, debe entrenarse la aplicación de LUIS para esta versión del modelo. Un modelo entrenado puede usarse en un [contenedor](luis-container-howto.md) o bien [publicarse](luis-how-to-publish-app.md) en los espacios de ensayo o del producto. 

El método [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) necesita el identificador de la aplicación y el identificador de la versión. 

Un modelo muy pequeño, como el que se muestra en este inicio rápido, se entrenará muy rápidamente. En el caso de las aplicaciones en el nivel de producción, el entrenamiento de la aplicación debe incluir una llamada de sondeo al método [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) para determinar si el entrenamiento se ha realizado correctamente. La respuesta es una lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) con un estado independiente para cada objeto. Para que el entrenamiento se considere completo, todos los objetos deben ser correctos.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publicación de una aplicación de Language Understanding

Publique la aplicación de LUIS con el método [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Así se publica la versión entrenada actual en el espacio especificado en el punto de conexión. La aplicación cliente usa este punto de conexión para enviar expresiones del usuario y con ellas predecir la intención y extraer entidades.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación con el comando `python` en el archivo de inicio rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI de Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Compilación de una aplicación de LUIS para determinar las intenciones del usuario](luis-quickstart-intents-only.md)

* [¿Qué es Language Understanding (LUIS)?](what-is-luis.md)
* [Novedades](whats-new.md)
* [Intenciones](luis-concept-intent.md), [entidades](luis-concept-entity-types.md), [expresiones de ejemplo](luis-concept-utterance.md) y [entidades precompiladas](luis-reference-prebuilt-entities.md)
* El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py).
