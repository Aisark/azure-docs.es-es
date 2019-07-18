---
title: 'Entrenamiento de un modelo: Custom Translator'
titleSuffix: Azure Cognitive Services
description: Entrenar un modelo es un paso importante al crear un modelo de traducción. El entrenamiento se produce según los documentos que seleccione para ello.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 8804285bf419bce5ca85cc5070cd47ce9a87392a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447965"
---
# <a name="train-a-model"></a>Entrenamiento de un modelo

Entrenar un modelo es un paso importante al crear un modelo de traducción, ya que sin ese entrenamiento el modelo no se puede generar. El entrenamiento se produce según los documentos que seleccione para ello.

Para entrenar un modelo:

1.  Seleccione el proyecto en el que desea crear un modelo.

2.  La pestaña Datos del proyecto mostrará todos los documentos pertinentes para el par de idiomas del proyecto. Seleccione manualmente los documentos que desea usar para entrenar el modelo. Puede seleccionar documentos de aprendizaje, optimización y prueba desde esta pantalla. También basta con seleccionar el conjunto de aprendizaje y que Custom Translator cree automáticamente los conjuntos de optimización y prueba.

    -  Nombre del documento: el nombre asignado.

    -  Emparejamiento: indica si se trata de un documento paralelo o monolingüe. En este momento, no se admiten los documentos monolingües para el entrenamiento.

    -  Tipo de documento: puede ser de entrenamiento, ajuste, prueba o diccionario.

    -  Par de idiomas: muestra los idiomas de origen y de destino para el proyecto.

    -  Frases de origen: muestra el número de frases extraídas del archivo de origen.

    -  Frases de destino: muestra el número de frases extraídas del archivo de destino.

    ![Entrenar modelo](media/how-to/how-to-train-model.png)

3.  Haga clic en el botón Entrenar.

4.  En el cuadro de diálogo, especifique un nombre para el modelo.

5.  Haga clic en Entrenar modelo.

    ![Cuadro de diálogo Entrenar modelo](media/how-to/how-to-train-model-2.png)

6.  Custom Translator enviará el entrenamiento y mostrará el estado del mismo en la pestaña de modelos.

    ![Página Entrenar modelo](media/how-to/how-to-train-model-3.png)

>[!Note]
>El traductor personalizado es compatible con 10 modelos de aprendizaje de un área de trabajo en cualquier momento dado.


## <a name="edit-a-model"></a>Edición de un modelo

Puede editar un modelo mediante el vínculo Editar de la página Detalles del modelo.

1.  Haga clic en el icono de lápiz.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

2.  En el cuadro de diálogo cambie,

    1.  Nombre del modelo (obligatorio): asigne un nombre descriptivo al modelo.

        ![Cuadro de diálogo Editar modelo](media/how-to/how-to-edit-model-dialog.png)

3.  Haga clic en Guardar.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [ver los detalles del modelo](how-to-view-model-details.md).
