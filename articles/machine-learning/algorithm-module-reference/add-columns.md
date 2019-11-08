---
title: 'Add Columns: referencia para los módulos'
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar el módulo Agregar columnas en Azure Machine Learning para concatenar dos conjuntos de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493969"
---
# <a name="add-columns-module"></a>Módulo Add Columns

En este artículo se describe un módulo del diseñador de Azure Machine Learning (versión preliminar).

Utilice este módulo para concatenar dos conjuntos de datos. Combina todas las columnas de los dos conjuntos de datos que especifica como entradas para crear un único conjunto de datos. Si necesita concatenar más de dos conjuntos de datos, use varias instancias de **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Cómo configurar Add Columns
1. Agregue el módulo **Add Columns** (Agregar columnas) a la canalización.

2. Conecte los dos conjuntos de datos que quiere concatenar. Si quiere combinar más de dos conjuntos de datos, puede encadenar varias combinaciones de **Add Columns**.

    - Es posible combinar dos columnas que tienen un número diferente de filas. El conjunto de datos de salida se rellena con los valores que faltan para cada fila de la columna de origen más pequeña.

    - No puede elegir columnas individuales para agregar. Todas las columnas de cada conjunto de datos se concatenan al usar **Add Columns**. Por lo tanto, si quiere agregar solo un subconjunto de las columnas, utilice Select Columns in Dataset (Seleccionar columnas de conjunto de datos) para crear un conjunto de datos con las columnas que quiere.

3. Ejecución de la canalización

### <a name="results"></a>Results
Una vez ejecutada la canalización:

- Para ver las primeras filas del nuevo conjunto de datos, haga clic con el botón derecho en la salida de **Add Columns** y seleccione Visualizar.

El número de columnas en el nuevo conjunto de datos es igual a la suma de las columnas de ambos conjuntos de datos de entrada.

Si hay dos columnas con el mismo nombre en los conjuntos de datos de entrada, se agrega un sufijo numérico al nombre de la columna. Por ejemplo, si hay dos instancias de una columna denominada TargetOutcome, la columna izquierda cambiaría de nombre a TargetOutcome_1 y la columna derecha cambiaría de nombre a TargetOutcome_2.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning. 