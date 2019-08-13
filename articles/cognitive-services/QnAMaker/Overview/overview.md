---
title: ¿Qué es el servicio QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker es un servicio NLP API basado en la nube que crea con facilidad una capa natural de conversación con los datos. Se puede usar para encontrar la respuesta más apropiada para una entrada de lenguaje natural determinada, desde la base de conocimiento personalizada (KB) de información.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
ms.openlocfilehash: f56798359cdc8739a363bed3bfddadd584617adf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815505"
---
# <a name="what-is-the-qna-maker-service"></a>¿Qué es el servicio QnA Maker?

QnA Maker es un servicio de Proceso de lenguaje natural (NLP) basado en la nube que crea con facilidad una capa natural de conversación con los datos. Se puede usar para encontrar la respuesta más apropiada para una entrada de lenguaje natural determinada, desde la base de conocimiento personalizada (KB) de información.

Una aplicación cliente para QnA Maker es cualquier aplicación conversacional que se comunique con un usuario en lenguaje natural para responder a una pregunta. Entre estas aplicaciones cliente se encuentran, por ejemplo, aplicaciones de redes sociales, bots de chat y aplicaciones de escritorio habilitadas para voz.

## <a name="when-to-use-qna-maker"></a>Cuándo usar QnA Maker

* **Cuando tiene información estática**: use QnA Maker cuando tenga información estática en la base de conocimiento de las respuestas. Esta base de conocimiento está personalizada para sus necesidades, que ha creado con documentos como [archivos PDF y direcciones URL.](../concepts/data-sources-supported.md)
* **Si desea proporcionar la misma respuesta a una solicitud, una pregunta o un comando**: cuando distintos usuarios envían la misma pregunta, se devuelve la misma respuesta a ambos. 
* **Si desea filtrar la información estática en función de la metainformación**: agregue etiquetas de [metadatos](../how-to/metadata-generateanswer-usage.md) para proporcionar opciones de filtrado adicionales relacionadas con los usuarios de la aplicación cliente y la información. La información común de los metadatos incluye el tipo de contenido o formato [de charla](../how-to/chit-chat-knowledge-base.md), y el propósito y la actualización del contenido.
* **Si desea administrar una conversación de bot que incluya información estática**: su conocimiento toma el texto o el comando de conversación de un usuario y lo responde. Si la respuesta forma parte de un flujo de conversación determinado previamente, representado en la base de conocimiento con [contexto multiturno](../how-to/multiturn-conversation.md), el bot puede proporcionar fácilmente este flujo.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Uso de una base de conocimiento de QnA Maker en un bot de chat

Una vez publicada una base de conocimiento de QnA Maker, una aplicación cliente envía una pregunta al punto de conexión de la base de conocimiento y recibe los resultados como una respuesta JSON. Una aplicación cliente común para QnA Maker es un bot de chat.

![Formulación de una pregunta a un bot y respuesta del contenido de la base de conocimiento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Paso|.|
|:--|:--|
|1|La aplicación cliente envía la _pregunta_  del usuario (texto en sus propias palabras) "Cómo actualizar mi base de conocimiento mediante programación?" al punto de conexión de la base de conocimiento.|
|2|QnA Maker usa la base de conocimiento entrenada para proporcionar la respuesta correcta y las solicitudes de seguimiento que se pueden usar para refinar la búsqueda de la mejor respuesta. QnA Maker devuelve una respuesta con formato JSON.|
|3|La aplicación cliente usa la respuesta JSON para tomar decisiones acerca de cómo continuar con la conversación. Estas decisiones pueden incluir mostrar la respuesta principal o presentar más opciones para refinar la búsqueda de la mejor respuesta. |
|||

## <a name="what-is-a-knowledge-base"></a>¿Qué es una base de conocimiento? 

QnA Maker [importa el contenido](../concepts/data-sources-supported.md) en una base de conocimiento de conjuntos de preguntas y respuestas. El proceso de importación extrae información sobre la relación entre las partes del contenido estructurado y semiestructurado para implicar relaciones entre los conjuntos de preguntas y respuestas. Puede editar estos conjuntos de preguntas y respuestas, o agregar otros nuevos.  

El contenido del conjunto de preguntas y respuestas incluye todas las preguntas alternativas para una respuesta concreta, las etiquetas de metadatos utilizadas para filtrar las opciones de respuesta durante la búsqueda y las solicitudes de seguimiento para continuar con el perfeccionamiento de la búsqueda.

![Ejemplo de pregunta y respuesta con metadatos](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Después de publicar la base de conocimiento, una aplicación cliente envía una pregunta de usuario al punto de conexión. El servicio de QnA Maker procesa la pregunta y responde con la mejor respuesta. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Creación, administración y publicación en un bot sin código

En el portal de QnA Maker se proporciona toda la experiencia de creación de la base de conocimiento. Puede importar documentos en su formulario actual a la base de conocimiento. Estos documentos (como las preguntas más frecuentes, el manual del producto, la hoja de cálculo o la página web) se convierten en conjuntos de preguntas y respuestas. Cada conjunto se analiza para los mensajes de seguimiento y se conecta a otros conjuntos. El formato de marcado final admite una presentación enriquecida que incluye imágenes y vínculos. 

Una vez editada la base de conocimiento, publíquela [en un bot de Azure Web App](https://azure.microsoft.com/services/bot-service/) en funcionamiento sin escribir ningún código. Pruebe el bot en [Azure Portal](https://portal.azure.com) o descargue y continúe el desarrollo. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>La calidad de la búsqueda y la clasificación proporcionan la mejor respuesta posible

El sistema de QnA Maker es un enfoque de clasificación por capas. Los datos se almacenan en la búsqueda de Azure, que también actúa como la primera capa de clasificación. Los resultados principales de la búsqueda de Azure se pasan en el modelo de reclasificación NLP de QnA Maker para generar los resultados finales y la puntuación de confianza.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker mejora el proceso de conversación

QnA Maker proporciona solicitudes multiturno y aprendizaje activo para ayudarle a mejorar sus conjuntos de preguntas y respuestas básicos. 

**Los mensajes multiturno** le ofrecen la oportunidad de conectar pares de preguntas y respuestas. Esta conexión permite a la aplicación cliente proporcionar una respuesta superior y proporciona más preguntas para refinar la búsqueda de una respuesta final. 

Una vez que la base de conocimiento recibe las preguntas de los usuarios en el punto de conexión publicado, QnA Maker aplica el **aprendizaje activo** a estas preguntas reales para sugerir cambios en la base de conocimiento y mejorar la calidad. 

## <a name="development-lifecycle"></a>Ciclo de vida del desarrollo

QnA Maker permite la creación, el entrenamiento y la publicación, además de ofrecer permisos de colaboración para integrarse en el ciclo de vida de desarrollo completo. 

## <a name="how-do-i-start"></a>¿Cómo se empieza?

**Paso 1**: cree un recurso de QnA Maker en [Azure Portal](https://portal.azure.com). 

**Paso 2**: cree una base de conocimiento en el portal de [QnA Maker](https://www.qnamaker.ai). Agregue [archivos y direcciones URL](../concepts/data-sources-supported.md)para crear la base de conocimiento.  

**Paso 3**: Publique su base de conocimiento y realice pruebas desde su punto de conexión personalizado con [cURL](../quickstarts/get-answer-from-kb-using-curl.md) o [Postman](../quickstarts/get-answer-from-kb-using-postman.md). 

**Paso 4**: Desde la aplicación cliente, llame al punto de conexión de la base de conocimiento mediante programación y lea la respuesta JSON para mostrar la mejor respuesta al usuario.  

## <a name="news-and-updates"></a>Noticias y actualizaciones

Conozca las novedades de QnA Maker.

* Junio de 2019
    * Modelo de clasificación mejorado para francés, Italiano, alemán, español y portugués
* Abril de 2019
    * Extracción de contenido del sitio web de soporte
    * Compatibilidad con documentos de SharePoint
* Marzo de 2019
    * Aprendizaje activo 
    * Modelo de clasificación NLP mejorado para inglés 

## <a name="next-steps"></a>Pasos siguientes
QnA Maker proporciona todo lo que necesita para compilar, administrar e implementar la base de conocimiento personalizada. 

> [!div class="nextstepaction"]
> [Crear un servicio QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
