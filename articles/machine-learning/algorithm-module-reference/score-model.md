---
title: 'Puntuar modelo: Referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Aprenda a usar el módulo Puntuar modelo en Azure Machine Learning Service para generar predicciones mediante un modelo entrenado de clasificación o regresión.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029271"
---
# <a name="score-model-module"></a>Score Model module

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Utilice este módulo para generar predicciones con un modelo entrenado de clasificación o regresión.

## <a name="how-to-use"></a>Modo de uso

1. Agregue el módulo **Puntuar modelo** al experimento.

2. Adjunte un modelo entrenado y un conjunto de datos que contenga los nuevos datos de entrada. 

    Los datos deben estar en un formato compatible con el tipo de modelo entrenado que usa. El esquema del conjunto de datos de entrada también debe coincidir generalmente con el esquema de los datos utilizados para entrenar el modelo.

3. Ejecute el experimento.

## <a name="results"></a>Results

Después de haber generado un conjunto de puntuaciones mediante [Puntuar modelo](./score-model.md):

+ Para generar un conjunto de métricas utilizadas para evaluar la precisión del modelo (rendimiento).  Puede conectar el conjunto de datos puntuado a [Evaluar modelo](./evaluate-model.md). 
+ Haga clic con el botón derecho en el módulo y seleccione **Visualizar** para ver un ejemplo de los resultados.
+ Guarde los resultados en un conjunto de datos.

La puntuación, o el valor de predicción, puede estar en muchos formatos diferentes, según el modelo y los datos de entrada:

- Para los modelos de clasificación, las salidas de [Puntuar modelo](./score-model.md) dan como resultado un valor de predicción para la clase, así como la probabilidad del valor de predicción.
- Para los modelos de regresión, [Puntuar modelo](./score-model.md) genera simplemente el valor numérico de predicción.
- Para los modelos de clasificación de imagen, la puntuación puede ser la clase de objeto en la imagen o un valor booleano que indica si se ha encontrado una característica determinada.

## <a name="publish-scores-as-a-web-service"></a>Publicar puntuaciones como servicio web

Un uso común de la puntuación es devolver la salida como parte de un servicio web predictivo. Para obtener más información, vea este tutorial sobre cómo crear un servicio web basado en un experimento en Azure Machine Learning:


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 