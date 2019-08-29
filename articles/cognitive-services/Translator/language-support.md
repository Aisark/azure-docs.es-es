---
title: 'Idiomas admitidos: Translator Text API'
titleSuffix: Azure Cognitive Services
description: Una lista de los idiomas naturales admitidos por Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be69f18f30b796e7401ef68aa5088153e07f14f5
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127882"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Compatibilidad de idiomas y regiones para Translator Text API

Translator Text API admite los siguientes idiomas para la conversión de texto a texto. La traducción automática neuronal (NMT) es el nuevo estándar de traducción automática de alta calidad con tecnologías de inteligencia artificial, y está disponible de forma predeterminada con la versión V3 de Translator Text API cuando hay un sistema neuronal disponible.

[Más información sobre cómo funciona la traducción automática](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Traducción

**Translator API V2**

> [!NOTE]
> V2 quedó en desuso el 30 de abril de 2018. Migre sus aplicaciones a V3 para aprovechar la nueva funcionalidad disponible exclusivamente en V3.

* Solo estadísticas: no hay ningún sistema neuronal disponible para este idioma.
* Traducción neuronal disponible: hay un sistema neuronal disponible. Utilice el parámetro `category=generalnn` para acceder al sistema neuronal.
* Neuronal como sistema predeterminado: el sistema de traducción neuronal es el predeterminado. Utilice el parámetro `category=smt` para acceder al sistema estadístico y utilizarlo con Microsoft Translator Hub.
* Solo neuronal: la traducción neuronal es la única que está disponible.

**Translator API V3** Translator API V3 utiliza el sistema neuronal de forma predeterminada, por lo que los sistemas estadísticos solamente están disponibles cuando no existe un sistema neuronal.

> [!NOTE]
> Actualmente, un subconjunto de los idiomas neuronales está disponible en Traductor personalizado y agregamos otros adicionales de forma gradual. [Vea los idiomas disponibles actualmente en Traductor personalizado](#customization).

|Idioma|  Código de idioma|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikáans| `af`    |Solo estadísticas|  Neuronal|
|Árabe|    `ar`    |Traducción neuronal disponible|  Neuronal|
|Bangla|    `bn`    |Traducción neuronal disponible|  Neuronal|
|Bosnio (latino)|   `bs`    |Traducción neuronal disponible|  Neuronal|
|Búlgaro| `bg`    |Traducción neuronal disponible|  Neuronal|
|Cantonés (tradicional)|   `yue`   |Solo estadísticas|  Estadística|
|Catalán|   `ca`    |Solo estadísticas|  Estadística|
|Chino simplificado|    `zh-Hans`   |Neuronal como sistema predeterminado |Neuronal|
|Chino tradicional|   `zh-Hant`   |Neuronal como sistema predeterminado |Neuronal|
|Croata|  `hr`    |Traducción neuronal disponible|  Neuronal|
|Checo| `cs`    |Traducción neuronal disponible|  Neuronal|
|Danés|    `da`    |Traducción neuronal disponible   |Neuronal|
|Neerlandés| `nl`    |Traducción neuronal disponible|  Neuronal|
|English|   `en`    |Traducción neuronal disponible|  Neuronal|
|Estonio|  `et`    |Traducción neuronal disponible|  Neuronal|
|Fiyiano|    `fj`    |Solo estadísticas|  Estadística|
|Filipino|  `fil`   |Solo estadísticas|  Estadística|
|Finés|   `fi`    |Traducción neuronal disponible|  Neuronal|
|Francés|    `fr`    |Traducción neuronal disponible|  Neuronal|
|Alemán|    `de`    |Traducción neuronal disponible|  Neuronal|
|Griego| `el`    |Traducción neuronal disponible|  Neuronal|
|Criollo haitiano|    `ht`    |Solo estadísticas   |Estadística|
|Hebreo |`he`   |Traducción neuronal disponible   |Neuronal|
|Hindi| `hi`    |Neuronal como sistema predeterminado|    Neuronal|
|Hmong Daw| `mww`   |Solo estadísticas|  Estadística|
|Húngaro| `hu`    |Traducción neuronal disponible|  Neuronal|
|Islandés| `is`    |Solo neuronal|   Neuronal|
|Indonesio|    `id`    |Solo estadísticas|  Estadística|
|Italiano|   `it`    |Traducción neuronal disponible|  Neuronal|
|Japonés|  `ja`    |Traducción neuronal disponible|  Neuronal|
|Kiswahili| `sw`    |Solo estadísticas|  Estadística|
|Klingon|   `tlh`   |Solo estadísticas|  Estadística|
|Klingon (plqaD)|   `tlh-Qaak`  |Solo estadísticas|  Estadística|
|Coreano |`ko`   |Traducción neuronal disponible|  Neuronal|
|Letón|   `lv`    |Traducción neuronal disponible|  Neuronal|
|Lituano|    `lt`    |Traducción neuronal disponible|  Neuronal|
|Malgache|  `mg`    |Solo estadísticas|  Estadística|
|Malayo| `ms`    |Solo estadísticas   |Estadística|
|Maltés|   `mt`    |Solo estadísticas|  Estadística|
|Noruego| `nb`    |Traducción neuronal disponible|  Neuronal|
|Persa|   `fa`    |Traducción neuronal disponible|  Neuronal|
|Polaco|    `pl`    |Traducción neuronal disponible|  Neuronal|
|Portugués|    `pt`    |Traducción neuronal disponible|  Neuronal|
|Otomí Querétaro|   `otq`   |Solo estadísticas|  Estadística|
|Rumano|  `ro`    |Traducción neuronal disponible|  Neuronal|
|Ruso|   `ru`    |Traducción neuronal disponible|  Neuronal|
|Samoano|    `sm`    |Solo estadísticas|  Estadística|
|Serbio (cirílico)|    `sr-Cyrl`   |Solo estadísticas|  Estadística|
|Serbio (latino)|   `sr-Latn`   |Solo estadísticas   |Estadística|
|Eslovaco|    `sk`    |Traducción neuronal disponible|  Neuronal|
|Esloveno| `sl`    |Traducción neuronal disponible|  Neuronal|
|Español|   `es`    |Traducción neuronal disponible|  Neuronal|
|Sueco|   `sv`    |Traducción neuronal disponible   |Neuronal|
|Tahitiano|  `ty`    |Solo estadísticas|  Estadística|
|Tamil| `ta`    |Solo estadísticas|  Estadística|
|Telugu|    `te`    |Solo neuronal|   Neuronal|
|Tailandés|  `th`    |Traducción neuronal disponible|  Neuronal|
|Tongano|    `to`    |Solo estadísticas|  Estadística|
|Turco|   `tr`    |Traducción neuronal disponible   |Neuronal|
|Ucraniano| `uk`    |Traducción neuronal disponible|  Neuronal|
|Urdu|  `ur`    |Solo estadísticas|  Estadística|
|Vietnamita|    `vi`    |Traducción neuronal disponible|  Neuronal|
|Galés| `cy`    |Traducción neuronal disponible|  Neuronal|
|Maya Yucateco|  `yua`   |Solo estadísticas|  Estadística|

## <a name="transliteration"></a>Transliteración

El método Transliterate admite los siguientes idiomas. En "Hacia/Desde", "<-->" indica que el idioma se puede transliterar hacia o desde cualquiera de los alfabetos enumerados. "-->" indica que el idioma solo se puede transliterar de un idioma al otro.

| Idioma    | Código de idioma | Script | Hacia/Desde | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latino `Latn` |
|Bangla  | `bn` | Bengalí `Beng` | <--> | Latino `Latn` |
| Chino (simplificado) | `zh-Hans` | Chino simplificado `Hans`| <--> | Latino `Latn` |
| Chino (simplificado) | `zh-Hans` | Chino simplificado `Hans`| <--> | Chino tradicional `Hant`|
| Chino (tradicional) | `zh-Hant` | Chino tradicional `Hant`| <--> | Latino `Latn` |
| Chino (tradicional) | `zh-Hant` | Chino tradicional `Hant`| <--> | Chino simplificado `Hans` |
| Gujarati | `gu`  | Guyaratí `Gujr` | --> | Latino `Latn` |
| Hebreo | `he` | Hebreo `Hebr` | <--> | Latino `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latino `Latn` |
| Japonés | `ja` | Japonés `Jpan` | <--> | Latino `Latn` |
| Canarés | `kn` | Canarés `Knda` | --> | Latino `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latino `Latn` |
| Maratí | `mr` | Devanagari `Deva` | --> | Latino `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latino `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latino `Latn`  |
| Serbio (cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latino `Latn` |
| Serbio (latino) | `sr-Latn` | Latino `Latn` | --> | Cirílico `Cyrl`|
| Tamil | `ta` | Tamil `Taml` | --> | Latino `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Latino `Latn` |
| Tailandés | `th` | Tailandés `Thai` | <--> | Latino `Latn` |

## <a name="dictionary"></a>Diccionario

El diccionario admite los siguientes idiomas desde o hacia el inglés con los métodos Lookup y Examples.

| Idioma    | Código de idioma |
|:----------- |:-------------:|
| Afrikáans      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bosnio (latino)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalán      | `ca`          |
| Chino simplificado      | `zh-Hans`          |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Danés      | `da`          |
| Neerlandés      | `nl`          |
| Estonio      | `et`          |
| Finés      | `fi`          |
| Francés      | `fr`          |
| Alemán      | `de`          |
| Griego      | `el`          |
| Criollo haitiano      | `ht`          |
| Hebreo      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandés    | `is`  |
| Indonesio      | `id`          |
| Italiano      | `it`          |
| Japonés      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letón      | `lv`          |
| Lituano      | `lt`          |
| Malayo      | `ms`          |
| Maltés      | `mt`          |
| Noruego      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Portugués      | `pt`          |
| Rumano      | `ro`          |
| Ruso      | `ru`          |
| Serbio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Español      | `es`          |
| Sueco      | `sv`          |
| Tamil      | `ta`          |
| Tailandés      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galés      | `cy`          |

## <a name="detect"></a>Detect

Translator Text API detecta todos los idiomas disponibles para traducción y transliteración.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acceso a la lista de idiomas de Translator Text API mediante programación

Puede recuperar una lista de los idiomas admitidos por Translator Text API v3.0 mediante el método Languages. Puede ver la lista por característica, código de idioma o por nombre del idioma en inglés o en cualquier otro idioma admitido. El servicio Microsoft Translator actualiza esta lista automáticamente cuando hay nuevos idiomas disponibles.

[Ver la documentación de referencia de la operación Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personalización

Los siguientes idiomas están disponibles para personalización al y del inglés mediante [Traductor personalizado](https://aka.ms/CustomTranslator).

| Idioma    | Código de idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bosnio (latino)      | `bs`          |
| Búlgaro      | `bg`          |
| Chino simplificado      | `zh-Hans`          |
|Chino tradicional|   `zh-Hant`   |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Danés      | `da`          |
| Neerlandés      | `nl`          |
| English    | `en`     |
| Estonio      | `et`          |
| Finés      | `fi`          |
| Francés      | `fr`          |
| Alemán      | `de`          |
| Griego      | `el`          |
| Hebreo      | `he`          |
| Hindi      | `hi`          |
| Húngaro      | `hu`          |
| Islandés | `is` |
| Indonesio|   `id`    |
| Italiano      | `it`          |
| Japonés      | `ja`          |
|Kiswahili| `sw`    |
| Coreano      | `ko`          |
| Letón      | `lv`          |
| Lituano      | `lt`          |
|Malgache|  `mg`    |
| Noruego      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Portugués      | `pt`          |
| Rumano      | `ro`          |
| Ruso      | `ru`          |
|Samoano|    `sm`    |
| Serbio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Español      | `es`          |
| Sueco      | `sv`          |
| Tailandés      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |
| Galés | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acceso a la lista en el sitio web de Microsoft Translator

Para echar un vistazo rápido a los idiomas, el sitio web de Microsoft Translator muestra todos los idiomas admitidos por Translator Text API y Speech API. Esta lista no incluye información específica para desarrolladores, por ejemplo, los códigos de idioma.

[Ver la lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
