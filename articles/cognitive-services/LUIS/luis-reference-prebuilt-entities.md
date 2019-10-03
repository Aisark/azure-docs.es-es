---
title: 'Todas las entidades precompiladas: LUIS'
titleSuffix: Azure Cognitive Services
description: Este artículo contiene listas de las entidades precompiladas que se incluyen en Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 66cdedb402392340d38ab9a72f6f19012c359664
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677621"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por la referencia cultural en el modelo de LUIS

Language Understanding (LUIS) proporciona entidades precompiladas. Cuando una entidad precompilada se incluye en la aplicación, LUIS incluye la predicción de entidad correspondiente en la respuesta del punto de conexión. Todas las expresiones de ejemplo también se etiquetan con la entidad. El comportamiento de las entidades predeterminadas **no puede** modificarse. A menos que se indique lo contrario, las entidades precompiladas están disponibles en todas las configuraciones locales de aplicación LUIS (referencias culturales). En la siguiente tabla se muestran las entidades precompiladas que se admiten para cada referencia cultural.

|Referencia cultural|Referencias culturales secundarias|Notas|
|--|--|--|
|Chino|[zh-CN](#chinese-entity-support)||
|Neerlandés|[nl-NL](#dutch-entity-support)||
|English|[en-US (inglés de EE. UU.)](#english-american-entity-support)||
|Francés|[fr-CA (Canadá)](#french-canadian-entity-support), [fr-FR (Francia)](#french-france-entity-support), ||
|Alemán|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Japonés|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Portugués|[pt-BR (portugués de Brasil)](#portuguese-brazil-entity-support)||
|Español|[es-ES (español de España)](#spanish-spain-entity-support), [es-MX (español de México)](#spanish-mexico-entity-support)||
|Turco|[turco](#turkish-entity-support)|No hay entidades precompiladas compatibles en turco|

## <a name="chinese-entity-support"></a>Compatibilidad con la entidad china

Se admiten las siguientes entidades:

|Entidad creada previamente|```zh-CN``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Compatibilidad con la entidad neerlandesa

Se admiten las siguientes entidades:

|Entidad creada previamente|```nl-NL``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Compatibilidad con la entidad inglesa (EE. UU.)

Se admiten las siguientes entidades:

|Entidad creada previamente|```en-US``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    ✔   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Compatibilidad con la entidad francesa (Francia)

Se admiten las siguientes entidades:

|Entidad creada previamente|```fr-FR``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Compatibilidad con la entidad francesa (Canadá)

Se admiten las siguientes entidades:

|Entidad creada previamente|```fr-CA``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Compatibilidad con la entidad alemana

Se admiten las siguientes entidades:

|Entidad creada previamente|```de-DE``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Compatibilidad con la entidad italiana

Se admiten las siguientes entidades:

|Entidad creada previamente|```it-IT``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Compatibilidad con la entidad japonesa

Se admiten las siguientes entidades:

|Entidad creada previamente|```ja-JP``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Compatibilidad con la entidad coreana

Se admiten las siguientes entidades:

|Entidad creada previamente|```ko-KR``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    -   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Compatibilidad con la entidad portuguesa (Brasil)

Se admiten las siguientes entidades:

|Entidad creada previamente|```pt-BR``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Compatibilidad con la entidad española (España)

Se admiten las siguientes entidades:

|Entidad creada previamente|```es-ES``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    ✔   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    ✔   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Compatibilidad con la entidad española (México)

Se admiten las siguientes entidades:

|Entidad creada previamente|```es-MX``` |
------|:------:|
[Edad](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Moneda (dinero)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidad fraccional (por ejemplo: penique)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>intervalo de tiempo   |    -   | 
[Dimensión](luis-reference-prebuilt-dimension.md):<br>volumen<br>área<br>peso<br>información (p. ej.: bits/bytes)<br>longitud (p. ej.: medidor)<br>velocidad (p. ej.: millas por hora)  |    -   | 
[Correo electrónico](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentaje](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de teléfono](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>grados Fahrenheit<br>grados Kelvin<br>grados Rankine<br>grados Delisle<br>grados centígrados   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Vea las notas en [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md) (Entidades precompiladas en desuso).

KeyPhrase no está disponible en todas las referencias culturales secundarias de portugués (Brasil): ```pt-BR```.

## <a name="turkish-entity-support"></a>Compatibilidad con la entidad turca

**No hay entidades precompiladas compatibles en turco.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir a las referencias culturales de las entidades precompiladas
Las entidades precompiladas se desarrollan en el proyecto de código abierto de Recognizers-Text. [Contribuya](https://github.com/Microsoft/Recognizers-Text) al proyecto. Este proyecto incluye ejemplos de moneda por cada referencia cultural. 

GeographyV2 y PersonName no se incluyen en el proyecto Recognizers-Text. Para problemas con estas entidades precompiladas, abra una [solicitud de soporte técnico](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de las entidades [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) y [currency](luis-reference-prebuilt-currency.md). 
