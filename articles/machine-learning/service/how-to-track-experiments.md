---
title: Métricas de registro durante las ejecuciones de entrenamiento
titleSuffix: Azure Machine Learning service
description: Puede realizar un seguimiento de sus experimentos y supervisar las métricas para mejorar el proceso de creación del modelo. Aprenda a agregar el registro al script de entrenamiento, enviar el experimento, comprobar el progreso de un trabajo en ejecución y ver los resultados de una ejecución.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 41797caa89108448f0eaa27309046c01d7432823
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494634"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Las métricas de registro durante el entrenamiento se ejecuta en Azure Machine Learning

Con el servicio Azure Machine Learning, puede realizar un seguimiento de sus experimentos y supervisar las métricas para mejorar el proceso de creación de modelos. En este artículo, obtenga información sobre cómo agregar el registro para el script de entrenamiento, envía una ejecución de experimento, supervisar la ejecución y ver los resultados de una ejecución.

## <a name="list-of-training-metrics"></a>Lista de métricas de entrenamiento 

Las siguientes métricas se pueden agregar a una ejecución durante el entrenamiento de un experimento. Para ver una lista más detallada de aquello de lo que puede realizar el seguimiento en una ejecución, consulte la [documentación de referencia de la clase Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Función de Python | Notas|
|----|:----|:----|
|Valores escalares |Función:<br>`run.log(name, value, description='')`<br><br>Ejemplo:<br>run.log("accuracy", 0.95) |Registra un valor numérico o de cadena en la ejecución con el nombre especificado. Al registrar una métrica en una ejecución, esa métrica se almacena en el registro de ejecución en el experimento.  Puede registrar la misma métrica varias veces dentro de una ejecución y el resultado se considerará un vector de esa métrica.|
|Listas|Función:<br>`run.log_list(name, value, description='')`<br><br>Ejemplo:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Registra una lista de valores en la ejecución con el nombre especificado.|
|Fila|Función:<br>`run.log_row(name, description=None, **kwargs)`<br>Ejemplo:<br>run.log_row("Y over X", x=1, y=0.4) | Al usar *log_row* se crea una métrica de varias columnas, tal y como se describe en los argumentos kwargs. Cada parámetro con nombre genera una columna con el valor especificado.  Se puede llamar una vez a *log_row* para registrar una tupla arbitraria, o varias veces en un bucle para generar una tabla completa.|
|Tabla|Función:<br>`run.log_table(name, value, description='')`<br><br>Ejemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registra un objeto de diccionario en la ejecución con el nombre especificado. |
|Imágenes|Función:<br>`run.log_image(name, path=None, plot=None)`<br><br>Ejemplo:<br>`run.log_image("ROC", plt)` | Registra una imagen en el registro de ejecución. Use log_image para registrar un archivo de imagen o un trazado matplotlib en la ejecución.  Estas imágenes serán visibles y comparables en el registro de ejecución.|
|Etiquetar una ejecución|Función:<br>`run.tag(key, value=None)`<br><br>Ejemplo:<br>run.tag("selected", "yes") | Etiqueta la ejecución con una clave de cadena y un valor de cadena opcional.|
|Cargar archivo o directorio|Función:<br>`run.upload_file(name, path_or_stream)`<br> <br> Ejemplo:<br>run.upload_file("best_model.pkl", "./model.pkl") | Carga un archivo en el registro de ejecución. Las ejecuciones capturan automáticamente el archivo en el directorio de salida especificado, cuyo valor predeterminado es "./outputs" para la mayoría de tipos de ejecución.  Use upload_file solo cuando sea necesario cargar archivos adicionales o no se especifique un directorio de salida. Se recomienda agregar `outputs` al nombre, para que se cargue en el directorio de salida. Puede enumerar todos los archivos que están asociados con esta ejecución de registro mediante la llamada `run.get_file_names()`|

> [!NOTE]
> Las métricas de valores escalares, listas, filas y tablas pueden tener el tipo: float, integer o string.

## <a name="start-logging-metrics"></a>Inicio de las métricas de registro

Si quiere realizar un seguimiento del experimento o supervisarlo, debe agregar código para iniciar el registro al enviar la ejecución. Las siguientes son formas de desencadenar el envío de ejecución:
* __Run.start_logging__: agrega funciones de registro al script de entrenamiento e inicia una sesión de registro interactiva en el experimento especificado. **start_logging** crea una ejecución interactiva para su uso en escenarios como los cuadernos. Todas las métricas que se registran durante la sesión se agregan al registro de ejecución en el experimento.
* __ScriptRunConfig__: agrega funciones de registro al script de entrenamiento y carga la carpeta del script completo con la ejecución.  **ScriptRunConfig** es una clase para configurar las ejecuciones del script. Con esta opción, puede agregar código de supervisión para recibir una notificación al finalizar o para obtener un widget visual para supervisar.

## <a name="set-up-the-workspace"></a>Configuración del área de trabajo
Antes de agregar el registro y enviar un experimento, debe configurar el área de trabajo.

1. Cargue el área de trabajo. Para más información acerca de cómo establecer la configuración de área de trabajo, siga los pasos de [crear un área de trabajo del servicio de Azure Machine Learning](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Opción 1: Usar start_logging

**start_logging** crea una ejecución interactiva para su uso en escenarios como los cuadernos. Todas las métricas que se registran durante la sesión se agregan al registro de ejecución en el experimento.

En el ejemplo siguiente, se entrena un modelo sklearn Ridge sencillo de forma local en un cuaderno de Jupyter local. Para más información sobre el envío de experimentos a diferentes entornos, consulte [Configuración de destinos de proceso para el entrenamiento de modelos con el servicio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Cree un script de entrenamiento en un cuaderno local de Jupyter. 

   ``` python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Agregue seguimiento del experimento mediante el SDK del servicio Azure Machine Learning y cargue un modelo guardado en el registro de ejecución del experimento. El código siguiente agrega etiquetas y registros y carga un archivo de modelo en la ejecución del experimento.

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

El script finaliza con ```run.complete()```, que marca la ejecución como completada.  Esta función se usa normalmente en escenarios de cuaderno interactivos.

## <a name="option-2-use-scriptrunconfig"></a>Opción 2: Usar ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) es una clase para configurar las configuraciones para el script se ejecuta. Con esta opción, puede agregar código de supervisión para recibir una notificación al finalizar o para obtener un widget visual para supervisar.

En este ejemplo se amplía el modelo sklearn Ridge básico anterior. Se realiza un barrido simple de parámetros sobre los valores alfa del modelo para capturar las métricas y los modelos entrenados en las ejecuciones del experimento. El ejemplo se ejecuta localmente en un entorno administrado por el usuario. 

1. Cree un script de entrenamiento `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. El script `train.py` hace referencia a `mylib.py`, que le permite obtener la lista de valores alfa que se usarán en el modelo de Ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configure un entorno local administrado por el usuario.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Envíe el script ```train.py``` para ejecutarse en el entorno administrado por el usuario. Esta carpeta del script completo se envía para el entrenamiento, incluido el archivo ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>Administrar una ejecución

El [iniciar, supervisar y cancelar las ejecuciones de entrenamiento](how-to-manage-runs.md) artículo destacan los flujos de trabajo específicos de Azure Machine Learning para administrar sus experimentos.

## <a name="view-run-details"></a>Visualización de los detalles de ejecución

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Supervisar la ejecución con widgets de cuadernos de Jupyter
Cuando se usa el método **ScriptRunConfig** para enviar ejecuciones, se puede ver el progreso de la ejecución con un widget de cuaderno de Jupyter. Al igual que el envío de ejecuciones, el widget es asincrónico y proporciona actualizaciones directas cada 10 o 15 segundos hasta que se completa el trabajo.

1. Vea el widget de Jupyter mientras espera a que finalice la ejecución.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Captura de pantalla del widget de cuaderno de Jupyter](./media/how-to-track-experiments/widgets.PNG)

2. **[Para ejecuciones de aprendizaje automático automatizado]**  Para acceder a los gráficos de una ejecución anterior. Reemplace `<<experiment_name>>` con el nombre del experimento adecuado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de cuaderno de Jupyter Notebooks para aprendizaje automático automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para ver más detalles de una canalización, haga clic en la canalización que quiere explorar en la tabla, y los gráficos se representarán en una ventana emergente desde Azure Portal.

### <a name="get-log-results-upon-completion"></a>Obtener los resultados de registros tras la finalización

El entrenamiento y la supervisión de modelos tienen lugar en segundo plano, de modo que pueden ejecutar otras tareas mientras espera. También puede esperar a que el modelo haya completado el entrenamiento antes de ejecutar más código. Cuando se usa **ScriptRunConfig**, se puede usar ```run.wait_for_completion(show_output = True)``` para mostrar cuándo finaliza el entrenamiento del modelo. La marca ```show_output``` le ofrece una salida detallada. 
  
### <a name="query-run-metrics"></a>Métricas de ejecución de consulta

Puede ver las métricas de un modelo entrenado con ```run.get_metrics()```. Ahora puede obtener todas las métricas que se registraron en el ejemplo anterior para determinar el mejor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Visualización del experimento en Azure Portal

Cuando un experimento ha terminado de ejecutarse, puede ir al registro de ejecución del experimento grabado. Puede acceder al historial de dos maneras:

* Obtener la dirección URL a la ejecución directamente ```print(run.get_portal_url())```
* Ver los detalles de la ejecución mediante el envío del nombre de la ejecución (en este caso, ```run```). Aquí se le indica el nombre del experimento, el identificador, el tipo, el estado, la página de detalles, un vínculo a Azure Portal y un vínculo a la documentación.

El vínculo para la ejecución le lleva directamente a la página de detalles de ejecución en Azure Portal. Aquí puede ver las propiedades, las métricas sometidas a seguimiento, las imágenes y los gráficos que se registran en el experimento. En este caso, se registra MSE y los valores alfa.

  ![Detalles de ejecución en Azure Portal](./media/how-to-track-experiments/run-details-page-web.PNG)

También puede ver las salidas o los registros de la ejecución, o descargar la instantánea del experimento que ha enviado para que pueda compartir la carpeta del experimento con otros usuarios.

### <a name="viewing-charts-in-run-details"></a>Visualización de gráficos en los detalles de ejecución

Hay varias maneras de usar las API de registro para registrar diferentes tipos de métricas durante una ejecución y verlas como gráficos en Azure Portal. 

|Valor registrado|Ejemplo de código| Ver en el portal|
|----|----|----|
|Registrar una matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Gráfico de líneas de una sola variable|
|Registrar un único valor numérico con el mismo nombre de métrica usado repetidamente (como desde dentro de un bucle)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de líneas de una sola variable|
|Registrar una fila con dos columnas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de líneas de dos variables|
|Registrar tabla con dos columnas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de líneas de dos variables|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Descripción de los gráficos de ML automatizado

Después de enviar un trabajo de ML automatizado en un cuaderno, puede encontrar un historial de estas ejecuciones en el área de trabajo del servicio de aprendizaje automático. 

Más información sobre:
+ [Gráficos y curvas para los modelos de clasificación](#classification)
+ [Diagramas y gráficos para los modelos de regresión](#regression)
+ [Modelo de capacidad de explicar](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Visualización de los gráficos de ejecución

1. Vaya a su área de trabajo. 

1. Seleccione **Experimentos** en el panel izquierdo del área de trabajo.

   ![Captura de pantalla del menú de experimentos](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Seleccione el experimento que le interesa.

   ![Lista de experimentos](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. En la tabla, seleccione Run Number (Número de ejecución).

   ![Ejecución de experimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. En la tabla, seleccione el número de iteración para el modelo que quiere explorar más a fondo.

   ![Modelo de experimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>clasificación

Para cada modelo de clasificación que se crea usando las funcionalidades de aprendizaje automático automatizado de Azure Machine Learning, puede ver los gráficos siguientes: 
+ [Matriz de confusión](#confusion-matrix)
+ [Gráfico de precisión de la recuperación](#precision-recall-chart)
+ [Receptor operativo características (o ROC)](#roc)
+ [Curva de elevación](#lift-curve)
+ [Curva de beneficios](#gains-curve)
+ [Trazado de calibración](#calibration-plot)

#### <a name="confusion-matrix"></a>Matriz de confusión

Una matriz de confusión se usa para describir el rendimiento de un modelo de clasificación. Cada fila muestra las instancias de la clase verdadera, y cada columna representa las instancias de la clase prevista. La matriz de confusión muestra las etiquetas clasificadas correctamente y las etiquetas clasificadas incorrectamente para un modelo determinado.

Por problemas de clasificación, Azure Machine Learning proporciona automáticamente una matriz de confusión para cada modelo que se crea. Para cada matriz de confusión, ML automatizado mostrará las etiquetas clasificadas correctamente en verde, y las clasificadas incorrectamente en rojo. El tamaño del círculo representa el número de muestras en esa ubicación. Además, el recuento de frecuencia de cada etiqueta predicha y cada etiqueta verdadera se proporciona en los gráficos de barras adyacentes. 

Ejemplo 1: Un modelo de clasificación con baja precisión ![un modelo de clasificación con baja precisión](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Ejemplo 2: Un modelo de clasificación con una gran precisión (ideal) ![un modelo de clasificación con alta precisión](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Gráfico de precisión-recuperación

Con este gráfico, puede comparar las curvas de precisión-recuperación para cada modelo para determinar qué modelo tiene una relación aceptable entre la precisión y la recuperación para su problema empresarial en particular. En este gráfico se muestran la precisión-recuperación promedio macro, la precisión-recuperación promedio micro y la precisión-recuperación asociadas con todas las clases de un modelo.

El término "precisión" representa la capacidad de un clasificador de etiquetar todas las instancias correctamente. "Recuperación" representa la capacidad de un clasificador de buscar todas las instancias de una etiqueta determinada. La curva de precisión-recuperación muestra la relación entre estos dos conceptos. Idealmente, el modelo tendría una precisión del 100 % y una exactitud del 100 %.

Ejemplo 1: Un modelo de clasificación con precisión baja y la recuperación bajo ![con precisión baja y la recuperación bajo un modelo de clasificación](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Ejemplo 2: Un modelo de clasificación con recuperación precisión y ~ 100% ~ 100% (ideal) ![una elevada precisión de modelo de clasificación y la recuperación](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

La característica operativa del receptor (o ROC) es un trazado de las etiquetas clasificadas correctamente frente a las etiquetas clasificadas incorrectamente para un modelo determinado. La curva ROC puede ser menos informativa al entrenar modelos en conjuntos de datos con gran sesgo, ya que no mostrará las etiquetas falsas positivas.

Ejemplo 1: Un modelo de clasificación con etiquetas verdaderas bajo y altas etiquetas falsas ![modelo de clasificación con etiquetas verdaderas bajo y altas etiquetas falsas](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Ejemplo 2: Un modelo de clasificación con etiquetas verdaderas alta y baja etiquetas falsas ![un modelo de clasificación con etiquetas verdaderas alta y baja etiquetas falsas](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Curva de elevación

Puede comparar la elevación del modelo creado automáticamente con Azure Machine Learning con la línea base para ver el aumento del valor de ese modelo concreto.

Los gráficos de elevación se usan para evaluar el rendimiento de un modelo de clasificación. Muestra cuánto mejor puede esperar que le vaya con un modelo en comparación con hacerlo sin un modelo. 

Ejemplo 1: Modelo realiza peor que un modelo de selección aleatoria ![un modelo de clasificación que modelan peor que una selección aleatoria](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Ejemplo 2: Modelo funciona mejor que un modelo de selección aleatoria ![un modelo de clasificación cuyo rendimiento es mejor](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Curva de beneficios

Un gráfico de beneficios evalúa el rendimiento de un modelo de clasificación por cada parte de los datos. Muestra, para cada percentil del conjunto de datos, cuánto mejor puede esperar rendir en comparación con un modelo de selección aleatoria.

Use el gráfico de beneficios acumulados para ayudarle a elegir la fecha límite de clasificación mediante un porcentaje que corresponda a un beneficio deseado del modelo. Esta información proporciona otra manera de examinar los resultados en el gráfico de elevación que lo acompaña.

Ejemplo 1: Un modelo de clasificación con mejora mínima ![un modelo de clasificación con mejora mínima](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Ejemplo 2: Un modelo de clasificación con ganancia significativa ![un modelo de clasificación con ganancia significativa](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Trazado de calibración

Para todos los problemas de clasificación, puede revisar la línea de calibración de promedio micro, promedio macro y cada clase en un modelo predictivo determinado. 

Un trazado de calibración se usa para mostrar la confianza de un modelo predictivo. Esto se consigue al mostrar la relación entre la probabilidad predicha y la probabilidad real, donde "probabilidad" representa la probabilidad de que una instancia determinada pertenezca a alguna etiqueta. Un modelo bien calibrado se alinea con y=x, donde resulta razonablemente confiable en sus predicciones. Un modelo con confianza excesiva se alinea con y=0, donde la probabilidad predicha está presente, pero no hay ninguna probabilidad real.

Ejemplo 1: Un modelo más bien calibrado ![ modelo más bien calibrada](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Ejemplo 2: Un modelo de exceso confidencial ![un modelo de exceso confidencial](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regresión
Para cada modelo de regresión que se crea usando las funcionalidades de aprendizaje automático automatizado de Azure Machine Learning, puede ver los gráficos siguientes: 
+ [Predicho frente a True](#pvt)
+ [Histograma de valores residuales](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Predicho frente a True

Predicho frente a True (Predicho frente verdadero), muestra la relación entre un valor predicho y su valor verdadero correlacionado para un problema de regresión. Este gráfico se puede usar para medir el rendimiento de un modelo, ya que cuanto más se acerquen los valores predichos a la línea y=x, mejor será la precisión de un modelo predictivo.

Después de cada ejecución, puede ver un gráfico de predicción frente a verdadero para cada modelo de regresión. Para proteger la privacidad de los datos, los valores se agrupan juntos y el tamaño de cada ubicación se muestra como un gráfico de barras en la parte inferior del área del gráfico. Puede comparar el modelo predictivo, donde el área de sombreado más claro muestra los márgenes de error, con el valor ideal donde se debería ubicar el modelo.

Ejemplo 1: Un modelo de regresión con precisión baja en las predicciones ![un modelo de regresión con precisión baja en las predicciones](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Ejemplo 2: Un modelo de regresión con precisión en sus predicciones ![un modelo de regresión con precisión en sus predicciones](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histograma de valores residuales

Un valor residual representa la y observada – la y predicha. Para mostrar un margen de error con poco sesgo, el histograma de valores residuales debe tener la forma de una curva de campana, centrada en 0. 

Ejemplo 1: Un modelo de regresión con el sesgo de los errores ![modelo de regresión de SA con el sesgo de los errores](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Ejemplo 2: Un modelo de regresión con una distribución más uniforme de los errores ![un modelo de regresión con una distribución más uniforme de errores](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Capacidad de explicación del modelo e importancia de características

La importancia de características proporciona una puntuación que indica cuán valiosa ha sido cada característica en la construcción de un modelo. Puede revisar la puntuación de la importancia de características para el modelo general, así como por cada clase en un modelo predictivo. Por característica, puede ver cómo se compara la importancia frente a cada clase y en general.

![Capacidad de explicación de características](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Cuadernos de ejemplo
Los cuadernos siguientes muestran los conceptos de este artículo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-Use-azureml/Training/Train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Pasos siguientes

Pruebe estos pasos para saber cómo usar el SDK de Azure Machine Learning para Python:

* Vea un ejemplo de cómo registrar el mejor modelo e implementarlo en el tutorial, [Entrenar un modelo de clasificación de imágenes con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Aprenda a [entrenar modelos de PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
