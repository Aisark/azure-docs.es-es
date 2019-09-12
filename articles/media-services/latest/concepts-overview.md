---
title: 'Terminología y conceptos de Azure Media Services: Azure | Microsoft Docs'
description: En este tema se ofrece una breve información general sobre la terminología y los conceptos de Azure Media Services y proporciona vínculos para obtener más información.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910312"
---
# <a name="media-services-concepts"></a>Conceptos de Media Services

En este tema se proporciona una breve información general sobre los conceptos de Azure Media Services. En el artículo también se proporcionan vínculos a artículos con una explicación más detallada de los conceptos y la funcionalidad de Media Services v3. 

Deben revisarse los conceptos fundamentales que se describen en estos temas antes de comenzar a desarrollar.

> [!NOTE]
> Actualmente, no puede usar Azure Portal para administrar recursos de v3. Use la [API REST](https://aka.ms/ams-v3-rest-ref), la [CLI](https://aka.ms/ams-v3-cli-ref) o uno de los [SDK](media-services-apis-overview.md#sdks) admitidos.

## <a name="terminology"></a>Terminología

En esta sección se muestra cómo algunos términos comunes del sector se relacionan con la API de Media Services v3.

### <a name="live-event"></a>Evento en directo

Un **evento en directo** representa una canalización para ingerir, transcodificar (opcionalmente) y empaquetar secuencias en directo de metadatos de audio, vídeo y tiempo real.

En el caso de los clientes que migran desde las API de Media Services v2, la entidad **Live Event** reemplaza a la entidad **Channel** en la versión 2. Para más información, consulte [Migración de v2 a v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Punto de conexión de streaming (empaquetado y origen)

Un **punto de conexión de streaming** representa un empaquetado dinámico (just-in-time) y el servicio de origen que puede entregar directamente el contenido en directo y a petición a una aplicación de reproducción de cliente, mediante uno de los protocolos de streaming de multimedia (HLS o DASH). Además, el **punto de conexión de streaming** proporciona cifrado dinámico (Just-In-Time) para los sistemas de DRM líderes del sector.

En el sector del streaming multimedia, este servicio se conoce comúnmente como **empaquetador** u **origen**.  Otros términos comunes en la industria para esta funcionalidad incluyen JITP (Just-in-time-packager) o JITE (Just-in-time-encryption). 
 
## <a name="cloud-upload-and-storage"></a>Carga y almacenamiento en la nube

Para iniciar la administración, el cifrado, la codificación, el análisis y el streaming de contenido multimedia en Azure, debe crear una cuenta de Media Services y cargar los archivos digitales en **recursos**.

- [Carga y almacenamiento en la nube](storage-account-concept.md)
- [Concepto de recursos](assets-concept.md)

## <a name="encoding"></a>Encoding

Cuando cargue los archivos multimedia digitales de alta calidad en recursos, puede codificarlos en formatos que se pueden reproducir en una gran variedad de exploradores y dispositivos. 

Para codificar con Media Services v3, debe crear una **transformación** y un **trabajo**.

![Transformaciones](./media/encoding/transforms-jobs.png)

- [Transformaciones y trabajos](transforms-jobs-concept.md)
- [Codificación con Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Media Analytics

Para analizar los archivos de audio y vídeo, también deberá crear **transformaciones** y **trabajos**.

- [Análisis de archivos de audio y vídeo](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empaquetado, entrega, protección

Cuando el contenido está codificado, puede sacar partido del **empaquetado dinámico**. En Media Services, un **punto de conexión de streaming**/origen es el servicio de empaquetado dinámico que se utiliza para entregar contenido multimedia a los reproductores del cliente. Para que los vídeos en el recurso de salida estén disponibles para los clientes para reproducción, tiene que crear un **localizador de streaming** y, después, generar direcciones URL de streaming. 

Al crear un **localizador de streaming**, debe especificar el nombre del recurso y el nombre de la **directiva de streaming**. Las **directivas de streaming** permiten definir los protocolos de streaming y las opciones de cifrado (si hubiera) de los **localizadores de streaming**.

El empaquetado dinámico se utiliza si se transmite el contenido en directo o a petición. En el siguiente diagrama se muestra el flujo de trabajo para streaming a petición con empaquetado dinámico.

![Empaquetado dinámico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Con Media Services puede entregar el contenido cifrado de forma dinámica en vivo y a petición con el Estándar de cifrado avanzado (AES-128) o cualquiera de los tres sistemas de administración de derechos digitales (DRM) principales: Microsoft PlayReady, Google Widevine y Apple FairPlay. Media Services también proporciona un servicio para entregar claves AES y licencias de DMR (PlayReady, Widevine y FairPlay) a los clientes autorizados.

Si se especifican opciones de cifrado en la secuencia, cree la **directiva de clave de contenido** y asóciela con el **localizador de streaming**. La **directiva de clave de contenido** permite configurar cómo se entrega la clave de contenido a los clientes finales.

En la siguiente imagen se ilustra el flujo de trabajo de protección de contenido de Media Services: 

![Proteger contenido](./media/content-protection/content-protection.svg)

&#42; El cifrado dinámico es compatible con la "clave sin cifrado" de AES-128, CBCS y CENC. 

Puede usar los **manifiestos dinámicos** de Media Services para transmitir solo una copia específica o subclips del vídeo. En el ejemplo siguiente, se usó un codificador para codificar un recurso intermedio en siete representaciones de vídeo MP4 ISO (de 180p a 1080p). El recurso codificado puede empaquetarse dinámicamente en cualquiera de los siguientes protocolos de transmisión: HLS, MPEG DASH y Smooth.  En la parte superior del diagrama, se muestra el manifiesto HLS para el activo sin filtros (contiene las siete representaciones).  En la parte inferior izquierda, se muestra el manifiesto HLS al que se aplicó un filtro denominado "ott". El filtro de "ott" especifica la eliminación de todas las velocidades de bits por debajo de 1 Mbps, lo que dio lugar a que se quitaran los dos niveles de calidad inferiores en la respuesta. En la parte inferior derecha se muestra el manifiesto HLS al que se aplicó un filtro denominado "móvil". El filtro "móvil" especifica la eliminación de las representaciones donde la resolución es mayor que 720p, lo que hizo que se quitaran las dos representaciones de 1080p.

![Filtrado de representaciones](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Empaquetado dinámico](dynamic-packaging-overview.md)
- [Puntos de conexión de streaming](streaming-endpoint-concept.md)
- [Localizadores de streaming](streaming-locators-concept.md)
- [Directivas de streaming](streaming-policy-concept.md)
- [Directivas de claves de contenido](content-key-policy-concept.md)
- [Protección de contenido](content-protection-overview.md)
- [Manifiestos dinámicos](filters-dynamic-manifest-overview.md)
- [Filtros](filters-concept.md)

## <a name="live-streaming"></a>Streaming en directo

Azure Media Services permite entregar eventos en directo a sus clientes en la nube de Azure. Los objetos **LiveEvents** son responsables de la ingesta y el procesamiento de las fuentes de vídeo en directo. Al crear un objeto **Live Event**, se crea un punto de conexión de entrada que puede utilizar para enviar una señal en directo desde un codificador remoto. Una vez que la secuencia fluye en el **evento en directo**, puede comenzar el evento de streaming mediante la creación de un **recurso**, una **salida en vivo** y un **localizador de streaming**. La **salida en vivo** archivará la secuencia en el **recurso** y la pondrá a disposición de los usuarios mediante el **punto de conexión de streaming**. Un objeto **Live Event** puede ser de uno de estos dos tipos: **paso a través** y **codificación en directo**.

En la imagen siguiente se muestra el flujo de trabajo de tipo de paso a través:

![paso a través](./media/live-streaming/pass-through.svg)

- [Introducción al streaming en vivo](live-streaming-overview.md)
- [Objetos LiveEvent y LiveOutput](live-events-outputs-concept.md)

## <a name="monitoring"></a>Supervisión

### <a name="event-grid"></a>Event Grid

Para ver el progreso del trabajo, debe usar **Event Grid**. Media Services también emite los tipos de eventos en directo. Con Event Grid, sus aplicaciones pueden escuchar y reaccionar a eventos de casi todos los servicios de Azure y de orígenes personalizados. 

- [Control de eventos de Event Grid](reacting-to-media-services-events.md)
- [Esquemas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Supervise las métricas y los registros de diagnóstico que le ayudan a comprender el rendimiento de sus aplicaciones con Azure Monitor.

- [Métricas y registros de diagnóstico](media-services-metrics-diagnostic-logs.md)
- [Esquemas de registros de diagnóstico](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clientes de Player

Puede usar Azure Media Player para reproducir contenido multimedia que se transmite por Media Services en una gran variedad de exploradores y dispositivos. Explorador multimedia de Azure utiliza los estándares del sector, como HTML5, Media Source Extensions (MSE, extensiones de origen multimedia) y Encrypted Media Extensions (EME, extensiones multimedia cifradas) para proporcionar una experiencia de streaming adaptativa enriquecida. 

- [Información general sobre Azure Media Player](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="next-steps"></a>Pasos siguientes

* [Codificación de archivos remotos y streaming de vídeo: REST](stream-files-tutorial-with-rest.md)
* [Codificación de archivos cargados y streaming de vídeo: .NET](stream-files-tutorial-with-api.md)
* [Streaming en vivo: .NET](stream-live-tutorial-with-api.md)
* [Análisis del vídeo: .NET](analyze-videos-tutorial-with-api.md)
* [Cifrado dinámico AES-128: .NET](protect-with-aes128.md)
* [Cifrado dinámico con varias soluciones de DRM: .NET](protect-with-drm.md) 
