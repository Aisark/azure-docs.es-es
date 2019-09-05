---
title: Migración de Windows Azure Media Encoder a Media Encoder Standard | Microsoft Docs
description: En este tema se describe cómo migrar del procesador de multimedia de Azure Media Encoder al de Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 2e9008d039f014c95d473f3197b48651bdaa5a45
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019324"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migración de Windows Azure Media Encoder a Media Encoder Standard

En este artículo se describen los pasos para migrar del procesador de multimedia heredado de Windows Azure Media Encoder (WAME), que se retirará el 30 de noviembre de 2019, al procesador de multimedia de Media Encoder Standard.

Al codificar archivos con WAME, los clientes suelen usar una cadena preestablecida con nombre como, por ejemplo, `H264 Adaptive Bitrate MP4 Set 1080p`. Para realizar la migración, se debe actualizar el código para que utilice el procesador de multimedia de **Media Encoder Standard** en lugar de WAME y uno de los [valores preestablecidos del sistema](media-services-mes-presets-overview.md) equivalentes como `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migración a Media Encoder Standard

A continuación se muestra un ejemplo de código de C# típico que usa el componente heredado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Esta es la versión actualizada que usa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Escenarios avanzados 

Si ha creado su propio valor preestablecido de codificación para WAME mediante su esquema, hay un [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md). Si tiene alguna pregunta sobre cómo asignar la configuración anterior al nuevo codificador, póngase en contacto con nosotros en mailto:amshelp@microsoft.com  

## <a name="known-differences"></a>Diferencias conocidas 

Media Encoder Standard es más sólido, confiable, tiene un mejor rendimiento y genera una salida de mejor calidad que el codificador WAME heredado. Además: 

* Media Encoder Standard genera archivos de salida con una convención de nomenclatura diferente de la de WAME.
* Media Encoder Standard genera artefactos, como archivos, que contienen los [metadatos de archivos de entrada](media-services-input-metadata-schema.md) y los [metadatos de archivos de salida](media-services-output-metadata-schema.md).
* Tal como se documenta en la [Página de precios](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente en la sección de preguntas más frecuentes), al codificar vídeos mediante Media Encoder Standard, se le facturará en función de la duración de los archivos generados de salida. Con WAME, se le facturará en función de los tamaños de los archivos de vídeo de entrada y de salida.

## <a name="next-steps"></a>Pasos siguientes

* [Componentes heredados](legacy-components.md)
* [Página de precios](https://azure.microsoft.com/pricing/details/media-services/#encoding)
