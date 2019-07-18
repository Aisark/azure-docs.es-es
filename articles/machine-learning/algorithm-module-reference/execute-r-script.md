---
title: 'Ejecutar script R: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Ejecutar script R en Azure Machine Learning Service para ejecutar código de R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 710d64b445953ae3124830931c8cbb9315d32b83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875707"
---
# <a name="execute-r-script"></a>Ejecución script de R

En este artículo se describe cómo usar el módulo **Ejecutar script R** para ejecutar código de R en el experimento de interfaz visual.

Con R, puede realizar tareas que no son compatibles actualmente con los módulos existentes, como: 
- Crear transformaciones de datos personalizadas
- Usar sus propias métricas para evaluar las predicciones
- Generar modelos mediante algoritmos que no se implementan como módulos independientes en la interfaz visual

## <a name="r-version-support"></a>Compatibilidad con la versión de R

La interfaz visual de Azure Machine Learning Service usa la distribución CRAN (red de archivo de R completa) de R. La versión que se usa actualmente es CRAN 3.5.1.

## <a name="supported-r-packages"></a>Paquetes de R admitidos

El entorno de R viene preinstalado con más de 100 paquetes. Para obtener una lista completa, consulte la sección [Paquetes de R preinstalados](#pre-installed-r-packages).

También puede agregar el código siguiente a cualquier módulo **Ejecutar script R** y para ver los paquetes instalados.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalación de paquetes de R
Para instalar paquetes de R adicionales, use el método `install.packages()`. Asegúrese de especificar el repositorio de CRAN. Se instalan paquetes para cada módulo **Ejecutar script R** y no se pueden compartir entre con otros módulos **Ejecutar script R**.

En este ejemplo se muestra cómo instalar Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Procedimiento para configurar Ejecutar script R

El módulo **Ejecutar script R** contiene código de ejemplo que puede usar como punto de partida. Para configurar el módulo **Ejecutar script R**, proporcione un conjunto de entradas y código para ejecutarlo.

![Módulo de R](media/module/execute-r-script.png)

Los conjuntos de datos almacenados en la interfaz visual se convierten automáticamente en una trama de datos de R cuando se cargan con este módulo.

1.  Agregue el módulo **Ejecutar script R** al experimento.

    > [!NOTE]
    > Todos los datos que se pasan al módulo **Ejecutar script R** se convierten al formato de R `data.frame`.

1. Conecte cualquier entrada que necesite el script. Las entradas son opcionales y pueden incluir datos y código de R adicional.

    * **Dataset1**: haga referencia a la primera entrada como `dataframe1`. El conjunto de datos de entrada debe tener el formato CSV, TSV o ARFF. También puede conectar un conjunto de datos de Azure Machine Learning.

    * **Dataset2**: haga referencia a la segunda entrada como `dataframe2`. Este conjunto de datos también se debe formatear como archivo CSV, TSV o ARFF, o como conjunto de datos de Azure Machine Learning.

    * **Conjunto de scripts**: la tercera entrada acepta archivos ZIP. El archivo ZIP puede contener varios archivos y varios tipos de archivo.

1. En el cuadro de texto **Script de R**, escriba o pegue el script de R válido.

    Para ayudarle a empezar, el cuadro de texto **Script de R** se muestra rellenado previamente con el código de ejemplo, que puede editar o reemplazar.

    * El script debe contener una función denominada `azureml_main`, que es el punto de entrada para este módulo.

    * La función de punto de entrada puede contener hasta dos argumentos de entrada: `Param<dataframe1>` y `Param<dataframe2>`
 
    > [!NOTE]
    >  Puede que el código de R existente necesite pequeños cambios para ejecutarse en un experimento de interfaz visual. Por ejemplo, los datos de entrada que se proporcionan en formato CSV deben convertirse explícitamente en un conjunto de datos para que pueda usarlos en su código. Los tipos de datos y columnas que se usan en el lenguaje R también difieren en algunos aspectos de los tipos de columnas y datos que se usan en la interfaz visual.

1.  **Valor de inicialización aleatorio**: escriba un valor para usarlo en el entorno de R como valor de inicialización aleatorio. Este parámetro equivale a llamar a `set.seed(value)` en el código de R.  

1. Ejecute el experimento.  

## <a name="results"></a>Results

Los módulos **Ejecutar script R** pueden devolver varias salidas, pero se deben proporcionar como tramas de datos de R. Las tramas de datos se convierten en conjuntos de datos de la interfaz visual para su compatibilidad con otros módulos.

Los mensajes estándar y los errores de R se devuelven al registro del módulo.

## <a name="sample-scripts"></a>Scripts de ejemplo

Hay muchas formas de ampliar el experimento mediante el script de R personalizado.  En esta sección se proporciona el código de ejemplo para las tareas comunes.


### <a name="add-r-script-as-an-input"></a>Agregar el script de R como entrada

El módulo **Ejecutar script R** admite archivos de script de R arbitrarios como entradas. Para ello, deben cargarse en el área de trabajo como parte del archivo ZIP.

1. Para cargar un archivo ZIP que contiene el código de R en el área de trabajo, haga clic en **Nuevo**, seleccione **Conjunto de datos**y, a continuación, seleccione **From local file** (De un archivo local) y la opción **Archivo ZIP**.  

1. Compruebe que el archivo comprimido esté disponible en la lista **Saved Datasets** (Conjuntos de datos guardados).

1.  Conecte el conjunto de datos al puerto de entrada del **conjunto de scripts**.

1. Todos los archivos que se encuentran en el archivo ZIP están disponibles durante el tiempo de ejecución del experimento. 

    Si el archivo del conjunto de scripts contiene una estructura de directorios, esta se conserva. Sin embargo, debe modificar el código para anteponer el directorio **./Conjunto de scripts** a la ruta de acceso.

### <a name="process-data"></a>Datos de proceso

En el ejemplo siguiente se muestra cómo escalar y normalizar los datos de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leer un archivo ZIP como entrada

En este ejemplo se muestra cómo usar un conjunto de datos en un archivo ZIP como entrada para el módulo **Ejecutar script R**.

1. Cree el archivo de datos en formato CSV y asígnele el nombre "mydatafile.csv".
1. Cree un archivo ZIP y agregue el archivo CSV al archivo.
1. Cargue el archivo comprimido en su área de trabajo de Azure Machine Learning. 
1. Conecte el conjunto de datos resultante en la entrada **ScriptBundle** del módulo **Ejecutar script R**.
1. Use el código siguiente para leer los datos en formato CSV del archivo comprimido.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar filas

En este ejemplo se muestra cómo replicar registros positivos en un conjunto de datos para equilibrar la muestra:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Pasar objetos R entre módulos Ejecutar script R

Puede pasar objetos R entre instancias del módulo **Ejecutar script R** mediante el mecanismo de serialización interno. En este ejemplo se da por supuesto que quiere mover el objeto de R denominado `A` entre dos módulos **Ejecutar script R**.

1. Agregue el primer módulo **Ejecutar script R** al experimento y escriba el siguiente código en el cuadro de texto **Script de R** para crear un objeto serializado `A` como columna en la tabla de datos de salida del módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversión explícita al tipo entero se realiza porque la función de serialización devuelve los datos en el formato de R `Raw`, lo que no es compatible con la interfaz visual.

1. Agregue una segunda instancia del módulo **Ejecutar script R** y conéctelo al puerto de salida del módulo anterior.

1. Escriba el siguiente código en el cuadro de texto **Script de R** para extraer el objeto `A` de la tabla de datos de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Paquetes de R instalados previamente

Lista actual de paquetes de R preinstalados disponibles para su uso:

|              |            | 
|--------------|------------| 
| Paquete      | Versión    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| conjuntos de datos     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0,3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progreso     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0,13       | 
| herramientas        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 