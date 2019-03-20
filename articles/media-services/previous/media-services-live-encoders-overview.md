---
title: Configuración de los codificadores locales al usar Azure Media Services para crear transmisiones con velocidad de bits múltiple | Microsoft Docs
description: En este tema se enumeran los codificadores en directo locales que puede utilizar para capturar los eventos en directo y enviar una secuencia en directo con velocidad de bits única a canales AMS (que permiten la codificación en directo) para su posterior procesamiento. El tema establece vínculos a tutoriales que muestran cómo configurar los codificadores que se presentan.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 360b822bc78d353e91612459a70334a0bf3a56e6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189638"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Configuración de los codificadores locales al usar Azure Media Services para crear transmisiones con velocidad de bits múltiple
En este tema se enumeran los codificadores en directo locales que puede utilizar para capturar los eventos en directo y enviar una secuencia en directo con velocidad de bits única a canales AMS (que permiten la codificación en directo) para su posterior procesamiento. El tema también incluye vínculos a tutoriales que muestran cómo configurar los codificadores presentados.

> [!NOTE]
> Al hacer el streaming mediante RTMP, compruebe la configuración del firewall o del proxy para confirmar que los puertos TCP salientes 1935 y 1936 están abiertos.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Para información sobre cómo configurar el codificador [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) para enviar una secuencia en directo con velocidad de bits única a un canal AMS, consulte [Configuración de FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Codificador Haivision KB
Para información sobre cómo configurar el [codificador Haivision KB](https://www.haivision.com/products/kb-series/) para enviar una secuencia en directo con velocidad de bits única a un canal AMS, consulte [Configuring Haivision KB Encoder](media-services-configure-kb-live-encoder.md) (Configuración del codificador Haivision KB).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Para obtener información sobre cómo configurar el codificador [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar una secuencia en directo con velocidad de bits única a un canal AMS, consulte [Configuración de Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Para obtener información sobre cómo configurar el codificador [Tricaster](http://newtek.com/products/tricaster-40.html) para enviar una secuencia en directo con velocidad de bits única a un canal AMS, consulte [Configuración de Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para obtener más información, consulte [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Pasos siguientes

[Streaming en vivo con Azure Media Services para crear transmisiones con velocidad de bits múltiple](media-services-manage-live-encoder-enabled-channels.md)

