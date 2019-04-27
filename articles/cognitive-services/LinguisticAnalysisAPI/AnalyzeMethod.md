---
title: 'Método de análisis: Linguistic Analysis API'
titlesuffix: Azure Cognitive Services
description: Uso del método de análisis en Linguistic Analysis API para analizar determinadas entradas en lenguaje natural.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401194"
---
# <a name="analyze-method"></a>Método de análisis

> [!IMPORTANT]
> El 9 de agosto de 2018 se retiró la versión preliminar de Linguistic Analysis. Para el procesamiento y el análisis de texto, se recomienda usar los [módulos de análisis de texto de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

La API REST de **análisis** se usa para analizar una determinada entrada de lenguaje natural.
Que puede implicar simplemente buscar el [oraciones y tokens](Sentences-and-Tokens.md) dentro de esa entrada, buscar el [parte de la oración etiquetas](POS-tagging.md), o buscar el [árbol al área](Constituency-Parsing.md).
Puede especificar los resultados que desee eligiendo los analizadores correspondientes.
Para obtener una lista de todos los analizadores disponibles, mire **[analizadores](AnalyzersMethod.md)**.

Tenga en cuenta que debe especificar el idioma de la cadena de entrada.

**Punto de conexión de REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parámetros de solicitud

NOMBRE | Type | Obligatorio | DESCRIPCIÓN
-----|-------|----------|------------
**language**    | string | Sí | El código ISO de dos letras para idiomas que se va a usar para el análisis. Por ejemplo, español es "es".
**analyzerIds** | lista de cadenas | Sí | Lista de GUID de los analizadores que se van a aplicar. Consulte la documentación sobre analizadores para más información.
**text**        | string | Sí | Entrada sin formato que se va a analizar. Podría tratarse de una cadena corta como una palabra o sintagma, una frase completa, un párrafo completo o un discurso.

## <a name="response-json"></a>Respuesta (JSON)

Una matriz de salidas de análisis, una para cada atributo especificado en la solicitud.

Los resultados son parecidos a estos:

NOMBRE | Type | DESCRIPCIÓN
-----|------|--------------
analyzerId | string | GUID del analizador especificado
result | objeto | resultado del analizador

Tenga en cuenta que el tipo de resultado depende del tipo de analizador de entrada.

### <a name="tokens-response-json"></a>Respuesta de tokens (JSON)

NOMBRE | Type | DESCRIPCIÓN
-----|------|-------------
result | lista de objetos de frase | límites de frases identificados en el texto |
result[x].Offset | int | desplazamiento de carácter inicial de cada frase |
result[x].Len | int | longitud en caracteres de cada frase |
result[x].Tokens | lista de objetos de token | límites de tokens identificados en la frase |
result[x].Tokens[y].Offset | int | desplazamiento de carácter inicial del token |
result[x].Tokens[y].Len | int | longitud en caracteres del token |
result[x].Tokens[y].RawToken | string | los caracteres dentro de ese token, antes de la normalización |
result[x].Tokens[y].NormalizedToken | string | una forma normalizada del carácter, para un uso seguro en un [árbol de análisis](Constituency-Parsing.md); por ejemplo, un carácter de paréntesis de apertura "(" se convierte en "-LRB-" |

Entrada de ejemplo: "Esto es una prueba. Hola".
Ejemplo de respuesta JSON:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Respuesta de etiquetas POS (JSON)

El resultado es una lista de las listas de cadenas.
Para cada frase, hay una lista de etiquetas POS y una etiqueta POS para cada token.
Para buscar el token correspondiente de cada etiqueta POS, deberá pedir un objeto de tokenización también.

### <a name="constituency-tree-response-json"></a>Respuesta de árbol de elementos de la oración (JSON)

El resultado es una lista de cadenas y un árbol de análisis para cada frase que se detecta en la entrada.
Los árboles de análisis se representan en un formato con paréntesis.

## <a name="example"></a>Ejemplo

`POST /analyze`

Cuerpo de la solicitud: carga de JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Respuesta: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
