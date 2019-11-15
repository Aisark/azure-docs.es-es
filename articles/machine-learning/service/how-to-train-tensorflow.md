---
title: Entrenamiento de una red neuronal de aprendizaje profundo con TensorFlow
titleSuffix: Azure Machine Learning
description: Aprenda a ejecutar los scripts de entrenamiento de TensorFlow a escala mediante Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: b3d5a61b93175559bce92a17e27602a4f79d88ad
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603972"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Creación de un modelo de aprendizaje profundo de TensorFlow a escala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

En este artículo se muestra cómo ejecutar los scripts de entrenamiento de [TensorFlow](https://www.tensorflow.org/overview) a escala mediante la clase [estimator de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) de Azure Machine Learning. En este ejemplo se entrena y registra un modelo de TensorFlow para clasificar dígitos manuscritos mediante una red neuronal profunda (DNN).

Tanto si se va a desarrollar un modelo de TensorFlow desde el principio como si va a incorporar un [modelo existente](how-to-deploy-existing-model.md) a la nube, puede usar Azure Machine Learning para escalar horizontalmente trabajos de entrenamiento de código abierto para crear, implementar, versionar y supervisar modelos de nivel de producción.

Más información sobre las diferencias entre [aprendizaje profundo y aprendizaje automático](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Requisitos previos

Ejecute este código en cualquiera de estos entornos:

 - Máquina virtual de Notebook de Azure Machine Learning: no se necesitan descargas ni instalación

     - Complete el [Tutorial: Configuración del entorno y el área de trabajo](tutorial-1st-experiment-sdk-setup.md) para crear un servidor de cuadernos dedicado en el que se habrán cargado previamente el SDK y el repositorio de ejemplos.
    - En la carpeta de aprendizaje profundo de ejemplos en el servidor de cuadernos, vaya a este directorio: carpeta **how-to-use-azureml > ml-frameworks > tensorflow > deployment > train-hyperparameter-tune-deploy-with-tensorflow**,en donde encontrará un cuaderno completado y expandido. 
 
 - Su propio servidor de Jupyter Notebook

    - [Instalación del SDK de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creación de un archivo de configuración del área de trabajo](how-to-configure-environment.md#workspace).
    - [Descarga de los archivos de script de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` y `utils.py`
     
    También puede encontrar una [versión de Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) completada de esta guía en la página de ejemplos de GitHub. El cuaderno incluye secciones expandidas que abarcan el ajuste de hiperparámetros inteligente, la implementación de modelos y los widgets del cuaderno.

## <a name="set-up-the-experiment"></a>Configuración del experimento

En esta sección, para configurar el experimento de entrenamiento, se cargan los paquetes de Python necesarios, se inicializa un área de trabajo, se crea un experimento y se cargan los datos y scripts de entrenamiento.

### <a name="import-packages"></a>Importación de paquetes

En primer lugar, importe las bibliotecas de Python necesarias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicialización de un área de trabajo

El [área de trabajo de Azure Machine Learning](concept-workspace.md) es el recurso de nivel superior para el servicio. Proporciona un lugar centralizado para trabajar con todos los artefactos que cree. En el SDK de Python, puede acceder a los artefactos del área de trabajo mediante la creación de un objeto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Cree un objeto de área de trabajo a partir del archivo `config.json` creado en la [sección de requisitos previos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Creación de un experimento de aprendizaje profundo

Cree un experimento y una carpeta para almacenar los scripts de entrenamiento. En este ejemplo, cree un experimento llamado "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Crear un conjunto de datos de archivo

Un objeto `FileDataset` hace referencia a uno o varios archivos del almacén de archivos del área de trabajo o direcciones URL públicas. Los archivos pueden estar en cualquier formato y la clase le permite descargar o montar los archivos en el proceso. Si crea un objeto `FileDataset`, se crea una referencia a la ubicación de los orígenes de datos. Si aplicó alguna transformación al conjunto de datos, también se almacenará en el conjunto de datos. Los datos se mantienen en la ubicación existente, por lo que no se genera ningún costo de almacenamiento adicional. Consulte la [guía de procedimientos](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) sobre el paquete `Dataset` para obtener más información.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Use el método `register()` para registrar el conjunto de datos en el área de trabajo de modo que pueda compartirlo con otros usuarios, reutilizarlo en varios experimentos y hacer referencia a él por nombre en el script de entrenamiento.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Creación de un destino de proceso

Cree un destino de proceso en el que ejecutar el trabajo de TensorFlow. En este ejemplo, cree un clúster de proceso de Azure Machine Learning habilitado para GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para más información sobre los destinos de proceso, consulte el artículo [¿Qué es un destino de proceso?](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator"></a>Creación de un estimador de TensorFlow

El [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) proporciona una manera sencilla de iniciar un trabajo de entrenamiento de TensorFlow en un destino de proceso.

El estimador de TensorFlow se implementa a través de la clase genérica [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), que se puede usar para admitir cualquier marco de trabajo. Para obtener más información sobre cómo entrenar modelos con el estimador genérico, consulte [Entrenamiento de modelos con Azure Machine Learning mediante un objeto Estimator](how-to-train-ml-models.md).

Si el script de entrenamiento necesita paquetes adicionales pip o conda para ejecutarse, puede hacer que los paquetes se instalen en la imagen de Docker resultante. Para ello, pase sus nombres mediante los argumentos `pip_packages` y `conda_packages`.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

> [!TIP]
> Se ha agregado compatibilidad con **Tensorflow 2.0** a la clase del calculador de Tensorflow. Para obtener más información, consulte la [entrada de blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

## <a name="submit-a-run"></a>Envío de una ejecución

El [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) proporciona la interfaz para el historial de ejecución mientras se ejecuta el trabajo y cuando se ha completado.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Durante la ejecución de Run, pasa por las fases siguientes:

- **Preparación**: se crea una imagen de Docker según el estimador de TensorFlow. La imagen se carga en el registro de contenedor del área de trabajo y se almacena en memoria caché para ejecuciones posteriores. Los registros también se transmiten al historial de ejecución y se pueden consultar para supervisar el progreso.

- **Escalado**: el clúster intenta escalar verticalmente si el clúster de Batch AI requiere más nodos para realizar la ejecución de los que se encuentran disponibles actualmente.

- **Running**: todos los scripts de la carpeta de scripts se cargan en el destino de proceso, se montan o se copian los almacenes de datos y se ejecuta entry_script. Las salidas de stdout y la carpeta ./logs se transmiten al historial de ejecución y se pueden usar para supervisar la ejecución.

- **Posprocesamiento**: la carpeta ./outputs de la ejecución se copia en el historial de ejecución.

## <a name="register-or-download-a-model"></a>Registro o descarga de un modelo

Una vez que haya entrenado el modelo, puede registrarlo en el área de trabajo. El registro del modelo permite almacenar los modelos y crear versiones de ellos en el área de trabajo para simplificar la [administración e implementación de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

También puede descargar una copia local del modelo mediante el uso del objeto Run. En el script de entrenamiento, `mnist-tf.py`, un objeto de protector de TensorFlow guarda el modelo en una carpeta local (local para el destino de proceso). Puede usar el objeto Run para descargar una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Entrenamiento distribuido

El estimador [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) también admite el entrenamiento distribuido en clústeres de CPU y GPU. Puede ejecutar fácilmente los trabajos distribuidos de TensorFlow y Azure Machine Learning se ocupará de administrar la orquestación automáticamente.

Azure Machine Learning admite dos métodos de aprendizaje distribuidos en TensorFlow:

- Entrenamiento distribuido [basado en MPI](https://www.open-mpi.org/) mediante el marco [Horovod](https://github.com/uber/horovod)
- [TensorFlow distribuido](https://www.tensorflow.org/deploy/distributed) nativo mediante el método de servidor de parámetros

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) es un marco de código abierto para el entrenamiento distribuido desarrollado por Uber. Ofrece una ruta de acceso fácil a los trabajos de GPU distribuidos de TensorFlow.

Para usar Horovod, especifique un objeto [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para el parámetro `distributed_training` en el constructor de TensorFlow. Este parámetro garantiza que se instala la biblioteca de Horovod para su uso en el script de entrenamiento.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Servidor de parámetro

También puede ejecutar [TensorFlow distribuido nativo](https://www.tensorflow.org/deploy/distributed), que utiliza el modelo de servidor del parámetro. En este método, entrena a través de un clúster de servidores de parámetro y los trabajadores. Los trabajadores calculan los degradados durante el entrenamiento, mientras que los servidores de parámetro agregan los degradados.

Para usar el método del servidor de parámetros, especifique un objeto [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) para el parámetro `distributed_training` en el constructor de TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definición de las especificaciones del clúster en "TF_CONFIG"

También necesitará las direcciones de red y los puertos del clúster para [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), por lo que Azure Machine Learning establece la variable de entorno `TF_CONFIG` por usted.

La variable de entorno `TF_CONFIG` es una cadena JSON. Este es un ejemplo de la variable para un servidor de parámetro:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Para la API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) de alto nivel de TensorFlow, TensorFlow analizará la variable `TF_CONFIG` y creará la especificación del clúster automáticamente.

Para la API básica de nivel inferior de TensorFlow para el entrenamiento, analice la variable `TF_CONFIG` y cree `tf.train.ClusterSpec` en el código de entrenamiento.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha entrenado y registrado un modelo de TensorFlow. Para obtener información sobre cómo implementar un modelo en un clúster habilitado para GPU, continúe con nuestro artículo de implementación del modelo GPU.

> [!div class="nextstepaction"]
> [Cómo y dónde implementar los modelos](how-to-deploy-and-where.md)
* [Seguir métricas de ejecución durante el entrenamiento](how-to-track-experiments.md)
* [Ajustar los hiperparámetros](how-to-tune-hyperparameters.md)
* [Implementar un modelo entrenado](how-to-deploy-and-where.md)
* [Arquitectura de referencia para el entrenamiento del aprendizaje profundo distribuido en Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
