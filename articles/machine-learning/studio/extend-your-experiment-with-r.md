---
title: Extender el experimento con R
titleSuffix: Azure Machine Learning Studio
description: Cómo extender la funcionalidad de Azure Machine Learning Studio mediante el lenguaje R con el módulo Ejecutar script de R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 8c1292d0d36874892a286d91b1e367c7336b99aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848803"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure Machine Learning Studio: Extender el experimento con R 
Puede extender la funcionalidad de Azure Machine Learning Studio mediante el lenguaje R con el módulo [Ejecutar script de R][execute-r-script].

Este módulo acepta varios conjuntos de datos de entrada y genera un solo conjunto de datos como salida. Puede escribir un script de R en el parámetro **Script de R** del módulo [Ejecutar script de R][execute-r-script].

Para tener acceso a cada puerto de entrada del módulo, se usa un código similar al siguiente:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Enumeración de todos los paquetes actualmente instalados
La lista de paquetes instalados puede cambiar. Puede encontrar una lista de los paquetes instalados actualmente en [R Packages Supported by Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx) (Paquetes de R compatibles con Azure Machine Learning Studio).

También puede obtener la lista completa y actual de los paquetes instalados especificando el código siguiente en el módulo [Ejecutar script de R][execute-r-script]:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Esto acción envía la lista de paquetes al puerto de salida del módulo [Ejecutar script R][execute-r-script].
Para ver la lista de paquetes, conecte un módulo de conversión, como [Convertir a CSV][convert-to-csv] con la salida izquierda del módulo [Ejecutar script R][execute-r-script], ejecute el experimento y luego haga clic en la salida del módulo de conversión y seleccione **Descargar**. 

![Descarga de los resultados del módulo Convertir en CSV](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importación de paquetes
Puede importar paquetes que no se hayan instalado mediante el uso de los siguientes comandos en el módulo [Ejecutar script de R][execute-r-script]:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

donde el archivo `my_favorite_package.zip` contiene el paquete.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
