---
title: Desarrollo de aplicaciones para reproductor de vídeo
description: El tema también proporciona vínculos a Player Framework y complementos que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465650"
---
# <a name="develop-video-player-applications"></a>Desarrollo de aplicaciones para reproductor de vídeo
## <a name="overview"></a>Información general
Azure Media Services proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes). Este tema también proporciona vínculos a los SDK y Player Framework que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Azure Media Services.

>[!NOTE]
>Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) es un reproductor de vídeo web integrado para reproducir contenido multimedia de Microsoft Azure Media Services en una gran variedad de exploradores y dispositivos. Explorador multimedia de Azure utiliza los estándares del sector, como HTML5, Media Source Extensions (MSE, extensiones de origen multimedia) y Encrypted Media Extensions (EME, extensiones multimedia cifradas) para proporcionar una experiencia de streaming adaptativa enriquecida. Cuando estos estándares no están disponibles en un dispositivo o en un explorador, Azure Media Player usa Flash y Silverlight como tecnología de reserva. Independientemente de la tecnología de reproducción que se usa, los desarrolladores tendrán una interfaz unificada de JavaScript para tener acceso a las API. Esto permite que el contenido proporcionado por Azure Media Services se reproduzca a través de una amplia gama de dispositivos y exploradores sin ningún esfuerzo adicional.

Microsoft Azure Media Services permite ofrecer contenido con formatos de streaming DASH, Smooth Streaming y HLS para reproducir contenido. Azure Media Player tiene en cuenta estos diversos formatos y reproduce automáticamente el vínculo mejor según las capacidades del explorador y plataforma. Microsoft Azure Media Services también permite el cifrado dinámico de recursos con el cifrado PlayReady o el cifrado de sellado de AES de 128 bits. Azure Media Player permite el descifrado del contenido cifrado de AES de 128 bits y PlayReady cuando se configura correctamente. 

Para obtener más información:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Documentación de Azure Media Player](https://aka.ms/ampdocs) 
* [Blog de introducción a Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Suscríbase para mantenerse al día con la versión más reciente de Azure Media Player](https://aka.ms/ampsignup)
* [Agregue nuevas solicitudes, ideas y comentarios](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Otras herramientas para crear aplicaciones del reproductor
También puede usar algunos de los SDK siguientes:

* [SDK de cliente de Smooth Streaming](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Aplicación de la Tienda Windows de Smooth Streaming](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [Documentación de HTML5 Player Framework](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Complemento Microsoft Smooth Streaming para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencias de kit de migración de cliente de Microsoft® Smooth Streaming](https://aka.ms/sspk) 
* [Desarrollo de aplicaciones de vídeo de XBOX](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Publicidad
Azure Media Services admite la inserción de anuncios mediante la plataforma de Windows Media: Player Frameworks. Player framework con compatibilidad con anuncios está disponible para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada marco de trabajo de reproductor contiene código de ejemplo que muestra cómo implementar una aplicación de reproductor. Hay tres tipos diferentes de anuncios que se pueden insertar en el archivo multimedia:

Lineales: anuncios en un marco completo que pausan el vídeo principal

No lineales: anuncios superpuestos que se muestran mientras se reproduce el vídeo principal, normalmente un logotipo u otra imagen estática colocada dentro del reproductor

Complementarios: anuncios que se muestran fuera del reproductor

Los anuncios se pueden colocar en cualquier punto de la línea de tiempo del vídeo principal. Debe indicar al reproductor cuándo reproducir el anuncio y qué anuncios reproducir. Esto se realiza con un conjunto de archivos XML estándar: Video Ad Service Template (VAST, plantilla de servicio de anuncio de vídeo), Digital Video Multiple Ad Playlist (VMAP, lista de reproducción de varios anuncios de vídeo digital), Media Abstract Sequencing Template (MAST, plantilla de secuenciación abstracta multimedia) y Digital Video Player Ad Interface Definition (VPAID, definición de interfaz de anuncios del reproductor de vídeo digital). Los archivos VAST especifican qué anuncios mostrar. Los archivos VMAP especifican cuándo reproducir varios anuncios y contienen XML VAST. Los archivos MAST constituyen otra manera de secuenciar los anuncios que también pueden contener XML VAST. Los archivos VPAID definen una interfaz entre el reproductor de vídeo y el anuncio o el servidor de anuncios. Para obtener más información, vea [Inserción de anuncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obtener información sobre la compatibilidad con anuncios y subtítulos en vídeos de streaming en vivo, vea [Estándares de inserción de anuncios y subtítulos compatibles](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vea también
[Incrustación de un vídeo de transmisión por secuencias adaptativa MPEG-DASH en una aplicación HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositorio dash.js de GitHub](https://github.com/Dash-Industry-Forum/dash.js)

