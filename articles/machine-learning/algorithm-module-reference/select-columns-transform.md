---
title: 'Seleccionar transformación de columnas: Referencia del módulo'
titleSuffix: Azure Machine Learning service
description: Aprenda a usar el módulo Select Columns Transform (Seleccionar transformación de columnas) en Azure Machine Learning Service para crear una transformación que seleccione el mismo subconjunto de columnas que en el conjunto de datos dado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 383adeb2bd081893d4202239cb97c12bf9a5a170
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717037"
---
# <a name="select-columns-transform"></a>Seleccionar transformación de columnas

En este artículo se describe cómo usar el módulo Select Columns Transform (Seleccionar transformación de columnas) en el diseñador de Azure Machine Learning (versión preliminar). El propósito del módulo Select Columns Transform (Seleccionar transformación de columnas) es asegurarse de que siempre se usa un conjunto de columnas coherente y predecible en las operaciones de aprendizaje automático de nivel inferior.

Este módulo es útil para tareas como puntuación, que requiere columnas específicas. Los cambios en las columnas disponibles pueden interrumpir la canalización o cambiar los resultados.

Use Select Columns Transform (Seleccionar transformación de columnas) para crear y guardar un conjunto de columnas. Después, use el módulo [Aplicar transformación](apply-transformation.md) para aplicar esas selecciones en los nuevos datos.

## <a name="how-to-use-select-columns-transform"></a>Cómo usar Selección de transformación de columnas

En este escenario se supone que desea usar la selección de características para generar un conjunto dinámico de columnas que se usará para el entrenamiento de un modelo. Para asegurarse de que las selecciones de columna son las mismas para el proceso de puntuación, use el módulo Select Columns Transform (Seleccionar transformación de columnas) para capturar las selecciones de columna y aplicarlas en cualquier otra parte de la canalización.

1. Agregue un conjunto de datos de entrada a la canalización del diseñador.

2. Agregue una instancia de [Selección de características basada en filtros](filter-based-feature-selection.md).

3. Conecte los módulos y configure el módulo de selección de características para encontrar automáticamente algunas de las mejores características en el conjunto de datos de entrada.

4. Agregue una instancia de [Entrenar modelo](train-model.md) y use la salida de [Selección de características basada en filtros](filter-based-feature-selection.md) como entrada para el entrenamiento.

    > [!IMPORTANT]
    > Dado que la importancia de la característica se basa en los valores de la columna, no puede saber de antemano qué columnas pueden estar disponibles para la entrada para [Train Model](train-model.md) (Entrenar modelo).  
5. Adjunte una instancia del módulo Select Columns Transform (Seleccionar transformación de columnas). 

    Este paso genera una selección de columnas como una transformación que se puede guardar o aplicar a otros conjuntos de datos. Este paso garantiza que las columnas identificadas por la selección de características se guarden para que otros módulos las vuelvan a usar.

6. Agregue el módulo [Puntuación modelo](score-model.md). 

   *No conecte el conjunto de datos de entrada.* En su lugar, agregue el módulo de [Aplicar transformación](apply-transformation.md) y conecte la salida de la transformación de selección de características.

   > [!IMPORTANT]
   > No puede esperar aplicar [Filter Based Feature Selection](filter-based-feature-selection.md) (Selección de características basada en filtros) al conjunto de datos de puntuación y obtener los mismos resultados. Dado que la selección de características se basa en valores, puede elegir un conjunto diferente de columnas, lo que provocaría el fracaso de la operación de puntuación.
7. Ejecución de la canalización

Este proceso de guardar y después aplicar una selección de columnas garantiza que el mismo esquema de datos esté disponible para el entrenamiento y la puntuación.


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 
