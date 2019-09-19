---
title: Introducción a Bing Speech Recognition API con bibliotecas cliente | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utilice las bibliotecas cliente de Bing Speech en Microsoft Cognitive Services para desarrollar aplicaciones que conviertan el audio hablado en texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966845"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Introducción a las bibliotecas de cliente del servicio Bing Speech

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Además de realizar solicitudes HTTP directas por medio de una API REST, el servicio Bing Speech proporciona a los desarrolladores bibliotecas de cliente de Voz en distintos idiomas. Bibliotecas cliente de voz:

- Son compatibles con las funciones más avanzadas de reconocimiento de voz, como los resultados intermedios en tiempo real, el flujo de audio de larga duración (hasta 10 minutos) y el reconocimiento continuado.
- Ofrecen una API simple e idiomática en el idioma de su preferencia.
- Ocultan los detalles de comunicaciones de nivel inferior.

Actualmente, están disponibles las siguientes bibliotecas de cliente de Bing Speech:

- [Biblioteca de escritorio de C#](GetStartedCSharpDesktop.md)
- [Biblioteca de servicios C#](GetStartedCSharpServiceLibrary.md)
- [Biblioteca de JavaScript](GetStartedJSWebsockets.md)
- [Biblioteca de Java para Android](GetStartedJavaAndroid.md)
- [Biblioteca de Objective-C para iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Recursos adicionales

- La página de [ejemplos](../samples.md) proporciona ejemplos completos para utilizar las bibliotecas cliente de voz.
- Si necesita una biblioteca cliente que aún no es compatible, puede crear su propio SDK. Implemente el [protocolo WebSocket de voz](../API-Reference-REST/websocketprotocol.md) en la plataforma y use el idioma de su elección.

## <a name="license"></a>Licencia

Todos los SDK de Cognitive Services y los ejemplos tienen una licencia de MIT. Para más información, consulte [Licencia](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
