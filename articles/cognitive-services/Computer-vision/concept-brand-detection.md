---
title: 'Detección de la marca: Computer Vision'
titleSuffix: Azure Cognitive Services
description: Conceptos relacionados con la detección de marca/logotipo en Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: b4bcd07cf7549029d09f5acd58fdf8f92fffed74
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945299"
---
# <a name="detect-popular-brands-in-images"></a>Detección de marcas populares en las imágenes

La detección de la marca es un modo especializado de [detección de objetos](concept-object-detection.md) que usa una base de datos de miles de logotipos mundiales para identificar las marcas comerciales en imágenes o vídeos. Puede usar esta característica, por ejemplo, para detectar qué marcas son más populares en medios sociales o más frecuentes en la ubicación de los productos multimedia.

El servicio Computer Vision detecta si existen logotipos de marca en una imagen determinada; si es así, devuelve el nombre de la marca, una puntuación de confianza y las coordenadas del rectángulo delimitador del logotipo.

La base de datos de logotipos integrada cubre marcas populares de electrónica de consumo, vestimenta y mucho más. Si encuentra que el servicio Computer Vision no detecta la marca que está buscando, puede ser conveniente crear y entrenar su propio detector de logotipos con el servicio [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Ejemplo de detección de marcas

En la siguiente respuesta JSON, se ilustra qué devuelve Computer Vision cuando se detectan marcas de las imágenes de ejemplo.

![Un camiseta roja con una etiqueta y el logotipo de Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

En algunos casos, el detector de marcas captará la imagen del logotipo y el nombre estilizado de la marca como dos logotipos independientes.

![Un sudadera gris con una etiqueta y el logotipo de Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Uso de la API

La característica de detección de marcas forma parte de la API [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Puede llamar a esta API mediante una SDK nativa o con llamadas a REST. Incluya `Brands` en el parámetro de consulta **visualFeatures**. Después, cuando llegue la respuesta JSON completa, simplemente analice la cadena con el contenido de la sección `"brands"`.

* [Inicio rápido: Análisis de imágenes (SDK .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Inicio rápido: Análisis de imágenes (API REST)](./quickstarts/csharp-analyze.md)
