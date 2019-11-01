---
title: Registro de métricas y experimentos de ML
titleSuffix: Azure Machine Learning
description: Supervise sus experimentos de Azure ML y supervise las métricas de ejecución para mejorar el proceso de creación de modelos. Agregue el registro al script de entrenamiento y vea los resultados registrados de una ejecución.  Use run.log, Run.start_logging o ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: c72de809dc5818cced95be2cbd6b47308bad4f22
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045206"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Supervisión de métricas y ejecuciones de experimentos de Azure ML

Realice el seguimiento de sus experimentos y supervise las métricas de ejecución para mejorar el proceso de creación del modelo. En este artículo, aprenderá a agregar código de registro a un script de entrenamiento, enviar una ejecución de experimento, supervisar dicha ejecución y comprobar los resultados en Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning también puede registrar información de otros orígenes durante el entrenamiento, como las ejecuciones de Machine Learning automatizado o el contenedor de Docker que ejecuta el trabajo de entrenamiento. Estos registros no están documentados. Si encuentra problemas y se pone en contacto con el Soporte técnico de Microsoft, es posible que puedan usar estos registros durante la resolución de problemas.

## <a name="available-metrics-to-track"></a>Métricas disponibles de las que se realiza seguimiento

Las siguientes métricas se pueden agregar a una ejecución durante el entrenamiento de un experimento. Para ver una lista más detallada de aquello de lo que puede realizar el seguimiento en una ejecución, consulte la [documentación de referencia de la clase Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|type| Función de Python | Notas|
|----|:----|:----|
|Valores escalares |Función:<br>`run.log(name, value, description='')`<br><br>Ejemplo:<br>run.log("accuracy", 0.95) |Registra un valor numérico o de cadena en la ejecución con el nombre especificado. Al registrar una métrica en una ejecución, esa métrica se almacena en el registro de ejecución en el experimento.  Puede registrar la misma métrica varias veces dentro de una ejecución y el resultado se considerará un vector de esa métrica.|
|Listas|Función:<br>`run.log_list(name, value, description='')`<br><br>Ejemplo:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Registra una lista de valores en la ejecución con el nombre especificado.|
|Fila|Función:<br>`run.log_row(name, description=None, **kwargs)`<br>Ejemplo:<br>run.log_row("Y over X", x=1, y=0.4) | Al usar *log_row* se crea una métrica de varias columnas, tal y como se describe en los argumentos kwargs. Cada parámetro con nombre genera una columna con el valor especificado.  Se puede llamar una vez a *log_row* para registrar una tupla arbitraria, o varias veces en un bucle para generar una tabla completa.|
|Tabla|Función:<br>`run.log_table(name, value, description='')`<br><br>Ejemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registra un objeto de diccionario en la ejecución con el nombre especificado. |
|Imágenes|Función:<br>`run.log_image(name, path=None, plot=None)`<br><br>Ejemplo:<br>`run.log_image("ROC", plt)` | Registra una imagen en el registro de ejecución. Use log_image para registrar un archivo de imagen o un trazado matplotlib en la ejecución.  Estas imágenes serán visibles y comparables en el registro de ejecución.|
|Etiquetar una ejecución|Función:<br>`run.tag(key, value=None)`<br><br>Ejemplo:<br>run.tag("selected", "yes") | Etiqueta la ejecución con una clave de cadena y un valor de cadena opcional.|
|Cargar archivo o directorio|Función:<br>`run.upload_file(name, path_or_stream)`<br> <br> Ejemplo:<br>run.upload_file("best_model.pkl", "./model.pkl") | Carga un archivo en el registro de ejecución. Las ejecuciones capturan automáticamente el archivo en el directorio de salida especificado, cuyo valor predeterminado es "./outputs" para la mayoría de tipos de ejecución.  Use upload_file solo cuando sea necesario cargar archivos adicionales o no se especifique un directorio de salida. Se recomienda agregar `outputs` al nombre, para que se cargue en el directorio de salida. Puede enumerar todos los archivos que están asociados con este registro de ejecución por el parámetro `run.get_file_names()` invocado.|

> [!NOTE]
> Las métricas de valores escalares, listas, filas y tablas pueden tener el tipo: float, integer o string.

## <a name="choose-a-logging-option"></a>Elección de una opción de registro

Si quiere realizar un seguimiento del experimento o supervisarlo, debe agregar código para iniciar el registro al enviar la ejecución. Las siguientes son formas de desencadenar el envío de ejecución:
* __Run.start_logging__: agrega funciones de registro al script de entrenamiento e inicia una sesión de registro interactiva en el experimento especificado. **start_logging** crea una ejecución interactiva para su uso en escenarios como los cuadernos. Todas las métricas que se registran durante la sesión se agregan al registro de ejecución en el experimento.
* __ScriptRunConfig__: agrega funciones de registro al script de entrenamiento y carga la carpeta del script completo con la ejecución.  **ScriptRunConfig** es una clase para configurar las ejecuciones del script. Con esta opción, puede agregar código de supervisión para recibir una notificación al finalizar o para obtener un widget visual para supervisar.

## <a name="set-up-the-workspace"></a>Configuración del área de trabajo
Antes de agregar el registro y enviar un experimento, debe configurar el área de trabajo.

1. Cargue el área de trabajo. Para obtener más información sobre la definición de la configuración del área de trabajo, consulte el [archivo de configuración del área de trabajo](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Opción 1: Usar start_logging

**start_logging** crea una ejecución interactiva para su uso en escenarios como los cuadernos. Todas las métricas que se registran durante la sesión se agregan al registro de ejecución en el experimento.

En el ejemplo siguiente, se entrena un modelo sklearn Ridge sencillo de forma local en un cuaderno de Jupyter local. Para más información sobre el envío de experimentos a diferentes entornos, consulte [Configuración de destinos de proceso para el entrenamiento de modelos con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Cree un script de entrenamiento en un cuaderno local de Jupyter. 

   ```python
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

2. Agregue seguimiento del experimento mediante el SDK de Azure Machine Learning y cargue un modelo guardado en el registro de ejecución del experimento. El código siguiente agrega etiquetas y registros y carga un archivo de modelo en la ejecución del experimento.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    El script finaliza con ```run.complete()```, que marca la ejecución como completada.  Esta función se usa normalmente en escenarios de cuaderno interactivos.

## <a name="option-2-use-scriptrunconfig"></a>Opción 2: Usar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) es una clase para configurar las ejecuciones del script. Con esta opción, puede agregar código de supervisión para recibir una notificación al finalizar o para obtener un widget visual para supervisar.

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
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Envíe el script ```train.py``` para ejecutarse en el entorno administrado por el usuario. Esta carpeta del script completo se envía para el entrenamiento, incluido el archivo ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Administración de ejecuciones

En el artículo [Inicio, supervisión y cancelación de las ejecuciones de entrenamiento](how-to-manage-runs.md) se resaltan los flujos de trabajo de Azure Machine Learning específicos sobre cómo administrar sus experimentos.

## <a name="view-run-details"></a>Visualización de los detalles de ejecución

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Supervisar la ejecución con widgets de cuadernos de Jupyter
Cuando se usa el método **ScriptRunConfig** para enviar ejecuciones, se puede ver el progreso de la ejecución con un [widget de Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Al igual que el envío de ejecución, el widget es asincrónico y proporciona las actualizaciones directas cada 10 a 15 segundos hasta que se completa el trabajo.

1. Vea el widget de Jupyter mientras espera a que finalice la ejecución.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Captura de pantalla del widget de cuaderno de Jupyter](./media/how-to-track-experiments/run-details-widget.png)

También puede obtener un vínculo a la misma pantalla en el área de trabajo.

```python
print(run.get_portal_url())
```

2. **[Para ejecuciones de aprendizaje automático automatizado]**  Para acceder a los gráficos de una ejecución anterior. Reemplace `<<experiment_name>>` por el nombre del experimento adecuado:

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
## <a name="view-the-experiment-in-the-azure-portal-or-your-workspace-landing-page-previewhttpsmlazurecom"></a>Visualización del experimento en Azure Portal o en la [página de aterrizaje del área de trabajo (versión preliminar)](https://ml.azure.com) propia

Cuando un experimento ha terminado de ejecutarse, puede ir al registro de ejecución del experimento grabado. Puede acceder al historial de dos maneras:

* Obtener directamente la dirección URL a la ejecución ```print(run.get_portal_url())```
* Ver los detalles de la ejecución mediante el envío del nombre de la ejecución (en este caso, ```run```). Aquí se le indica el nombre del experimento, el identificador, el tipo, el estado, la página de detalles, un vínculo a Azure Portal y un vínculo a la documentación.

El vínculo para la ejecución le lleva directamente a la página de detalles de ejecución en Azure Portal. Aquí puede ver las propiedades, las métricas sometidas a seguimiento, las imágenes y los gráficos que se registran en el experimento. En este caso, se registra MSE y los valores alfa.

  ![Detalles de ejecución en Azure Portal](./media/how-to-track-experiments/run-details-page.png)

También puede ver las salidas o los registros de la ejecución, o descargar la instantánea del experimento que ha enviado para que pueda compartir la carpeta del experimento con otros usuarios.

### <a name="viewing-charts-in-run-details"></a>Visualización de gráficos en los detalles de ejecución

Hay varias maneras de usar las API de registro para registrar diferentes tipos de métricas durante una ejecución y verlas como gráficos en Azure Portal. 

|Valor registrado|Ejemplo de código| Ver en el portal|
|----|----|----|
|Registrar una matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Gráfico de líneas de una sola variable|
|Registrar un único valor numérico con el mismo nombre de métrica usado repetidamente (como desde dentro de un bucle)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de líneas de una sola variable|
|Registrar una fila con dos columnas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de líneas de dos variables|
|Registrar tabla con dos columnas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de líneas de dos variables|


## <a name="example-notebooks"></a>Cuadernos de ejemplo
Los cuadernos siguientes muestran los conceptos de este artículo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Pasos siguientes

Pruebe estos pasos para saber cómo usar el SDK de Azure Machine Learning para Python:

* Vea un ejemplo de cómo registrar el mejor modelo e implementarlo en el tutorial, [Entrenar un modelo de clasificación de imágenes con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Aprenda a [entrenar modelos de PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
