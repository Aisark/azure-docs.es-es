---
title: Codificadores de streaming en vivo recomendados por Media Services en Azure | Microsoft Docs
description: Información sobre los codificadores locales de streaming en vivo recomendados por Media Services
services: media-services
keywords: encoding;encoders;media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268193"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>Codificadores de streaming en vivo locales recomendados

En Azure Media Services, un [evento en directo](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa una canalización para procesar contenido de streaming en vivo. El evento en directo recibe las secuencias de entrada en vivo de una de estas dos formas:

* Un codificador en directo local envía una secuencia RTMP o Smooth Streaming (MP4 fragmentado) de velocidad de bits múltiple al canal Evento en directo que no está habilitado para realizar la codificación en vivo con Media Services. Las secuencias recopiladas pasan a través de Eventos en directo sin más procesamiento. Este método se llama **paso a través**. Se recomienda que el codificador en directo envíe transmisiones de múltiples velocidades de bits en lugar de una transmisión de una sola velocidad de bits a un evento en directo de paso a través para permitir el streaming con velocidad de bits adaptable al cliente. 

    Si usa transmisiones de múltiples velocidades de bits para el evento en directo de paso a través, el tamaño del GOP de vídeo y los fragmentos de vídeo con diferentes velocidades de bits deben estar sincronizados para evitar un comportamiento inesperado en el lado de reproducción.

  > [!NOTE]
  > El método de paso a través es la forma más económica de realizar un streaming en vivo.
 
* Un codificador en directo local envía una secuencia de velocidad de bits única al Evento en directo que está habilitado para realizar la codificación en vivo con Media Services, con uno de los siguientes formatos: RTMP o Smooth Streaming (MP4 fragmentado). Después, el Evento en directo codifica en vivo la secuencia entrante de velocidad de bits única en una secuencia de vídeo de velocidad de bits múltiple (adaptable).

Para obtener información detallada sobre la codificación en vivo con Media Services, vea [Streaming en vivo con Azure Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisitos del codificador

Los codificadores deben admitir TLS 1.2 al usar protocolos HTTPS o RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores en directo que generan una salida RTMP

Media Services recomienda usar uno de los siguientes codificadores en directo que tienen RTMP como salida. Los esquemas URL admitidos son `rtmp://` o `rtmps://`.

Al hacer el streaming mediante RTMP, compruebe la configuración del firewall o del proxy para confirmar que los puertos TCP salientes 1935 y 1936 están abiertos.<br/><br/>
Al hacer el streaming mediante RTMPS, compruebe la configuración del firewall o del proxy para confirmar que los puertos TCP salientes 2935 y 2936 están abiertos.

> [!NOTE]
> Los codificadores deben admitir TLS 1.2 al usar protocolos RTMP.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versión 2.14.15 y posteriores)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (versión 13.0.2 o posterior debido al requisito de TLS 1.2)
- Telestream Wirecast S (solo se admite RTMP)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 y Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores en directo que generan una salida de MP4 fragmentado

Media Services recomienda usar uno de los codificadores en directo siguientes que tienen Smooth Streaming de velocidad de bits múltiple (MP4 fragmentado) como salida. Los esquemas URL admitidos son `http://` o `https://`.

> [!NOTE]
> Los codificadores deben admitir TLS 1.2 al usar protocolos HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versión 2.14.15 y posterior debido al requisito de TLS 1.2)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live y Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Si está transmitiendo eventos en directo en varios idiomas (por ejemplo, una pista de audio en inglés y una en español), puede hacerlo con el codificador en directo de elementos multimedia de Excel configurado para enviar la fuente en directo a un evento en directo de paso a través.

## <a name="configuring-on-premises-live-encoder-settings"></a>Configuración de los valores del codificador en directo local

Para información acerca de qué valores son válidos para el tipo de evento en directo, consulte [Comparación de tipos de objetos LiveEvent](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reproducción

Para volver a reproducir un contenido, debe haber una secuencia de audio y una secuencia de vídeo. No se admite la reproducción de la secuencia solo de vídeo.

### <a name="configuration-tips"></a>Sugerencias de configuración

- Siempre que sea posible, use una conexión a Internet por cable.
- Cuando tenga que determinar los requisitos de ancho de banda, duplique las velocidades de bits de streaming. Aunque no es obligatorio, esta sencilla regla ayuda a mitigar el impacto de la congestión de la red.
- Cuando se usen codificadores por software, cierre todos los programas innecesarios.
- Si se modifica la configuración del codificador una vez iniciada la inserción, se producirán efectos negativos en el evento. Los cambios de configuración pueden provocar que el evento se vuelva inestable. 
- Asegúrese de dejar tiempo suficiente para configurar el evento. En el caso de los eventos a gran escala, se recomienda iniciar la configuración una hora antes del evento.

## <a name="see-also"></a>Consulte también

[Conviértase en un asociado de codificador local](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Pasos siguientes

[Streaming en vivo con Media Services v3](live-streaming-overview.md)
