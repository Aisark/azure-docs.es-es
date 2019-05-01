---
title: 'Análisis en HDInsight Spark con PySpark, Scala: proceso de ciencia de datos en equipo'
description: Ejemplos del proceso de ciencia de datos en equipo que recorren el uso de PySpark y Scala en un clúster de Azure HDInsight Spark para realizar análisis predictivo.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ce57c37791f9fcf353d08e177eb243f4939b52a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805535"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Tutoriales de ciencia de datos de HDInsight Spark con PySpark y Scala en Azure

En estos tutoriales se usa PySpark y Scala en un clúster de Azure Spark para realizar análisis predictivo. En ellos se siguen los pasos descritos en el proceso de ciencia de datos en equipo. Para obtener información general sobre el proceso de ciencia de datos en equipo, consulte [Proceso de ciencia de datos](overview.md). Para obtener información general de Spark en HDInsight, consulte [Introducción a Spark en HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Los otros tutoriales de ciencia de datos donde se ejecuta el Proceso de ciencia de datos en equipo se agrupan por la **plataforma** que usan. Consulte [Walkthroughs executing the Team Data Science Process](walkthroughs.md) (Tutoriales para ejecutar el Proceso de ciencia de datos en equipo) para ver ejemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Predicción de propinas para taxis con PySpark en Azure Spark

En el tutorial [Uso de Spark en Azure HDInsight](spark-overview.md) se usan datos de taxis de Nueva York para predecir si se da una propina y el intervalo de importes de pago esperado. Se emplea el proceso de ciencia de datos en equipos en un escenario con un [clúster de Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para almacenar y explorar los datos del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente, así como para realizar la ingeniería de sus características. En este tema de introducción se le presenta un clúster de HDInsight Spark y los cuadernos de Jupyter PySpark usados en el resto del tutorial. En estos cuadernos se muestra cómo explorar los datos y luego cómo crear y consumir los modelos. El cuaderno de exploración y modelado de datos avanzado muestra cómo incluir la validación cruzada, el barrido de los hiperparámetros y la evaluación de modelos.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploración y modelado de datos con Spark 
Explore el conjunto de datos y cree, puntúe y evalúe modelos de aprendizaje automático con la ayuda del tema [Creación de modelos de clasificación y regresión para datos con el kit de herramientas de MLib de Spark](spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Consumo de modelos
Para saber cómo puntuar los modelos de clasificación y regresión creados en este tema, consulte [Puntuación y evaluación de modelos de aprendizaje automático creados con Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validación cruzada y barrido de hiperparámetros
Consulte [Exploración y modelado avanzados de datos con Spark](spark-advanced-data-exploration-modeling.md) para saber cómo se pueden entrenar modelos con el barrido de hiperparámetros y la validación cruzada.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Predicción de propinas para taxis mediante Scala en Azure Spark

En el tutorial [Ciencia de datos mediante Scala y Spark en Azure](scala-walkthrough.md) se usan datos de taxis de Nueva York para predecir si se da una propina y el intervalo de importes de pago esperado. Se muestra cómo usar Scala para realizar tareas supervisadas de aprendizaje automático con la biblioteca de aprendizaje automático de Spark (MLlib) y los paquetes de SparkML en un clúster Azure HDInsight Spark. Además, explicaremos cuáles son las tareas que constituyen el [proceso de ciencia de datos](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): exploración e ingesta de datos, visualización, ingeniería de características, modelado y consumo de modelos. Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente:


## <a name="next-steps"></a>Pasos siguientes

Para obtener una explicación de los componentes clave que conforman el proceso de ciencia de datos en equipo, consulte [Información general del proceso de ciencia de datos en equipo](overview.md).

Para obtener una explicación del ciclo de vida del proceso de ciencia de datos en equipo que puede usar para estructurar los proyectos de ciencia de datos, consulte [Team Data Science Process lifecycle](lifecycle.md) (Ciclo de vida del proceso de ciencia de datos en equipo). El ciclo de vida describe el proceso, de principio a fin, que suelen seguir los proyectos al ejecutarlos. 

