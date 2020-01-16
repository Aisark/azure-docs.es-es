---
title: Referencia de módulos y algoritmos
description: Conozca los módulos disponibles en el diseñador de Azure Machine Learning (versión preliminar).
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: 659c205bd12d7ca27bfc28e3ab6e62f58d9743ac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978656"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Referencia de módulo y algoritmo para el diseñador de Azure Machine Learning

Este contenido de referencia proporciona el contexto técnico de cada uno de los algoritmos y módulos de aprendizaje automático disponibles en el diseñador de Azure Machine Learning (versión preliminar).

Cada módulo representa un conjunto de código que puede ejecutarse de forma independiente y realizar una tarea de aprendizaje automático, dadas las entradas necesarias. Un módulo puede contener un algoritmo en particular o realizar una tarea que sea importante para el aprendizaje automático, como el reemplazo de un valor que falta o análisis estadísticos.

> [!TIP]
> En cualquier canalización del diseñador, puede obtener información sobre un módulo específico. Seleccione el módulo y luego seleccione el vínculo **más ayuda** en el panel **Ayuda rápida**.

## <a name="modules"></a>Módulos

Los módulos se organizan por funcionalidad:

| Funcionalidad | Descripción | módulo |
| --- |--- | --- |
|  | **Preparación de datos**: | |
| Entrada y salida de datos | Mueva los datos de los orígenes en la nube a la canalización. Escriba los resultados o los datos intermedios en Azure Storage, una base de datos SQL o Hive, mientras ejecuta una canalización, o use el almacenamiento en la nube para intercambiar datos entre canalizaciones.  | [Introducción manual de datos](enter-data-manually.md) <br/> [Export Data](export-data.md) <br/> [Import Data](import-data.md) |
| Transformación de datos | Operaciones con los datos que son exclusivas del aprendizaje automático, como la normalización o discretización de datos, la reducción de la dimensionalidad y la conversión de datos entre barios formatos de archivo.| [Adición de columnas](add-columns.md) <br/> [Adición de filas](add-rows.md) <br/> [Aplicación de operación matemática](apply-math-operation.md) <br/> [Aplicación de transformaciones de SQL](apply-sql-transformation.md) <br/> [Clean Missing Data](clean-missing-data.md) (limpiar datos faltantes) <br/> [Recorte de valores](clip-values.md) <br/> [Conversión a CSV](convert-to-csv.md) <br/> [Conversión en conjunto de datos](convert-to-dataset.md) <br/> [Edición de metadatos](edit-metadata.md) <br/> [Combinación de datos](join-data.md) <br/> [Normalize Data](normalize-data.md) (normalizar datos) <br/> [Partición y ejemplo](partition-and-sample.md)  <br/> [Supresión de filas duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selección de transformación de columnas](select-columns-transform.md) <br/> [Seleccionar columnas de conjunto de datos](select-columns-in-dataset.md) <br/> [División de datos](split-data.md) |
| Selección de características | Seleccione un subconjunto de características pertinentes y útiles para la creación de un modelo analítico. | [Selección de características basada en filtros](filter-based-feature-selection.md) <br/> [Importancia de la característica de permutación](permutation-feature-importance.md) |
| Funciones estadísticas | Proporcionar una amplia variedad de métodos estadísticos relacionados con la ciencia de datos. | [Resumen de datos](summarize-data.md)|
|  | **Algoritmos de aprendizaje automático**: | |
| Regresión | Prediga un valor. | [Boosted Decision Tree Regression](boosted-decision-tree-regression.md) (Regresión del árbol de decisión ampliado) <br/> [Regresión de bosque de decisión](decision-forest-regression.md) <br/> [Regresión lineal](linear-regression.md)  <br/> [Regresión de red neuronal](neural-network-regression.md)  <br/> |
| Agrupación en clústeres | Agrupe datos.| [Agrupación en clústeres K-Means](k-means-clustering.md)
| clasificación | Prediga una clase.  Elija en algoritmos binarios (dos clases) o multiclase.| [Árbol de decisión ampliado multiclase](multiclass-boosted-decision-tree.md) <br/> [Bosque de decisión multiclase](multiclass-decision-forest.md) <br/> [Regresión logística multiclase](multiclass-logistic-regression.md)  <br/> [Red neuronal multiclase](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) (Multiclase uno frente a todos) <br/> [Perceptrón promedio de dos clases](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Árbol de decisión promovido por dos clases)  <br/> [Bosque de decisión de dos clases](two-class-decision-forest.md) <br/>  [Regresión logística de dos clases](two-class-logistic-regression.md) <br/> [Red neuronal de dos clases](two-class-neural-network.md) <br/> [Two Class Support Vector Machine](two-class-support-vector-machine.md) (Máquina de vectores compatible con dos clases) | 
|  | **Compilar y evaluar modelos**: | |
| Entrenamiento del modelo | Ejecute datos a través del algoritmo. |  [Entrenamiento del modelo de agrupación en clústeres](train-clustering-model.md) <br/> [Train Model](train-model.md) (entrenar modelo)  <br/> [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Optimizar hiperparámetros del modelo) |
| Evaluación y puntuación del modelo | Mida la precisión del modelo entrenado. | [Aplicación de la transformación](apply-transformation.md) <br/> [Assign Data to Clusters](assign-data-to-clusters.md) (asignar datos a los clústeres) <br/> [Cross Validate Model](cross-validate-model.md) (Modelo de validación cruzada) <br/> [Evaluación de módulo](evaluate-model.md) <br/> [Score Model](score-model.md) (puntuar modelo) |
| Lenguaje Python | Escriba código e insértelo en un módulo para integrar Python con la canalización. | [Creación de modelo Python](create-python-model.md) <br/> [Ejecución de script de Python](execute-python-script.md) |
| Lenguaje R | Escriba código e insértelo en un módulo para integrar R con la canalización. | [Ejecución script de R](execute-r-script.md) |
| Text Analytics | Proporcione herramientas de cálculo especializadas para trabajar con texto estructurado y no estructurado. | [Extracción de características de n-gramas a partir de texto](extract-n-gram-features-from-text.md) <br/> [Hash de características](feature-hashing.md) <br/> [Preprocesamiento de texto](preprocess-text.md) |
| Recomendación | Compile modelos de recomendación. | [Evaluate Recommender](evaluate-recommender.md) (Evaluar recomendador) <br/> [Score SVD Recommender](score-svd-recommender.md) (Puntuar recomendador de SVD) <br/> [Train SVD Recommender](train-SVD-recommender.md) (Entrenar recomendador de SVD) |

## <a name="error-messages"></a>Mensajes de error

Conozca los [mensajes de error y códigos de excepción](designer-error-codes.md) que pueden surgir al usar los módulos del diseñador de Azure Machine Learning.
