---
title: 'Inicio rápido: Cómo familiarizarse con Content Moderator: Content Moderator'
titleSuffix: Azure Cognitive Services
description: En este tutorial, usará la herramienta de revisión en línea de Content Moderator para probar las funcionalidades básicas de Content Moderator sin tener que escribir ningún código.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb4fc076d01c1108278cea0cebba958b4ea94660
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044049"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Inicio rápido: Cómo familiarizarse con Content Moderator

En este tutorial, usará la herramienta de revisión en línea de Content Moderator para probar las funcionalidades básicas de Content Moderator sin tener que escribir ningún código. Si desea integrar este servicio a su aplicación más rápidamente, vea las otras guías de inicio rápido en la sección [Pasos siguientes](#next-steps).

## <a name="prerequisites"></a>Requisitos previos

- Un explorador web

## <a name="set-up-the-review-tool"></a>Configuración de la herramienta de revisión
La herramienta de revisión Content Moderator es una herramienta basada en web que permite a los usuarios revisores ayudar a tomar decisiones a la instancia de Cognitive Services. En esta guía, recorrerá el breve proceso de configuración de la herramienta de revisión para que pueda ver cómo funciona el servicio de Content Moderator. Vaya al sitio de la [herramienta de revisión de Content Moderator](https://contentmoderator.cognitive.microsoft.com/) y regístrese.

![Página principal de Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Creación de un equipo de revisión

A continuación, cree un equipo de revisión. En un escenario de trabajo, será el grupo de personas que va a revisar manualmente las decisiones de moderación del servicio. Por ahora, basta con crear el nombre del equipo. Si quiere invitar colegas a su equipo, puede escribir sus direcciones de correo electrónico aquí.

![Invitar a miembros del equipo](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Carga del contenido de ejemplo

Ahora está listo para cargar el contenido de ejemplo. Seleccione **Probar > Imagen**, **Probar > Texto** o **Probar > Vídeo**.

![Probar moderación de imagen o texto](images/tryimagesortext.png)

Envíe el contenido para moderación. Internamente, la herramienta de revisión llamará a las API de moderación para examinar el contenido. Cuando haya finalizado el análisis, verá un mensaje que le informa de la presencia de resultados a la espera de que los revise.

![Moderar archivos](images/submitted.png)

## <a name="review-moderation-tags"></a>Revisión de las etiquetas de moderación

Revise las etiquetas de moderación aplicadas. Puede ver qué etiquetas se aplicaron a su contenido y cuál fue la puntuación de cada categoría. Consulte los temas de moderación de [Imagen](image-moderation-api.md), [Texto](text-moderation-api.md) y [Vídeo](video-moderation-api.md) para obtener información sobre qué indican las diferentes etiquetas de contenido.

![Revisión de los resultados](images/reviewresults_text.png)

En un proyecto, usted o su equipo de revisión pueden cambiar estas etiquetas o agregar más según sea necesario. Los cambios se envían con el botón **Siguiente**. Dado que la aplicación empresarial llama a las API de Moderator, el contenido etiquetado se apilará en la cola y estará listo para que los equipos de usuarios revisores lo examinen. Con este enfoque, puede revisar rápidamente grandes volúmenes de contenido.

Hasta ahora, ha usado la herramienta de revisión Content Moderator para ver ejemplos de lo que puede hacer el servicio Content Moderator. A continuación, puede obtener más información acerca de la herramienta de revisión y cómo integrarla en un proyecto de software mediante las API de revisión, o puede ir a la sección [Pasos siguientes](#next-steps) para obtener información sobre cómo usar las API de moderación en la aplicación.

## <a name="learn-more-about-the-review-tool"></a>Más información sobre la herramienta de revisión

Para obtener más información sobre cómo usar la herramienta de revisión de Content Moderator, eche un vistazo a la guía [Herramienta de revisión](Review-Tool-User-Guide/human-in-the-loop.md) y examine las API de la herramienta de revisión para aprender a ajustar la experiencia de revisión humana:
- La [API de trabajos](try-review-api-job.md) examina el contenido con las API de moderación y genera las revisiones en la herramienta de revisión. 
- La [API de revisiones](try-review-api-review.md) crea directamente revisiones de imágenes, texto o vídeos para moderadores humanos sin examinar antes el contenido. 
- La [API de flujos de trabajo](try-review-api-workflow.md) crea, actualiza y obtiene información detallada sobre los flujos de trabajo personalizados que crea el equipo.

O bien continúe con los pasos siguientes para empezar a usar las API de moderación en su código.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar las API de moderación en su aplicación.
- Implementación de la moderación de imágenes. Use la [consola de API](try-image-api.md) o siga el [inicio rápido del SDK de .NET](dotnet-sdk-quickstart.md) para examinar imágenes y detectar posible contenido explícito y para adultos mediante etiquetas, puntuaciones de confianza y otra información extraída.
- Implementación de la moderación de texto. Use la [consola de API](try-text-api.md) o el [inicio rápido del SDK de .NET](dotnet-sdk-quickstart.md) para examinar el contenido de texto con el fin de buscar posibles palabras soeces, clasificación de texto no deseado asistida por máquina (versión preliminar) y datos personales.
- Implementación de la moderación de vídeo. Consulte la [guía de procedimientos de moderación en vídeo para C#](video-moderation-api.md) para buscar vídeos y detectar posible contenido para adultos y subido de tono. 
