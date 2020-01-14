---
title: Etiquetado de imágenes en un proyecto de etiquetado
title.suffix: Azure Machine Learning
description: Obtenga información sobre el uso de las herramientas de etiquetado de datos en un proyecto de etiquetado de Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7ff3651d082f1649326e1b41b13fa177069c40d3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/28/2019
ms.locfileid: "75532348"
---
# <a name="tag-images-in-a-labeling-project"></a>Etiquetado de imágenes en un proyecto de etiquetado

Después de que el administrador del proyecto [cree un proyecto de etiquetado](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-labeling-projects#create-a-labeling-project) en Azure Machine Learning, puede usar la herramienta de etiquetado para preparar rápidamente los datos para un proyecto de Machine Learning. En este artículo se describe:

> [!div class="checklist"]
> * Cómo acceder a proyectos de etiquetado
> * Herramientas de etiquetado
> * Cómo usar las herramientas para tareas específicas de etiquetado

## <a name="prerequisites"></a>Prerequisites

* La dirección URL del portal de etiquetado para un proyecto de etiquetado de datos en ejecución
* Una [cuenta Microsoft](https://account.microsoft.com/account) o una cuenta de Azure Active Directory para la organización y el proyecto

> [!NOTE]
> El administrador del proyecto puede encontrar la dirección URL del portal de etiquetado en la pestaña **Detalles** de la página **Detalles del proyecto**.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Inicio de sesión en el portal de etiquetado del proyecto

Vaya a la dirección URL del portal de etiquetado que le proporcionó el administrador del proyecto. Inicie sesión con la cuenta de correo electrónico que el administrador usó para agregarle al equipo. Para la mayoría de los usuarios, será su cuenta Microsoft. Si el proyecto de etiquetado usa Azure Active Directory, esa será la forma de iniciar sesión.

## <a name="understand-the-labeling-task"></a>Descripción de la tarea de etiquetado

Después de iniciar sesión, verá la página de información general del proyecto.

Vaya a **View detailed instructions** (Ver instrucciones detalladas). Estas instrucciones son específicas del proyecto. Explican el tipo de datos a los que se enfrenta, cómo debe tomar sus decisiones y otra información pertinente. Después de leer esta información, vuelva a la página del proyecto y seleccione **Start labeling** (Iniciar etiquetado).

## <a name="common-features-of-the-labeling-task"></a>Características comunes de la tarea de etiquetado

En todas las tareas de etiquetado de imágenes, elija una o varias etiquetas adecuadas del conjunto especificado por el administrador del proyecto. Puede seleccionar las nueve primeras etiquetas con las teclas numéricas del teclado.  

En las tareas de clasificación de imágenes, puede elegir ver varias imágenes simultáneamente. Use los iconos situados encima del área de imagen para seleccionar el diseño. Para seleccionar todas las imágenes mostradas al mismo tiempo, use **Select all** (Seleccionar todo). Para seleccionar imágenes individuales, use el botón de selección circular en la esquina superior derecha de la imagen. Debe seleccionar al menos una imagen para aplicar una etiqueta. Si selecciona varias imágenes, la etiqueta que seleccione se aplicará a todas las imágenes seleccionadas.

Aquí, hemos elegido un diseño de dos por dos y vamos a aplicar la etiqueta "Mammal" a las imágenes del oso y la orca. La imagen del tiburón ya se ha etiquetado como "Cartilaginous fish" y la iguana todavía no se ha etiquetado.

![Múltiples diseños de imagen y selección](./media/how-to-label-images/layouts.png)

> [!Important] 
> Cambie el diseño solo cuando tenga una página nueva de datos sin etiquetar. El cambio de diseño borra el trabajo de etiquetado en curso de la página.

Azure habilita el botón **Enviar** una vez etiquetadas todas las imágenes de la página. Seleccione **Submit** (Enviar) para guardar el trabajo.

Una vez que haya enviado etiquetas para los datos con los que está trabajando, Azure actualizará la página con un nuevo conjunto de imágenes de la cola de trabajo.

## <a name="tag-images-for-multi-class-classification"></a>Etiquetado de imágenes para la clasificación en varias clases

Si su proyecto es del tipo "multiclase de clasificación de imágenes", asignará una sola etiqueta a toda la imagen. Para revisar las instrucciones en cualquier momento, vaya a la página **Instructions** (Instrucciones) y seleccione **View detailed instructions** (Ver instrucciones detalladas).

Si ve que ha cometido un error después de asignar una etiqueta a una imagen, puede corregirla. Seleccione la cruz "**X**" en la etiqueta que se muestra debajo de la imagen para borrar la etiqueta. O bien, seleccione la imagen y elija otra clase. El valor recién seleccionado reemplazará a la etiqueta aplicada previamente.

## <a name="tag-images-for-multi-label-classification"></a>Etiquetado de imágenes para la clasificación en varias clases

Si está trabajando en un proyecto con clasificación con varias etiquetas, aplicará una *o más* etiquetas a una imagen. Para ver las instrucciones específicas del proyecto, seleccione **Instructions** (Instrucciones) y vaya a **View detailed instructions** (Ver instrucciones detalladas).

Seleccione la imagen que desea etiquetar y, a continuación, seleccione la etiqueta. La etiqueta se aplica a todas las imágenes seleccionadas y, a continuación, se anula la selección de las imágenes. Para aplicar más etiquetas, debe volver a seleccionar las imágenes. En la animación siguiente se muestra el etiquetado con varias etiquetas:

1. **Select all** (Seleccionar todo) se usa para aplicar la etiqueta "Ocean".
1. Se selecciona una sola imagen y se etiqueta como "Closeup".
1. Se seleccionan tres imágenes y se etiquetan como "Wide angle".

![La animación muestra el flujo multietiqueta](./media/how-to-label-images/multilabel.gif)

Para corregir un error, haga clic en "**X**" para borrar etiquetas individuales, o seleccione las imágenes y elija la etiqueta para borrar la etiqueta de todas las imágenes seleccionadas. Este escenario se muestra aquí. Al hacer clic en "Land", se borra la etiqueta de las dos imágenes seleccionadas.

![Una captura de pantalla muestra la anulación de varias selecciones](./media/how-to-label-images/multiple-deselection.png)

Azure solo habilitará el botón **Enviar** cuando haya aplicado al menos una etiqueta a cada imagen. Seleccione **Submit** (Enviar) para guardar el trabajo.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Etiquetado de imágenes y especificación de rectángulos de selección para la detección de objetos

Si el proyecto es del tipo "Identificación del objeto (rectángulo de selección)", deberá especificar uno o varios rectángulos de selección en la imagen y aplicar una etiqueta a cada cuadro. Las imágenes pueden tener varios rectángulos de selección, cada uno con una sola etiqueta. Use **View detailed instructions** (Ver instrucciones detalladas) para determinar si se usan varios rectángulos de selección en el proyecto.

1. Seleccione una etiqueta para el rectángulo de selección que va a crear.
1. Seleccione la herramienta **Cuadro rectangular**![Herramienta Cuadro rectangular](./media/how-to-label-images/rectangular-box-tool.png) o presione "R".
3. Haga clic y arrastre diagonalmente en el destino para crear un rectángulo de selección aproximado. Para ajustar el rectángulo de selección, arrastre los bordes o las esquinas.

![Una captura de pantalla muestra la creación del rectángulo de selección.](./media/how-to-label-images/bounding-box-sequence.png)

Para eliminar un rectángulo de selección, haga clic en la forma de X que aparece junto al rectángulo de selección una vez creado.

No se puede cambiar la etiqueta de un rectángulo de selección existente. Si asigna una etiqueta por error, tendrá que eliminar el rectángulo de selección y crear uno nuevo con la etiqueta correcta.

De forma predeterminada, puede editar los rectángulos de selección existentes. La herramienta **Bloquear/desbloquear regiones**![Bloquear/desbloquear regiones](./media/how-to-label-images/lock-bounding-boxes-tool.png) o "L" alterna ese comportamiento. Si las regiones están bloqueadas, solo puede cambiar la forma o la ubicación de un nuevo rectángulo de selección.

Use la herramienta **Manipulación de regiones**![Herramienta Manipulación de regiones](./media/how-to-label-images/regions-tool.png) o "M" para ajustar un cuadro de límite existente. Arrastre los bordes o esquinas para ajustar la forma. Haga clic en el interior para poder arrastrar todo el rectángulo de selección. Si no puede editar una región, es probable que haya activado la herramienta **Bloquear/desbloquear regiones**.

Use la herramienta **Cuadro basado en plantilla**![Herramienta Cuadro de plantilla](./media/how-to-label-images/template-box-tool.png) o "T" para crear varios cuadros de límite del mismo tamaño. Si la imagen no tiene rectángulos de selección y activa los recuadros basados en plantillas, la herramienta generará cuadros de 50 por 50 píxeles. Si crea un rectángulo de selección y, a continuación, activa los rectángulos basados en plantillas, los nuevos rectángulos de selección tendrán el tamaño del último rectángulo. Se puede cambiar el tamaño de los cuadros basados en plantillas después de la selección de ubicación. Al cambiar el tamaño de un rectángulo basado en una plantilla, solo se cambia el tamaño de ese rectángulo en concreto.

Para eliminar *todos* los rectángulos de selección de la imagen actual, seleccione la herramienta **Eliminar todas las regiones**![Herramienta Eliminar regiones](./media/how-to-label-images/delete-regions-tool.png).

Después de crear los rectángulos de selección de una imagen, seleccione **Submit** (Enviar) para guardar el trabajo; de lo contrario, no se guardará el trabajo en curso.

## <a name="finish-up"></a>Finalizar

Cuando se envía una página de datos etiquetados, Azure asigna datos nuevos sin etiquetar desde una cola de trabajo. Si no hay más datos sin etiquetar disponibles, verá un mensaje de aviso junto con un vínculo a la página principal del portal.

Cuando haya terminado de etiquetar, seleccione su nombre en la esquina superior derecha del portal de etiquetado y, a continuación, seleccione **sign-out** (Cerrar sesión). Si no lo hace, Azure acabará finalizando su "tiempo de espera" y asignará sus datos a otro etiquetador.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [entrenar modelos de clasificación de imágenes en Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml).
* Obtenga información sobre la [detección de objetos con Azure y la técnica denominada "R-CNN más rápida"](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/).
