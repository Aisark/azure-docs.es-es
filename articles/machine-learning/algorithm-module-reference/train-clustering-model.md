---
title: 'Entrenamiento del modelo de agrupación en clústeres: referencia para los módulos'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el módulo Entrenamiento del modelo de agrupación en clústeres en Azure Machine Learning para entrenar un modelo de clasificación o regresión.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 57cf5bab2afe58ed802ef5ec03c316f1ebec6673
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232592"
---
# <a name="train-clustering-model"></a>Entrenamiento del modelo de agrupación en clústeres

En este artículo se describe un módulo del diseñador de Azure Machine Learning (versión preliminar).

Utilice este módulo para entrenar un modelo de agrupación en clústeres.

El módulo toma un modelo de agrupación en clústeres no entrenado que ya ha configurado mediante el módulo [Agrupación en clústeres k-means](k-means-clustering.md) y entrena el modelo utilizando un conjunto de datos sin etiqueta o con etiqueta. El módulo crea un modelo entrenado que puede usar para la predicción y un conjunto de asignaciones de clúster para cada caso de los datos de entrenamiento.

> [!NOTE]
> No se puede entrenar un modelo de agrupación en clústeres con el módulo [Entrenar modelo](train-model.md), que es el módulo genérico para el entrenamiento de modelos de Machine Learning. Es debido a que [Entrenar modelo](train-model.md) solo funciona con algoritmos de aprendizaje supervisado. K-means y otros algoritmos de agrupación en clústeres permiten el aprendizaje no supervisado, lo que significa que el algoritmo puede aprender de los datos sin etiqueta.  
  
## <a name="how-to-use-train-clustering-model"></a>Procedimiento para usar el modelo de entrenamiento de almacenamiento en clúster  

1.  Agregue el módulo **Entrenamiento del modelo de agrupación en clústeres** a la canalización en el diseñador. Puede encontrar el módulo en **Módulos de Machine Learning** de la categoría **Entrenar**.  
  
2. Agregue el módulo [Agrupación en clústeres k-means](k-means-clustering.md) u otro módulo personalizado que cree un modelo de agrupación en clústeres compatible y establezca los parámetros del modelo de agrupación en clústeres.  
    
3.  Adjunte un conjunto de datos de entrenamiento a la entrada de la derecha de **Entrenamiento del modelo de agrupación en clústeres**.
  
5.  En **Conjunto de columnas**, seleccione las columnas del conjunto de datos que desea utilizar para compilar clústeres. Asegúrese de seleccionar columnas que sean buenas características: por ejemplo, evite utilizar id. u otras columnas que tengan valores únicos o columnas que tengan todos los mismos valores.

    Si hay una etiqueta, puede usarla como una característica u omitirla.  
  
6. Seleccione la opción **Check for Append or Uncheck for Result Only** (Marcar para anexar o no marcar para resultados solo), si desea producir los datos de entrenamiento junto con la nueva etiqueta de clúster.

    Si anula la selección de esta opción, la salida solo contiene las asignaciones de clúster. 

7. Ejecute la canalización o haga clic en el módulo **Entrenamiento del modelo de agrupación en clústeres** y seleccione **Ejecutar seleccionados**.  
  
### <a name="results"></a>Results

Una vez completado el entrenamiento:


+  Para ver los valores del conjunto de datos, haga clic con el botón derecho en el módulo, seleccione **Result datasets** (Conjuntos de datos del resultado) y haga clic en **Visualizar**.

+ Para guardar el modelo entrenado a fin de volver a usarlo más adelante, haga clic con el botón derecho en el módulo, seleccione **Modelo entrenado** y haga clic en **Guardar como modelo entrenado**.

+ Para generar puntuaciones a partir del modelo, utilice [Asignación de datos a clústeres](assign-data-to-clusters.md).

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning. 