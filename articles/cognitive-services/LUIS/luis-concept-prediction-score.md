---
title: Puntuaciones de predicción
titleSuffix: Language Understanding - Azure Cognitive Services
description: Una puntuación de predicción indica el grado de confianza que tiene el servicio API de LUIS en los resultados de predicción, según una expresión del usuario.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813997"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Las puntuaciones de predicción indican la precisión de la predicción de las intenciones y las entidades.

Una puntuación de predicción indica el grado de confianza que tiene LUIS en los resultados de la predicción, según una expresión del usuario.

Una puntuación de predicción se encuentra entre cero (0) y uno (1). Un ejemplo de una puntuación de LUIS de gran confianza es 0,99. Un ejemplo de una puntuación de confianza baja es 0,01. 

|Valor de la puntuación|Confianza|
|--|--|
|1|coincidencia clara|
|0,99|confianza alta|
|0,01|confianza baja|
|0|falta de coincidencia clara|

Cuando una expresión da como resultado una puntuación de confianza baja, LUIS lo pone de manifiesto en la página **Intención** del sitio web de [LUIS](luis-reference-regions.md), con el valor de **labeled-intent** identificado destacado en color rojo.

![Discrepancia en la puntuación](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Intención de puntuación superior

Todas las predicciones de expresión devuelven una intención de puntuación superior. Esta predicción es una comparación numérica de las puntuaciones de predicción. Puede haber una diferencia muy pequeña entre las dos puntuaciones mayores. LUIS no indica esta proximidad de otra forma que no sea devolver la mayor puntuación.  

## <a name="return-prediction-score-for-all-intents"></a>Devolver la puntuación de predicción de todas las intenciones

Un resultado de prueba o punto de conexión puede incluir todas las intenciones. Esta configuración se establece en el [punto de conexión](https://aka.ms/v1-endpoint-api-docs) con el par valor/nombre de cadena de consulta `verbose=true`.

## <a name="review-intents-with-similar-scores"></a>Revisar las intenciones con puntuaciones similares

Revisar la puntuación de todas las intenciones es una buena manera de comprobar que no solo se identifica la intención correcta, sino que la puntuación de la siguiente intención identificada es notable y sistemáticamente inferior en las expresiones.

Si varias intenciones tienen puntuaciones de predicción cercanas, en función del contexto de una expresión, LUIS puede alternar entre las intenciones. Para corregir esta situación, siga agregando expresiones a cada intención con una variedad más amplia de diferencias contextuales, o puede hacer que la aplicación cliente, como un bot de chat, realice elecciones de programación sobre cómo controlar las dos intenciones principales.

Las dos intenciones con puntuaciones muy cercanas se pueden invertir debido al aprendizaje no determinista. La puntuación superior podría convertirse en la segunda superior y la puntuación segunda podría convertirse en la puntuación superior. Con el fin de evitar esta situación, agregue expresiones de ejemplo a cada una de las dos intenciones principales para esa expresión con la elección de palabras y el contexto que las diferencia. Las dos intenciones deben tener aproximadamente el mismo número de expresiones de ejemplo. Una regla general de separación para evitar la inversión debida al aprendizaje sería una diferencia del 15 % en las puntuaciones.

Puede desactivar el aprendizaje no determinista mediante el [entrenamiento de todos los datos](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferencias con las predicciones entre sesiones de entrenamiento distintas

Si entrena el mismo modelo en otra aplicación y las puntuaciones no son las mismas, esta diferencia se debe a que hay aprendizaje no determinista (un elemento de aleatoriedad). En segundo lugar, cualquier superposición de una expresión a más de una intención significa que la intención superior de la misma expresión puede cambiar en función del aprendizaje.

Si el bot de chat requiere una puntuación de LUIS específica para indicar confianza en una intención, debe usar la diferencia de puntuación entre las dos intenciones principales. Esta situación proporciona flexibilidad para las variaciones en el entrenamiento.

## <a name="e-exponent-notation"></a>Notación E (exponencial)

Las puntuaciones de predicción pueden usar la notación exponencial, de forma que *aparecen* por encima del intervalo 0-1, como `9.910309E-07`. Esta puntuación es una indicación de un número muy **pequeño**.

|Puntuación de notación E |Puntuación real|
|--|--|
|9,910309E-07|,0000009910309|

## <a name="punctuation"></a>Signos de puntuación

Los signos de puntuación son un token independiente de LUIS. Una expresión que contiene un punto final frente a una expresión que no lo tiene son dos expresiones distintas y pueden tener predicciones diferentes. Asegúrese de que el modelo controla los signos de puntuación, ya sea en las [expresiones de ejemplo](luis-concept-utterance.md) (que los tengan o no) o en los [patrones](luis-concept-patterns.md), donde es más sencillo pasar por alto los signos de puntuación con la sintaxis especial: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Pasos siguientes

Vea [Add entities](luis-how-to-add-entities.md) (Agregar entidades) para obtener más información sobre cómo agregar entidades a la aplicación de LUIS.
