---
title: ¿Qué es una puntuación BLEU? Custom Translator
titleSuffix: Azure Cognitive Services
description: BLEU es una medida de las diferencias existentes entre una traducción automática y una o varias traducciones humanas de referencia de una misma frase de origen. El algoritmo de BLEU compara expresiones consecutivas de la traducción automática con las expresiones consecutivas que encuentra en la traducción de referencia y cuenta el número de coincidencias en un modo ponderado.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: a77fd1a84c1ffc18a1e0c74000c72db5cdbb00e1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447378"
---
# <a name="what-is-a-bleu-score"></a>¿Qué es una puntuación BLEU?

[BLEU (Bilingual Evaluation Understudy o suplente de evaluación bilingüe)](https://en.wikipedia.org/wiki/BLEU) es una medida de las diferencias existentes entre una traducción automática y una o varias traducciones humanas de referencia de una misma frase de origen.

## <a name="scoring-process"></a>Proceso de puntuación

El algoritmo de BLEU compara expresiones consecutivas de la traducción automática con las expresiones consecutivas que encuentra en la traducción de referencia y cuenta el número de coincidencias en un modo ponderado. Estas coincidencias son independientes de la posición. Un mayor grado de coincidencia indica un mayor grado de similitud con la traducción de referencia y, por tanto, una puntuación más alta. La inteligibilidad y la corrección gramatical no se tienen en cuenta.

## <a name="how-bleu-works"></a>¿Cómo funciona BLEU?

La principal ventaja de BLEU es que se correlaciona bien con el criterio humano calculando el promedio de los errores de criterio de frases individuales de un corpus de prueba, en lugar de intentar averiguar el criterio humano exacto para cada frase.

[Aquí](https://youtu.be/-UqDljMymMg) puede encontrar un análisis más completo sobre la puntuación BLEU.

Los resultados de BLEU dependen en gran medida de la amplitud del dominio, la coherencia de los datos de prueba con los datos de aprendizaje y optimización, y la cantidad de datos disponible para el entrenamiento. Si los modelos se han entrenado en un dominio reducido, y los datos de aprendizaje son coherentes con los datos de prueba, es previsible una puntuación BLEU alta.

>[!NOTE]
>Una comparación entre puntuaciones BLEU solo se puede justificar si los resultados de BLEU se comparan con el mismo conjunto de prueba, el mismo par de idiomas y el mismo motor de traducción automática. La puntuación BLEU de un conjunto de prueba diferente es obligatoriamente diferente.
