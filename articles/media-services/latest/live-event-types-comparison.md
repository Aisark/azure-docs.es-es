---
title: Tipos de LiveEvent en Azure Media Services | Microsoft Docs
description: En este artículo aparece una tabla detallada que compara los tipos de LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322291"
---
# <a name="live-event-types-comparison"></a>Comparación de tipos de objetos LiveEvent

En Azure Media Services, los objetos [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) pueden ser de uno de estos dos tipos: codificación en directo y paso a través. 

## <a name="types-comparison"></a>Comparación de tipos 

En la tabla siguiente se comparan las características de dos tipos de LiveEvent.

| Característica | LiveEvent de paso a través | LiveEvent estándar |
| --- | --- | --- |
| La entrada de velocidad de bits única se codifica en varias velocidades de bits en la nube |Sin  |Sí |
| Resolución de vídeo máxima para la fuente de contribución |4 K (4096 x 2160 en 60 fotogramas por segundo) |1080 p (1920 x 1088 en 30 fotogramas por segundo)|
| Niveles máximos recomendados en la fuente de contribución|Hasta 12|Un audio|
| Número máximo de niveles en la salida| Igual que la entrada|Hasta 6 (consulte los valores preestablecidos del sistema a continuación)|
| Ancho de banda agregado máximo de la fuente de contribución|60 Mbps|N/D|
| Velocidad de bits máxima para una sola capa de la contribución |20 Mbps|20 Mbps|
| Compatibilidad con pistas de audio de varios idiomas|Sí|Sin |
| Códecs compatibles de vídeo de entrada |H.264/AVC y H.265/HEVC|H.264/AVC|
| Códecs compatibles de vídeo de salida|Igual que la entrada|H.264/AVC|
| Profundidad de bits, entrada y salida de vídeo compatibles|Hasta 10 bits incluido HDR 10/HLG|8 bits|
| Códecs de audio de entrada compatibles|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Códecs de audio de salida compatibles|Igual que la entrada|AAC-LC|
| Resolución máxima de vídeo para el vídeo de salida|Igual que la entrada|720p (a 30 fotogramas/segundo)|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Precio|Consulte la [página de precios](https://azure.microsoft.com/pricing/details/media-services/) y haga clic en la pestaña "Vídeo en vivo"|Consulte la [página de precios](https://azure.microsoft.com/pricing/details/media-services/) y haga clic en la pestaña "Vídeo en vivo"|
| Tiempo de ejecución máximo| 24 horas x 365 días, lineal en directo | Hasta 24 horas|
| Posibilidad de pasar a través de los subtítulos CEA 608/708 insertados|Sí|Sí|
| Compatibilidad con inserción de tabletas táctiles|Sin |Sin |
| Compatibilidad con señalización de anuncios mediante API| Sin |Sin |
| Compatibilidad con los mensajes de señalización de anuncios mediante SCTE-35 en banda|Sí|Sí|
| Capacidad para la recuperación de breves pausas en la fuente de contribución|Sí|No (LiveEvent comenzará a usar la tableta táctil transcurridos más de 6 segundos sin datos de entrada)|
| Compatibilidad con GOP de entrada no uniformes|Sí|No: la entrada debe tener una duración fija del grupo de imágenes|
| Compatibilidad con la entrada de la velocidad de fotogramas variable|Sí|No: la entrada debe ser una velocidad de fotogramas fija. Se tolerarán pequeñas variaciones, por ejemplo, durante las escenas con grandes movimientos. Pero la fuente de contribución no puede reducir la velocidad de fotogramas (por ejemplo, a 15 fotogramas/segundo).|
| Apagado automático de LiveEvent cuando se pierde la fuente de entrada|Sin |Después de 12 horas, si no hay ningún objeto LiveEvent en ejecución|

## <a name="system-presets"></a>Valores preestablecidos del sistema

Cuando se usa la codificación en directo (el evento LiveEvent se establece en **Standard**), la codificación preestablecida define cómo se codifica la transmisión entrante en varias velocidades de bits o capas. Actualmente, el único valor permitido para el valor predeterminado es *Default720p* (valor predeterminado).

**Default720p** codificará el vídeo en las 6 capas siguientes.

### <a name="output-video-stream"></a>Secuencia de vídeo de salida

| Velocidad de bits | Ancho | Alto | Fotogramas/seg. máx. | Perfil | Nombre secuencia salida |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alto |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alto |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alto |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alto |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alto |Video_340x192_200kbps |

> [!NOTE]
> Si necesita usar un valor preestablecido personalizado para la codificación en directo, póngase en contacto con amshelp@microsoft.com. Debe especificar la tabla de resoluciones y velocidades de bits deseadas. Compruebe que hay solo una capa a 720p y, como máximo, 6 capas.

### <a name="output-audio-stream"></a>Secuencia de audio de salida

El audio se codifica como estéreo AAC-LC a 128 kbps, con una frecuencia de muestreo de 48 kHz.

## <a name="next-steps"></a>Pasos siguientes

[Introducción al streaming en vivo](live-streaming-overview.md)
