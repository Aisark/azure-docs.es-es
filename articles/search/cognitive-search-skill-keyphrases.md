---
title: 'Aptitud de Cognitive Search para la extracción de frases clave: Azure Search'
description: Evalúa el texto no estructurado y, en cada registro, devuelve una lista de frases clave en un canal de enriquecimiento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: dcd0579482a8292dc848a1d2f6a779c93e7e349c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841041"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Aptitud cognitiva para la extracción de frases clave

La aptitud **Extracción de frases clave** evalúa el texto no estructurado y, en cada registro, devuelve una lista de frases clave. Esta aptitud utiliza los modelos de aprendizaje automático proporcionados por [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) en Cognitive Services.

Esta capacidad es útil si necesita identificar rápidamente los principales puntos de conversación en el registro. Por ejemplo, dado el texto de entrada "La comida estaba deliciosa y el personal era maravilloso", el servicio devuelve "comida" y "personal maravilloso".

> [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y para la extracción de imágenes como parte de la fase de descifrado de documentos en Azure Search. No hay ningún cargo por la extracción de texto de documentos.
>
> La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Los precios de la extracción de imágenes se describen en la [página de precios de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe ser de 50 000 caracteres, medidos por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos al extractor de frases clave, puede usar la [aptitud de división de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de las aptitudes

Los parámetros distinguen mayúsculas de minúsculas.

| Entradas                | DESCRIPCIÓN |
|---------------------|-------------|
| defaultLanguageCode | (Opcional) Es el código de idioma que se aplicará a los documentos que no especifiquen el lenguaje de forma explícita.  Si no se especifica el código de idioma predeterminado, se usará el inglés (en) como código de idioma predeterminado. <br/> Ver [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (Lista completa de idiomas admitidos). |
| maxKeyPhraseCount   | (Opcional) Es el número máximo de frases clave para producir. |

## <a name="skill-inputs"></a>Entradas de la aptitud

| Entradas     | DESCRIPCIÓN |
|--------------------|-------------|
| text | Texto que se va a analizar.|
| languageCode  |  Cadena que indica el idioma de los registros. Si no se especifica este parámetro, el código de idioma predeterminado se utilizará para analizar los registros. <br/>Ver [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (Lista completa de idiomas admitidos).|

##  <a name="sample-definition"></a>Definición de ejemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>Entrada de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Salida de ejemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Errores y advertencias
Si proporciona un código de idioma no admitido, se generará un error y no se extraerán las frases clave.
Si el texto está vacío, se creará una advertencia.
Si el texto tiene más de 50 000 caracteres, solo se analizarán los primeros 50 000 caracteres y se emitirá una advertencia.

## <a name="see-also"></a>Otras referencias

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
