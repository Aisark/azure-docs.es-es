---
title: 'Regresión de red neuronal: Referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Regresión de red neuronal en Azure Machine Learning Service para crear un modelo de regresión usando un algoritmo de red neuronal personalizable.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 54518d21db0c9b14cbb7b4fc4316d1db2b871573
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692736"
---
# <a name="neural-network-regression-module"></a>Módulo Regresión de red neuronal

*Crea un modelo de regresión usando un algoritmo de red neuronal*  
  
 Categoría: Machine Learning/Inicializar modelo/Regresión
  
## <a name="module-overview"></a>Información general sobre el módulo  

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Utilice este módulo para crear un modelo de regresión usando un algoritmo de red neuronal personalizable.
  
 Aunque las redes neuronales son ampliamente conocidas por su uso en problemas complejos de aprendizaje profundo y modelado, como el reconocimiento de imágenes, se adaptan fácilmente a los problemas de regresión. Cualquier clase de modelo estadístico puede denominarse red neuronal si utiliza pesos adaptables y puede aproximar funciones no lineales de sus entradas. Por lo tanto, la regresión de red neuronal es adecuada en los problemas para los que un modelo de regresión más tradicional no puede encontrar una solución.
  
 La regresión de red neuronal es un método de aprendizaje supervisado y, por lo tanto, requiere un *conjunto de datos etiquetado*, que incluya una columna de etiqueta. Dado que un modelo de regresión predice un valor numérico, la columna de etiqueta debe ser de un tipo de datos numérico.  
  
 Puede entrenar el modelo proporcionando el modelo y el conjunto de datos etiquetado como entrada para [Entrenar modelo](./train-model.md). Después, el modelo entrenado puede utilizarse para predecir valores para los nuevos ejemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar la regresión de red neuronal 

Las redes neuronales se pueden personalizar ampliamente. En esta sección se describe cómo crear un modelo mediante dos métodos:
  
+ [Crear un modelo de red neuronal con la arquitectura predeterminada](#bkmk_DefaultArchitecture)  
  
    Si acepta la arquitectura predeterminada de red neuronal, use el panel **Propiedades** para establecer los parámetros que controlan el comportamiento de la red neuronal, como el número de nodos de la capa oculta, la velocidad de aprendizaje y la normalización.

    Empiece aquí si no está familiarizado con las redes neuronales. El módulo admite muchas personalizaciones, así como el ajuste de modelos, sin un conocimiento profundo de las redes neuronales. 

+ Definir una arquitectura personalizada para una red neuronal 

    Use esta opción si desea agregar capas ocultas adicionales o personalizar completamente la arquitectura de red, sus conexiones y las funciones de activación.
    
    Esta opción es mejor si ya está un poco familiarizado con las redes neuronales. Use el lenguaje Net# para definir la arquitectura de red.  

##  <a name="bkmk_DefaultArchitecture"></a>Crear un modelo de red neuronal con la arquitectura predeterminada
  
1.  Agregue el módulo **Regresión de red neuronal** a la canalización en la interfaz. Puede encontrar este módulo en **Machine Learning**, **Inicializar**, en la categoría **Regresión**. 
  
2. Para indicar cómo quiere que se entrene el modelo, establezca la opción **Create trainer mode** (Crear modo entrenador).  
  
    -   **Single Parameter** (Parámetro único): Elija esta opción si ya sabe cómo desea configurar el modelo.  

3.  En **Hidden layer specification** (Especificación de capa oculta), seleccione **Fully connected case** (Caso completamente conectado). Esta opción crea un modelo mediante la arquitectura predeterminada de red neuronal, lo que, para un modelo de regresión de red neuronal, tiene estos atributos:  
  
    + La red tiene una sola capa oculta.
    + La capa de salida está completamente conectada a la capa oculta, y esta a la capa de entrada.
    + El usuario puede establecer el número de nodos de la capa oculta (el valor predeterminado es 100).  
  
    Dado que el número de nodos de la capa de entrada viene determinado por el número de características de los datos de entrenamiento, en un modelo de regresión solo puede haber un nodo en la capa de salida.  
  
4. Para **Número de nodos ocultos**, escriba el número de nodos ocultos. El valor predeterminado es una capa oculta con 100 nodos. (Esta opción no está disponible si se define una arquitectura personalizada con Net#).
  
5.  Para **Velocidad de aprendizaje**, escriba un valor que defina el paso llevado a cabo en cada iteración, antes de la corrección. Un valor mayor para la velocidad de aprendizaje puede hacer que el modelo converja con mayor rapidez, pero puede superar los mínimos locales.

6.  Para **Number of learning iterations** (Número de iteraciones de aprendizaje), especifique el número máximo de veces que el algoritmo procesa los casos de entrenamiento.

7.  Para **El diámetro de pesos de aprendizaje inicial, escriba un valor que determine los pesos de nodo en el inicio del proceso de aprendizaje.

8.  Para **The momentum** (El momentum), escriba el valor que se debe aplicar durante el aprendizaje como peso en los nodos de iteraciones anteriores.

10. Seleccione la opción **Shuffle examples** (Ejemplos de orden aleatorio), para cambiar el orden de los casos entre iteraciones. Si anula la selección de esta opción, los casos se procesan exactamente en el mismo orden cada vez que se ejecuta la canalización.
  
11. Para **Random number seed** (Inicialización de número aleatorio), puede escribir opcionalmente un valor que se usará como inicialización. Especificar un valor de inicialización es útil cuando desea asegurar la repetibilidad entre ejecuciones de la misma canalización.
  
13. Conecte un conjunto de datos de entrenamiento y uno de los [módulos de entrenamiento](module-reference.md): 
  
    -   Si establece **Create trainer mode** (Crear modo entrenador) en **Single Parameter** (Parámetro único), use [Entrenar modelo](./train-model.md).  
  
   
14. Ejecución de la canalización  

## <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para ver un resumen de los parámetros del modelo, junto con los pesos de característica aprendidos en el entrenamiento y otros parámetros de la red neuronal, haga clic con el botón derecho en la salida de [Train Model](./train-model.md) (Entrenar modelo) y seleccione **Visualizar**.  

+ Para guardar una instantánea del modelo entrenado, haga clic con el botón derecho en el resultado de **Trained model** (Modelo entrenado) y seleccione **Save As Trained Model** (Guardar como modelo entrenado). Este modelo no se actualiza en las ejecuciones sucesivas de la misma canalización.


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 