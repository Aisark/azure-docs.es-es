---
title: Transformación Anular dinamización de Azure Data Factory Mapping Data Flow
description: Transformación Anular dinamización de Azure Data Factory Mapping Data Flow
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348314"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformación Anular dinamización de Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use Anular dinamización en ADF Mapping Data Flow como una forma de convertir un conjunto de datos sin normalizar en una versión más normalizada, ampliando los valores de varias columnas en un único registro a varios registros con los mismos valores en una sola columna.

![Transformación Anular dinamización](media/data-flow/unpivot1.png "Unpivot options 1")

## <a name="ungroup-by"></a>Ungroup By

![Transformación Anular dinamización](media/data-flow/unpivot5.png "Unpivot options 2")

En primer lugar, establezca las columnas que desea agrupar para la agregación dinámica. Establezca una o varias columnas para desagrupar con el + situado junto a la lista de columnas.

## <a name="unpivot-key"></a>Clave de anulación de dinamización

![Transformación Anular dinamización](media/data-flow/unpivot6.png "Unpivot options 3")

La clave dinámica es la columna que ADF dinamizará de fila a columna. De forma predeterminada, cada valor único del conjunto de datos de este campo se dinamizará en una columna. Sin embargo, también puede escribir los valores del conjunto de datos que desea dinamizar en valores de columna.

## <a name="unpivoted-columns"></a>Columnas con dinamización anulada

![Transformación Anular dinamización](media/data-flow//unpivot7.png "Unpivot options 4")

Por último, elija la agregación que se va a usar para los valores dinamizados y cómo desea que las columnas se muestren en la nueva proyección de salida de la transformación.

(Opcional) Puede establecer un patrón de nomenclatura con un prefijo, un término intermedio y el sufijo que se agregará a los nuevos nombres de columna creados a partir de los valores de fila.

Por ejemplo, dinamizar "Ventas" por "Región" simplemente daría lugar a nuevos valores de columna a partir de cada valor de ventas. Por ejemplo:  "25", "50", "1000", etc. Sin embargo, si establece un valor de prefijo "Ventas", "Ventas" se usará como prefijo de los valores.

<img src="media/data-flow/unpivot3.png" width="400">

Al establecer la organización de la columna en "Normal", se agruparán todas las columnas dinámicas con sus valores agregados. Al establecer la organización de las columnas en "Lateral", se alternará entre columna y valor.

![Transformación Anular dinamización](media/data-flow//unpivot7.png "Unpivot options 5")

El conjunto de resultados final de los datos con dinamización anulada muestra los totales de columna ahora sin dinamizar en valores de fila independientes.

## <a name="next-steps"></a>Pasos siguientes

Use la [transformación Dinamizar](data-flow-pivot.md) para dinamizar las filas en columnas.
