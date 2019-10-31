---
title: 'Multiclass Neural Network: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Multiclass Neural Network (Red neuronal multiclase) en Azure Machine Learning Service para crear un modelo de red neuronal que pueda usarse para predecir un destino que tenga varios resultados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 3322f2be3c5c6f819adbc9d83ee9eb6b6577f707
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692722"
---
# <a name="multiclass-neural-network-module"></a>Módulo Multiclass Neural Network

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Use este módulo para crear un modelo de red neuronal que se pueda usar para predecir un destino que tenga varios valores. 

Por ejemplo, las redes neurales de este tipo podrían usarse en tareas complejas de visión informática, como el reconocimiento de números o letras, la clasificación de documentos y el reconocimiento de patrones.

La clasificación mediante redes neuronales es un método de aprendizaje supervisado y, por lo tanto, requiere un *conjunto de datos etiquetado* que incluya una columna de etiquetas.

Puede entrenar el modelo proporcionando el modelo y el conjunto de datos etiquetado como entrada para [Entrenar modelo](./train-model.md). Después, el modelo entrenado puede utilizarse para predecir valores para los nuevos ejemplos de entrada.  

## <a name="about-neural-networks"></a>Acerca de las redes neuronales

Una red neuronal es un conjunto de capas interconectadas. Las entradas son la primera capa y se conectan a una capa de salida mediante un grafo acíclico que consta de nodos y aristas ponderadas.

Entre las capas de entrada y salida puede insertar varias capas ocultas. La mayoría de tareas predictivas pueden realizarse fácilmente con solo una o varias capas ocultas. Sin embargo, las investigaciones recientes han demostrado que las redes neuronales profundas (DNN) con muchas capas pueden ser eficaces en tareas complejas, como en el reconocimiento de imágenes o de voz. Las capas sucesivas se usan para modelar los crecientes niveles de profundidad semántica.

La relación entre entradas y salidas se aprende mediante el entrenamiento de la red neuronal con los datos de entrada. La dirección del grafo procede desde las entradas a través de la capa oculta y hacia la capa de salida. Todos los nodos de una capa se conectan mediante las aristas ponderadas a los nodos de la capa siguiente.

Para calcular la salida de la red para una entrada determinada, se calcula un valor en cada nodo en las capas ocultas y en la capa de salida. El valor se establece calculando la suma ponderada de los valores de los nodos de la capa anterior. A continuación, se aplica una función de activación a esa suma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar una red neuronal multiclase

1. Agregue el módulo **Red neuronal multiclase** a la canalización en la interfaz. Puede encontrar este módulo en **Machine Learning**, **Inicializar**, en la categoría **Clasificación**.

2. **Create trainer mode** (Crear modo entrenador): Use esta opción para especificar cómo quiere que se entrene al modelo:

    - **Single Parameter** (Parámetro único): Elija esta opción si ya sabe cómo desea configurar el modelo.

    

3. **Hidden layer specification** (Especificación de capa oculta): Seleccione el tipo de arquitectura de red que se va a crear.

    - **Fully connected case** (Caso completamente conectado): Seleccione esta opción para crear un modelo con la arquitectura de red neuronal predeterminada. Para los modelos de red neuronal multiclase, los valores predeterminados son los siguientes:

        - Una capa oculta.
        - La capa de salida está conectada por completo a la capa oculta.
        - La capa oculta está conectada por completo a la capa de entrada.
        - El número de nodos de la capa de entrada está determinado por el número de características de los datos de entrenamiento.
        - El usuario puede establecer el número de nodos de la capa oculta. El valor predeterminado es 100.
        - El número de nodos de la capa de salida depende del número de clases.
  
   

5. **Number of hidden nodes** (Número de nodos ocultos): Esta opción le permite personalizar el número de nodos ocultos en la arquitectura predeterminada. Escriba el número de nodos ocultos. El valor predeterminado es una capa oculta con 100 nodos.

6. **Velocidad de aprendizaje**: Defina el tamaño del paso llevado a cabo en cada iteración, antes de la corrección. Un valor mayor para la velocidad de aprendizaje puede hacer que el modelo converja con mayor rapidez, pero puede superar los mínimos locales.

7. **Number of learning iterations** (Número de iteraciones de aprendizaje): Especifique el número máximo de veces que el algoritmo debe procesar los casos de entrenamiento.

8. **The initial learning weights diameter** (El diámetro de pesos de aprendizaje inicial): Escriba un valor que determine los pesos de nodo en el inicio del proceso de aprendizaje.

9. **The momentum** (El momento): Especifique una ponderación que se debe aplicar durante el aprendizaje a los nodos de iteraciones anteriores.
  
11. **Shuffle examples** (Ordenar ejemplos aleatoriamente): Seleccione esta opción para ordenar de forma aleatoria los casos entre iteraciones.

    Si anula la selección de esta opción, los casos se procesan exactamente en el mismo orden cada vez que se ejecuta la canalización.

12. **Número de iniciación aleatorio**: Escriba un valor para usar como inicialización si desea asegurar la repetibilidad entre ejecuciones de la misma canalización.

14. Conecte un conjunto de datos de entrenamiento y uno de los [módulos de entrenamiento](module-reference.md): 

    - Si establece **Create trainer mode** (Crear modo entrenador) en **Single Parameter** (Parámetro único), use [Entrenar modelo](train-model.md).  
  

## <a name="results"></a>Results

Una vez completado el entrenamiento:

- Para ver un resumen de los parámetros del modelo, junto con los pesos de característica aprendidos en el entrenamiento y otros parámetros de la red neuronal, haga clic con el botón derecho en la salida de [Train Model](./train-model.md) (Entrenar modelo) y seleccione **Visualizar**.  

- Para guardar una instantánea del modelo entrenado, haga clic con el botón derecho en el resultado de **Trained model** (Modelo entrenado) y seleccione **Save As Trained Model** (Guardar como modelo entrenado). Este modelo no se actualiza en las ejecuciones sucesivas de la misma canalización.


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 