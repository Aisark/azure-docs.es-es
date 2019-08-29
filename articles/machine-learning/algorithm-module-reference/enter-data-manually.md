---
title: 'Introducción manual de datos: Referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Introducción manual de datos en Azure Machine Learning Service para crear un conjunto de datos pequeño escribiendo valores. El conjunto de datos puede tener varias columnas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d7e6cede12f5a348f59db83b31b19c89266dfdf7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128832"
---
# <a name="enter-data-manually-module"></a>Módulo Introducción manual de datos

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Utilice este módulo para crear un conjunto de datos pequeño escribiendo valores. El conjunto de datos puede tener varias columnas.
  
Este módulo puede ser útil en escenarios como los siguientes:  
  
- Generación de un pequeño conjunto de valores para las pruebas  
  
- Creación de una lista breve de etiquetas
  
- Escritura de una lista de nombres de columna para insertarlas en un conjunto de datos

## <a name="enter-data-manually"></a>Introducción manual de datos 
  
1.  Agregue el módulo [Introducción manual de datos](./enter-data-manually.md) al experimento. Puede encontrar el módulo en la categoría **Entrada y salida de datos** de Azure Machine Learning. 
  
2.  Para **DataFormat**, seleccione una de las siguientes opciones. Estas opciones determinan cómo se deben analizar los datos proporcionados. Los requisitos para cada formato varían en gran medida, por lo que debe asegurarse de leer los temas relacionados.  
  
    -   **ARFF**. El formato de archivo de relación de atributos, usado por Weka.   
  
    -   **CSV**. Formato de valores separados por comas. Para obtener más información, consulte [Convert to CSV](./convert-to-csv.md) (Convertir a CSV).  
  
    -   **SVMLight**. Formato usado por Vowpal Wabbit y otros marcos de aprendizaje automático.  
  
    -   **TSV**. Formato de valores separados por tabulaciones.

     Si elige un formato y no proporciona datos que cumplan las especificaciones de formato, se produce un error en tiempo de ejecución.
  
3.  Haga clic dentro del cuadro de texto **Datos** para empezar a escribir datos. Los formatos siguientes requieren una atención especial:  
  
    - **CSV**:  para crear varias columnas, pegue texto separado por comas o escriba varias columnas utilizando comas entre los campos.
  
        Si selecciona la opción **HasHeader**, puede usar la primera fila de valores como encabezado de columna.  
  
        Si anula la selección de esta opción, se usan los nombres de columnas, Col1, Col2, etc. Puede agregar o cambiar los nombres de columnas más adelante mediante [Editar metadatos](./edit-metadata.md).  
  
    - **TSV**: para crear varias columnas, pegue texto separado por tabulaciones o escriba varias columnas utilizando tabulaciones entre los campos.  
  
        Si selecciona la opción **HasHeader**, puede usar la primera fila de valores como encabezado de columna.  
  
        Si anula la selección de esta opción, se usan los nombres de columnas, Col1, Col2, etc. Puede agregar o cambiar los nombres de columnas más adelante mediante [Editar metadatos](./edit-metadata.md).  
  
    -   **ARFF**:  pegue en un archivo de formato ARFF existente. Si escribe los valores directamente, no olvide agregar el encabezado opcional y los campos de atributos requeridos al principio de los datos. 
    
        Por ejemplo, las filas de encabezado y de atributo siguientes podrían agregarse a una lista simple. El encabezado de columna sería `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: escriba o pegue los valores con el formato SVMLight.  
  
        Por ejemplo, en el ejemplo siguiente se representa el primer par de líneas del conjunto de datos Blood Donation, en formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Al ejecutar el módulo [Introducción manual de datos](./enter-data-manually.md), estas líneas se convierten en un conjunto de datos de columnas y valores de índice como sigue:  
  
        |Col1|Col2|Col3|Col4|Etiquetas|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Presione ENTRAR después de cada fila, para iniciar una nueva línea.  
  
     **Asegúrese de presionar ENTRAR después de la fila final.** 
     
     Si presiona ENTRAR varias veces para agregar varias filas finales vacías, la fila final vacía se quita, pero las demás filas vacías se tratan como valores que faltan.  
  
     Si crea filas con valores que faltan, siempre puede filtrarlas más adelante.  
  
5.  Haga clic con el botón derecho en el módulo y seleccione **Ejecutar seleccionados** para analizar los datos y cargarlos en el área de trabajo como un conjunto de datos.  
  
     Para ver el conjunto de datos, haga clic en el puerto de salida y seleccione **Visualizar**.  
## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 