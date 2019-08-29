---
title: 'Combinación de datos: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Aprenda a usar el módulo de combinación Combinación de datos en el servicio Azure Machine Learning Service para combinar conjuntos de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: 5fdb822a445e8a62cd36e6dd05aa218b9b41ce9e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128733"
---
# <a name="join-data"></a>Combinación de datos

En este artículo se describe cómo usar el módulo **Combinación de datos** en la interfaz visual del servicio Azure Machine Learning Service para combinar dos conjuntos de datos mediante una operación de combinación con estilo de base de datos.  

## <a name="how-to-configure-join-data"></a>Configuración de Combinación de datos

Para realizar una combinación en dos conjuntos de datos, deben estar relacionados por una columna de clave. También se admiten las claves compuestas con varias columnas. 

1. Agregue los conjuntos de datos que quiere combinar y, luego, arrastre el módulo **Combinación de datos** al experimento. 

    Puede encontrar el módulo en la categoría **Transformación de datos**, en **Manipulación**.

1. Conecte los conjuntos de datos al módulo **Combinación de datos**. 
 
1. Seleccione **Iniciar el selector de columnas** para elegir las columnas de clave. Recuerde que debe elegir columnas tanto para la entrada izquierda como para la derecha.

    Para una sola clave:

    Seleccione una sola columna de clave para ambas entradas.
    
    Para una clave compuesta:

    Seleccione todas las columnas de clave de la entrada izquierda y la entrada derecha en el mismo orden. El módulo **Combinación de datos** combinará las tablas cuando coincidan todas las columnas de clave. Active la opción **Permitir duplicados y conversar el orden de las columnas en la selección** si el orden de las columnas no es igual que en la tabla original. 

    ![selector de columnas](media/module/join-data-column-selector.png)


1. Active la opción **Match case** (Coincidir mayúsculas y minúsculas) si quiere conservar la distinción entre mayúsculas y minúsculas en una combinación de columnas de texto. 
   
1. Use la lista desplegable **Tipo de combinación** para especificar cómo se deben combinar los conjuntos de datos.  
  
    * **Combinación interna**: una *combinación interna* es la operación de combinación más común. Devuelve las filas combinadas solo cuando coinciden los valores de las columnas de clave.  
  
    * **Combinación externa izquierda**: una *combinación externa izquierda* devuelve filas combinadas para todas las filas de la tabla izquierda. Si hay una fila en la tabla izquierda sin filas coincidentes en la tabla derecha, la fila devuelta contiene valores faltantes para todas las columnas que provienen de la tabla derecha. También puede especificar un valor de reemplazo para los valores faltantes.  
  
    * **Combinación externa completa**: una *combinación externa completa* devuelve todas las filas de la tabla izquierda (**table1**) y de la tabla derecha (**table2**).  
  
         Para cada una de las filas de cualquiera de las tablas sin filas coincidentes en la otra tabla, el resultado incluye una fila que contiene valores faltantes.  
  
    * **Semicombinación izquierda**: una *semicombinación izquierda* solo devuelve los valores de la tabla izquierda cuando coinciden los valores de las columnas de clave.  

1. Para la opción **Keep right key columns in joined table** (Mantener las columnas de clave derechas en la tabla combinada):

    * active esta opción para ver las claves de ambas tablas de entrada.
    * Anule la selección para devolver solo las columnas de clave de la entrada izquierda.

1. Ejecute el experimento o seleccione el módulo Combinación de datos y seleccione **Ejecutar seleccionado** para realizar la combinación.

1. Para ver los resultados, haga clic con el botón derecho en **Combinación de datos** > **Conjunto de datos de resultados** > **Visualizar**.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 