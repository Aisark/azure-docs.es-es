---
title: Aptitud cognitiva Text Translation
titleSuffix: Azure Cognitive Search
description: Evalúa el texto y devuelve para cada registro el texto traducido al idioma de destino especificado en una canalización de enriquecimiento de inteligencia artificial de Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c2405fe67b39e016e64efb1b36cc551a00a338fc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791851"
---
#   <a name="text-translation-cognitive-skill"></a>Aptitud cognitiva Text Translation

La aptitud **Text Translation** evalúa el texto y, para cada registro, devuelve el texto traducido al idioma de destino especificado. Esta aptitud usa [Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate), disponible en Cognitive Services.

Esta funcionalidad es útil si espera que los documentos no estén en un único idioma, en cuyo caso puede normalizar el texto a un solo idioma antes de la indexación para la búsqueda mediante su traducción.  También es útil para los casos de uso de localización, en los que puede que desee tener copias del mismo texto disponibles en varios idiomas.

[Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) es un servicio de Cognitive Services no regional, lo que significa que no se garantiza que los datos permanezcan en la misma región que Azure Cognitive Search o el recurso de Cognitive Services asociado.

> [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y por la extracción de imágenes como parte de la fase de descifrado de documentos de Azure Cognitive Search. No hay ningún cargo por la extracción de texto de documentos.
>
> La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Los precios de la extracción de imágenes se describen en la [página de precios de Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Límites de datos
El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos a la aptitud Text Translation, puede usar la [aptitud Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Entradas                | DESCRIPCIÓN |
|---------------------|-------------|
| defaultToLanguageCode | (Obligatorio) Código de idioma al que traducir los documentos para aquellos documentos que no especifican el idioma de destino explícitamente. <br/> Vea [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (Lista completa de idiomas admitidos). |
| defaultFromLanguageCode | (Opcional) Código de idioma desde el que traducir los documentos para aquellos documentos que no especifican el idioma de origen explícitamente.  Si no se especifica defaultFromLanguageCode, se usará la detección automática de idioma proporcionada por Translator Text API para determinar el idioma de origen. <br/> Vea [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (Lista completa de idiomas admitidos). |
| suggestedFrom | (Opcional) Código de idioma desde el que se van a traducir documentos cuando no se proporcionan ni la entrada fromLanguageCode ni el parámetro defaultFromLanguageCode y la detección automática de idioma no se realiza correctamente.  Si no se especifica el idioma suggestedFrom, se usará inglés (en) como idioma suggestedFrom. <br/> Vea [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (Lista completa de idiomas admitidos). |

## <a name="skill-inputs"></a>Entradas de la aptitud

| Nombre de entrada     | DESCRIPCIÓN |
|--------------------|-------------|
| text | Texto que se va a traducir.|
| toLanguageCode    | Cadena que indica el idioma al que se debe traducir el texto. Si no se especifica esta entrada, se usará defaultToLanguageCode para traducir el texto. <br/>Vea [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (Lista completa de idiomas admitidos).|
| fromLanguageCode  | Cadena que indica el idioma actual del texto. Si no se especifica este parámetro, se utilizará defaultFromLanguageCode (o la detección automática de idioma si no se proporciona defaultFromLanguageCode) para traducir el texto. <br/>Vea [Full list of supported languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (Lista completa de idiomas admitidos).|

## <a name="skill-outputs"></a>Salidas de la aptitud

| Nombre de salida    | DESCRIPCIÓN |
|--------------------|-------------|
| translatedText | Cadena resultante de la traducción del texto desde translatedFromLanguageCode a translatedToLanguageCode.|
| translatedToLanguageCode  | Cadena que indica el código de idioma al que se ha traducido el texto. Resulta útil si va a traducir a varios idiomas y desea poder realizar un seguimiento del idioma de cada texto.|
| translatedFromLanguageCode    | Cadena que indica el código de idioma desde el que se ha traducido el texto. Resulta útil si opta por la opción de detección automática de idioma, ya que esta salida le dará el resultado de dicha detección.|

##  <a name="sample-definition"></a>Definición de ejemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Salida de ejemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Errores y advertencias
Si proporciona un código de idioma no admitido para el lenguaje de origen o destino, se genera un error y no se traduce el texto.
Si el texto está vacío, se creará una advertencia.
Si el texto tiene más de 50 000 caracteres, solo se traducirán los primeros 50 000 caracteres y se emitirá una advertencia.

## <a name="see-also"></a>Otras referencias

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
