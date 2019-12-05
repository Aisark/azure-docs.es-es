---
title: Cómo y dónde implementar los modelos
titleSuffix: Azure Machine Learning
description: Aprenda cómo y dónde implementar sus modelos de Azure Machine Learning, incluidos Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge y matrices de puertas programables por campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 63d2aa5c9e4ec751d9b95ba0d884e6dc17e207bb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276787"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implementación de modelos con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar el modelo de Machine Learning como un servicio web en la nube de Azure o en dispositivos de Azure IoT Edge.

El flujo de trabajo es siempre parecido independientemente de [donde implemente](#target) el modelo:

1. Registre el modelo.
1. Prepárese para la implementación. (Especifique los recursos, el uso y el destino de proceso).
1. Implemente el modelo en el destino de proceso.
1. Pruebe el modelo implementado, también denominado servicio web.

Para más información sobre los conceptos implicados en el flujo de trabajo de implementación, consulte [Administración, implementación y supervisión de modelos con Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Requisitos previos

- Un área de trabajo de Azure Machine Learning. Para más información, consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

- Un modelo. Si no tiene un modelo entrenado, puede usar el modelo y los archivos de dependencia que se proporcionan en [este tutorial](https://aka.ms/azml-deploy-cloud).

- La [extensión de la CLI de Azure para Machine Learning Service](reference-azure-machine-learning-cli.md), el [SDK de Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o la [extensión de Visual Studio Code para Azure Machine Learning](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Conexión con su área de trabajo

En el código siguiente se muestra cómo conectarse a un área de trabajo de Azure Machine Learning con la información almacenada en caché del entorno de desarrollo local:

+ **Uso del SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para más información sobre el uso del SDK para conectarse a un área de trabajo, consulte la documentación del [SDK de Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

+ **Uso de la CLI**

   Al usar la CLI, use el parámetro `-w` o `--workspace-name` para especificar el área de trabajo para el comando.

+ **Uso de Visual Studio Code**

   Cuando use VS Code, seleccione el área de trabajo mediante una interfaz gráfica. Para más información, consulte [Implementación y administración de modelos](how-to-vscode-tools.md#deploy-and-manage-models) en la documentación de la extensión de VS Code.

## <a id="registermodel"></a> Registro del modelo

Un modelo registrado es un contenedor lógico para uno o varios archivos que componen el modelo. Por ejemplo, si tiene un modelo que se almacena en varios archivos, puede registrarlos como un único modelo en el área de trabajo. Después de registrar los archivos, puede descargar o implementar el modelo registrado y recibir todos los archivos que registró.

> [!TIP]
> Al registrar un modelo, se proporciona una ruta de acceso de una ubicación en la nube (desde una ejecución de entrenamiento) o de un directorio local. Esta ruta de acceso es solo para localizar los archivos que se van a cargar como parte del proceso de registro. No es necesario que coincida con la ruta de acceso que se usa en el script de entrada. Para obtener más información, consulte [Locate model files in your entry script](#locate-model-files-in-your-entry-script) (Localización de archivos de modelo en el script de entrada).

Los modelos de Machine Learning se registran en el área de trabajo de Azure Machine Learning. El modelo puede proceder de Azure Machine Learning o de otro lugar. En los ejemplos siguientes se muestra cómo registrar un modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registro de un modelo a partir de una ejecución de experimento

En los fragmentos de código de esta sección se muestra cómo registrar un modelo a partir de una ejecución de entrenamiento:

> [!IMPORTANT]
> Para usar estos fragmentos de código, necesita haber realizado previamente una ejecución de entrenamiento y tener acceso al objeto `Run` (ejemplo del SDK) o al valor de identificador de ejecución (ejemplo de la CLI). Para más información sobre los modelos de entrenamiento, consulte [Configuración y uso de destinos de proceso para el entrenamiento del modelo](how-to-set-up-training-targets.md).

+ **Uso del SDK**

  Al usar el SDK para entrenar un modelo, puede recibir un objeto [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) o [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master), en función de cómo haya entrenado el modelo. Cada objeto se puede usar para registrar un modelo creado por una ejecución del experimento.

  + Registre un modelo desde un objeto `azureml.core.Run`:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    El parámetro `model_path` hace referencia a la ubicación del modelo en la nube. En este ejemplo, se usa la ruta de acceso de un único archivo. Para incluir varios archivos en el registro del modelo, establezca `model_path` en la ruta de acceso de una carpeta que contiene los archivos. Para obtener más información, consulte la documentación [Run.register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-).

  + Registre un modelo desde un objeto `azureml.train.automl.run.AutoMLRun`:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    En este ejemplo, los parámetros `metric` y `iteration`, no se especifican, por lo que se registrará la iteración con la mejor métrica principal. Se utiliza el valor `model_id` devuelto de la ejecución en lugar de un nombre de modelo.

    Para más información, consulte la documentación de [AutoMLRun.register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-).

+ **Uso de la CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  El parámetro `--asset-path` hace referencia a la ubicación del modelo en la nube. En este ejemplo, se usa la ruta de acceso de un único archivo. Para incluir varios archivos en el registro del modelo, establezca `--asset-path` en la ruta de acceso de una carpeta que contiene los archivos.

+ **Uso de Visual Studio Code**

  Registre los modelos usando todas las carpetas o archivos de modelo mediante la extensión [VS Code](how-to-vscode-tools.md#deploy-and-manage-models).

### <a name="register-a-model-from-a-local-file"></a>Registrar un modelo desde un archivo local

Puede registrar un modelo proporcionando la ruta de acceso local de dicho modelo. Puede proporcionar la ruta de acceso de una carpeta o un único archivo. Puede usar este método para registrar los modelos entrenados con Azure Machine Learning y, luego, descargados. También puede usar este método para registrar modelos entrenados fuera de Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Uso del SDK y ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir varios archivos en el registro del modelo, establezca `model_path` en la ruta de acceso de una carpeta que contiene los archivos.

+ **Uso de la CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir varios archivos en el registro del modelo, establezca `-p` en la ruta de acceso de una carpeta que contiene los archivos.

**Tiempo estimado**: Aproximadamente 10 segundos.

Para más información, consulte la documentación de la [clase Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para más información sobre cómo trabajar con modelos entrenados al margen de Azure Machine Learning, consulte el artículo sobre cómo [implementar un modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Elección de un destino de proceso

Puede usar los siguientes destinos o recursos de proceso para hospedar la implementación del servicio web:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparación de la actualización

Para implementar el modelo, necesita los siguientes elementos:

* **Un script de entrada**. Este script acepta solicitudes, puntúa las solicitudes mediante el modelo y devuelve los resultados.

    > [!IMPORTANT]
    > * El script de entrada es específico para su modelo. Debe comprender el formato de los datos de la solicitud entrante, el formato de los datos que espera el modelo y el formato de los datos que se devuelven a los clientes.
    >
    >   Si los datos de la solicitud están en un formato que el modelo no puede usar, el script puede transformarlos a un formato aceptable. También puede transformar la respuesta antes de devolverla al cliente.
    >
    > * El SDK de Azure Machine Learning no proporciona una manera para que las implementaciones de servicios web o IoT Edge tengan acceso al almacén de datos o a los conjuntos de datos. Si el modelo implementado necesita acceder a los datos almacenados fuera de la implementación, como los datos de una cuenta de almacenamiento de Azure, debe desarrollar una solución de código personalizada mediante el SDK pertinente. Por ejemplo, el [SDK de Azure Storage para Python](https://github.com/Azure/azure-storage-python).
    >
    >   Otra alternativa que puede funcionar para su escenario son las [predicciones por lotes](how-to-run-batch-predictions.md), que proporcionan acceso a los almacenes de datos durante la puntuación.

* **Dependencias**, como scripts de asistente o paquetes de Python/Conda, necesarias para ejecutar el modelo o el script de entrada.

* **La configuración de la implementación** del destino de proceso que hospeda el modelo implementado. Esta configuración describe los aspectos, como los requisitos de memoria y CPU, necesarios para ejecutar el modelo.

Estos elementos se encapsulan en una *configuración de inferencia* y una *configuración de implementación*. La configuración de inferencia hace referencia al script de entrada y a otras dependencias. Estas configuraciones se definen mediante programación cuando se usa el SDK para realizar la implementación. Se definen en archivos JSON cuando se usa la CLI.

### <a id="script"></a> 1. Definición de los scripts y dependencias de entrada

El script de entrada recibe los datos enviados a un servicio web implementado y los pasa al modelo. A continuación, toma la respuesta devuelta por el modelo y la envía al cliente. *El script es específico para su modelo*. Debe entender los datos que el modelo espera y devuelve.

El script contiene dos funciones que cargan y ejecutan el modelo:

* `init()`: normalmente, esta función carga el modelo en un objeto global. Esta función solo se ejecuta una vez cuando se inicia el contenedor de Docker para el servicio web.

* `run(input_data)`: esta función usa el modelo para predecir un valor basado en los datos de entrada. Las entradas y salidas de la ejecución suelen usar el formato JSON para la serialización y deserialización. También puede trabajar con datos binarios sin formato. Puede transformar los datos antes de enviarlos al modelo o antes de devolverlos al cliente.

#### <a name="locate-model-files-in-your-entry-script"></a>Localización de archivos de modelo en el script de entrada

Hay dos maneras de buscar modelos en el script de entrada:
* `AZUREML_MODEL_DIR`: variable de entorno que contiene la ruta de acceso a la ubicación del modelo.
* `Model.get_model_path`: API que devuelve la ruta de acceso al archivo de modelo mediante el nombre del modelo registrado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR es una variable de entorno que se crea durante la implementación del servicio. Puede usar esta variable de entorno para buscar la ubicación de los modelos implementados.

En la tabla siguiente se describe el valor de AZUREML_MODEL_DIR en función del número de modelos implementados:

| Implementación | Valor de la variable de entorno |
| ----- | ----- |
| Modelo único | Ruta de acceso a la carpeta que contiene el modelo. |
| Varios modelos | Ruta de acceso a la carpeta que contiene todos los modelos. Los modelos se encuentran por nombre y versión en esta carpeta (`$MODEL_NAME/$VERSION`). |

Para obtener la ruta de acceso a un archivo en un modelo, combine la variable de entorno con el nombre del archivo que está buscando.
Los nombres de archivo de los archivos de modelo se conservan durante el registro y la implementación. 

**Ejemplo de modelo único**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Ejemplo de varios modelos**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Cuando registra un modelo, puede proporcionar un nombre de modelo que se usa para administrar este en el registro. Usará este nombre con el método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar la ruta de acceso del archivo del modelo en el sistema de archivos local. Si registra una carpeta o una colección de archivos, esta API devolverá la ruta de acceso del directorio que contiene esos archivos.

Al registrar un modelo, se le asigna un nombre. El nombre se corresponde con la ubicación donde se coloque el modelo, ya sea localmente o durante la implementación del servicio.

> [!IMPORTANT]
> Si usó aprendizaje automático automatizado para entrenar un modelo, se utiliza un valor `model_id` como nombre del modelo. Para ver un ejemplo de registro e implementación de un modelo entrenado con aprendizaje automático automatizado, consulte [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) en GitHub.

El ejemplo siguiente devolverá una ruta de acceso a un único archivo denominado `sklearn_mnist_model.pkl` (que se registró con el nombre `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Generación automática de esquemas (Opcional)

Para generar automáticamente un esquema para el servicio web, proporcione un ejemplo de la entrada y salida del constructor de uno de los objetos de tipo definidos. El tipo y ejemplo se usan para crear automáticamente el esquema. Azure Machine Learning creará, a continuación, una especificación de [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para el servicio web durante la implementación.

Actualmente se admiten estos tipos:

* `pandas`
* `numpy`
* `pyspark`
* Objeto estándar de Python

Para usar la generación de esquemas, incluya el paquete `inference-schema` en el archivo de entorno de Conda. Para más información sobre este paquete, consulte [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Ejemplo de archivo de dependencias

El siguiente código YAML es un ejemplo de un archivo de dependencias de Conda para inferencia:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Si la dependencia está disponible tanto a través de Conda como de PIP (de PyPi), Microsoft recomienda usar la versión de Conda, ya que los paquetes de Conda normalmente incluyen archivos binarios pregenerados que hacen que la instalación sea más confiable.
>
> Para obtener más información, consulte la [descripción de Conda y PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Para comprobar si la dependencia está disponible a través de Conda, use el comando `conda search <package-name>` o los índices de paquetes en [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) y [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Si desea usar la generación automática de esquemas, el script de entrada debe importar los paquetes `inference-schema`.

Defina los formatos de ejemplo de entrada y salida de las variables `input_sample` y `output_sample`, que representan los formatos de solicitud y respuesta del servicio web. Use estos ejemplos en los decoradores de entrada y salida de la función `run()`. El siguiente ejemplo de Scikit-learn usa generación de esquemas.

##### <a name="example-entry-script"></a>Script de entrada de ejemplo

En el siguiente ejemplo se muestra cómo aceptar y devolver datos JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

En el ejemplo siguiente se muestra cómo definir datos de entrada como un diccionario `<key: value>` mediante DataFrame. Este método se admite para consumir el servicio Web implementado desde Power BI. ([Obtenga más información sobre cómo consumir el servicio web desde Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)).

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para más ejemplos, consulte los siguientes scripts:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Datos binarios

Si el modelo acepta datos binarios, como una imagen, debe modificar el archivo `score.py` usado para la implementación para aceptar solicitudes HTTP sin procesar. Para aceptar los datos sin procesar, use la clase `AMLRequest` en el script de entrada y agregue el elemento decorador `@rawhttp` a la función `run()`.

Este es un ejemplo de `score.py` que acepta datos binarios:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La clase `AMLRequest` está en el espacio de nombres `azureml.contrib`. Las entidades de este espacio de nombres cambian con frecuencia mientras trabajamos para mejorar el servicio. Todo el contenido de este espacio de nombres debe considerarse una versión preliminar que no es completamente compatible con Microsoft.
>
> Si necesita probar esto en su entorno de desarrollo local, puede instalar los componentes mediante el comando siguiente:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Uso compartido de recursos entre orígenes (CORS)

Uso compartido de recursos entre orígenes es una manera de permitir que los recursos en una página web se soliciten desde otro dominio. CORS funciona a través de los encabezados HTTP enviados con la solicitud del cliente y se devuelven con la respuesta del servicio. Para más información sobre CORS y los encabezados válidos, consulte [Intercambio de recursos de origen cruzado](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) en Wikipedia.

Para configurar la implementación del modelo para que admita CORS, use la clase `AMLResponse` en el script de entrada. Esta clase permite establecer los encabezados en el objeto de respuesta.

En el ejemplo siguiente se establece el encabezado `Access-Control-Allow-Origin` para la respuesta desde el script de entrada:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La clase `AMLResponse` está en el espacio de nombres `azureml.contrib`. Las entidades de este espacio de nombres cambian con frecuencia mientras trabajamos para mejorar el servicio. Todo el contenido de este espacio de nombres debe considerarse una versión preliminar que no es completamente compatible con Microsoft.
>
> Si necesita probar esto en su entorno de desarrollo local, puede instalar los componentes mediante el comando siguiente:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definición de InferenceConfig

La configuración de inferencia describe cómo configurar el modelo para realizar predicciones. Esta configuración no forma parte del script de entrada. Hace referencia a su script de entrada y se usa para localizar todos los recursos que requiere la implementación. Se usa más adelante, cuando se implementa el modelo.

La configuración de inferencia puede usar entornos de Azure Machine Learning para definir las dependencias de software necesarias para la implementación. Los entornos permiten crear, administrar y reutilizar las dependencias de software necesarias para el entrenamiento y la implementación. En el ejemplo siguiente se muestra cómo cargar un entorno desde el área de trabajo y luego usarlo con la configuración de inferencia:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Para obtener más información sobre los entornos, consulte el tema sobre la [creación y administración de entornos de entrenamiento e implementación](how-to-use-environments.md).

También puede especificar directamente las dependencias sin usar un entorno. En el ejemplo siguiente se muestra cómo crear una configuración de inferencia que carga las dependencias de software desde un archivo de Conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Para más información, consulte la documentación de la clase [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

Para información sobre el uso de una imagen personalizada de Docker con una configuración de inferencia, consulte [Cómo implementar un modelo con una imagen personalizada de Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Ejemplo de la CLI de InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

El comando siguiente muestra cómo implementar un modelo mediante la CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

En este ejemplo, la configuración especifica la siguiente configuración:

* Este modelo requiere Python.
* El [script de entrada](#script), que se usa para controlar las solicitudes web que se envían al servicio implementado.
* El archivo de Conda que describe los paquetes de Python necesarios para realizar la inferencia.

Para información sobre el uso de una imagen personalizada de Docker con una configuración de inferencia, consulte [Cómo implementar un modelo con una imagen personalizada de Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definición de la configuración de la implementación

Antes de implementar el modelo, debe definir la configuración de esta. *La configuración de implementación es específica del destino de proceso que hospedará el servicio web.* Por ejemplo, cuando implementa un modelo de forma local, debe especificar el puerto en el que el servicio aceptará las solicitudes. La configuración de la implementación no forma parte del script de entrada. Se usa para definir las características del destino de proceso que hospedará el modelo y el script de entrada.

Es posible que también tenga que crear el recurso de proceso si, por ejemplo, aún no tiene una instancia de Azure Kubernetes Service (AKS) asociada con el área de trabajo.

La tabla siguiente proporciona un ejemplo de creación de una configuración de implementación para cada destino de proceso:

| Destino de proceso | Ejemplo de configuración de implementación |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Las clases de servicios web locales, de Azure Container Instances y AKS se puede importar desde `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Generación de perfiles

Antes de implementar el modelo como un servicio, puede que desee generar un perfil para determinar los requisitos de CPU y memoria óptimos. Puede usar el SDK o la CLI para generar perfiles del modelo. En los siguientes ejemplos se muestra cómo crear un perfil de un modelo mediante el SDK.

> [!IMPORTANT]
> Al usar la generación de perfiles, la configuración de inferencia que proporciona no puede hacer referencia a un entorno de Azure Machine Learning. En su lugar, defina las dependencias de software mediante el parámetro `conda_file` del objeto `InferenceConfig`.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Este código muestra un resultado similar a la siguiente salida:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Los resultados de la generación de perfiles del modelo se emiten como un objeto `Run`.

Para obtener información sobre el uso de la generación de perfiles desde la CLI, consulte el [perfil de modelo az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Para más información, consulte estos documentos:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profile()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Esquema del archivo de configuración de inferencia](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Implementación en el destino

La implementación usa la configuración de implementación de la configuración de inferencia para implementar los modelos. El proceso de implementación es similar, independientemente del destino de proceso. La implementación en AKS es ligeramente diferente, ya que debe proporcionar una referencia al clúster de AKS.

### <a name="securing-deployments-with-ssl"></a>Protección de implementaciones con SSL

Para obtener más información sobre cómo proteger una implementación de servicio web, vea [Uso de SSL para proteger un servicio web](how-to-secure-web-service.md#enable).

### <a id="local"></a> Implementación local

Para implementar un modelo de forma local, debe tener Docker instalado en la máquina local.

#### <a name="using-the-sdk"></a>Uso del SDK
zzs
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para más información, consulte la documentación de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) y [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Uso de la CLI

Para implementar un modelo con la CLI, use el siguiente comando. Reemplace `mymodel:1` por el nombre y la versión del modelo registrado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Para más información, consulte la documentación de [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

### <a id="notebookvm"></a> Servicio web de máquina virtual de Notebook (desarrollo/pruebas)

Consulte [Implementación de un modelo en las máquinas virtuales de Azure Machine Learning Notebook](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a> Azure Container Instances (desarrollo/pruebas)

Consulte [Implementación en Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes Service (desarrollo/pruebas y producción)

Consulte [Implementación en Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Consumo de servicios web

Cada servicio web implementado proporciona una API REST, por lo que puede crear aplicaciones cliente en diversos lenguajes de programación.
Si ha habilitado la autenticación por clave para el servicio, deberá proporcionar una clave de servicio como token en el encabezado de la solicitud.
Si ha habilitado la autenticación por tokens, deberá proporcionar un token JWT de Azure Machine Learning como token de portador en el encabezado de solicitud.

> [!TIP]
> Puede recuperar el documento JSON del esquema después de implementar el servicio. Use la [propiedad swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) del servicio web implementado (por ejemplo, `service.swagger_uri`) para obtener el URI del archivo Swagger del servicio web local.

### <a name="request-response-consumption"></a>Consumo de solicitud-respuesta

Este es un ejemplo de cómo invocar el servicio en Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para más información, consulte [Consumir un modelo de Azure Machine Learning que está implementado como un servicio web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Esquema de servicio web (especificación de OpenAPI)

Si ha usado la generación automática de esquemas con la implementación, puede obtener la dirección de la especificación de OpenAPI para el servicio mediante la [propiedad swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por ejemplo, `print(service.swagger_uri)`). Use una solicitud GET o abra el URI en un explorador para recuperar la especificación.

El siguiente documento JSON es un ejemplo de un esquema (especificación de OpenAPI) generado para una implementación:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Para más información, consulte la [especificación de OpenAPI](https://swagger.io/specification/).

Para obtener una utilidad que puede crear bibliotecas de cliente a partir de la especificación, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a> Inferencia por lotes
Los destinos de proceso de Azure Machine Learning se crean y administran mediante Azure Machine Learning. Estos se pueden usar para la predicción por lotes en canalizaciones de Azure Machine Learning.

Para ver un tutorial sobre la inferencia por lotes con Proceso de Azure Machine Learning, consulte [Cómo ejecutar predicciones por lotes](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a> Inferencia de IoT Edge
La compatibilidad con implementaciones en el perímetro está en versión preliminar. Para más información, consulte [Implementación de Azure Machine Learning como un módulo de IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a> Actualización de servicios web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implementación de modelos de forma continuada

Puede implementar continuamente modelos mediante la extensión de Machine Learning para [Azure DevOps](https://azure.microsoft.com/services/devops/). Puede usar la extensión de Machine Learning para Azure DevOps para desencadenar una canalización de implementación cuando se registra un nuevo modelo de aprendizaje automático en el área de trabajo de Azure Machine Learning.

1. Regístrese en [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), lo que hace posible la integración continua y la entrega de la aplicación en cualquier plataforma o nube. (Tenga en cuenta que Azure Pipelines no es lo mismo que las [canalizaciones de Machine Learning](concept-ml-pipelines.md#compare)).

1. [Cree un proyecto de Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale la [extensión de Machine Learning para Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use las conexiones de servicio para configurar una conexión de entidad de servicio para el área de trabajo de Azure Machine Learning para tener acceso a los artefactos. Vaya a configuración del proyecto, seleccione **Conexiones de servicio** y, luego, seleccione **Azure Resource Manager**:

    [![Selección de Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. En la lista **Nivel de ámbito**, seleccione **AzureMLWorkspace** y luego escriba el resto de los valores:

    ![Selección de AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implementar continuamente el modelo de aprendizaje automático mediante Azure Pipelines, seleccione **Versión** en Canalizaciones. Agregue un artefacto nuevo y seleccione el artefacto del **modelo de AzureML** y la conexión de servicio que creó en el paso anterior. Seleccione el modelo y la versión para desencadenar una implementación:

    [![Selección del modelo de AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite el desencadenador del modelo en el artefacto del modelo. Al activar el desencadenador, cada vez que se registra la versión especificada (es decir, la versión más reciente) de ese modelo en el área de trabajo, se desencadena una canalización de versión de Azure DevOps.

    [![Habilitación del desencadenador de modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para más ejemplos y proyectos de muestra, consulte estos repositorios de ejemplo en GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Descarga de un modelo
Si desea descargar el modelo para usarlo en su propio entorno de ejecución, puede hacerlo con los comandos de SDK/CLI siguientes:

SDK:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Versión preliminar) Implementación de modelo sin código

La implementación del modelo sin código se encuentra actualmente en versión preliminar y admite los siguientes marcos de aprendizaje automático:

### <a name="tensorflow-savedmodel-format"></a>Formato Tensorflow SavedModel

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modelos de ONNX

El registro y la implementación del modelo ONNX se admiten en cualquier gráfico de inferencia de ONNX. Los pasos de preprocesamiento y postproceso no se admiten actualmente.

Este es un ejemplo de cómo registrar e implementar un modelo de MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Modelos de Scikit-learn

Se admite la implementación de modelo sin código para todos los tipos de modelos integrados de Scikit-learn.

Este es un ejemplo de cómo registrar e implementar un modelo de sklearn sin código adicional:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

NOTA:  Estas dependencias se incluyen en el contenedor de inferencia sklearn integrado previamente:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelos de paquetes

En algunos casos, es posible que desee crear una imagen de Docker sin implementar el modelo (si, por ejemplo, pretende [implementar en Azure App Service](how-to-deploy-app-service.md)). O bien, puede que desee descargar la imagen y ejecutarla en una instalación local de Docker. Incluso, puede que desee descargar los archivos usados para compilar la imagen, inspeccionarlos, modificarlos y compilar la imagen manualmente.

El empaquetado de modelos permite hacer estas cosas. Empaqueta todos los recursos necesarios para hospedar un modelo como servicio web y le permite descargar una imagen de Docker totalmente compilada o los archivos necesarios para compilar una. Hay dos maneras de usar el empaquetado del modelos:

**Descarga de un modelo empaquetado:** descargue una imagen de Docker que contiene el modelo y otros archivos necesarios para hospedarla como servicio web.

**Generar un Dockerfile:** descargue el Dockerfile, el modelo, el script de entrada y otros recursos necesarios para compilar una imagen de Docker. Después, puede inspeccionar los archivos o realizar cambios antes de compilar la imagen localmente.

Ambos paquetes se pueden usar para obtener una imagen de Docker local.

> [!TIP]
> La creación de un paquete es similar a la implementación de un modelo. Se usa un modelo registrado y una configuración de inferencia.

> [!IMPORTANT]
> Para descargar una imagen completamente compilada o compilar una imagen localmente, debe tener [Docker](https://www.docker.com) instalado en el entorno de desarrollo.

### <a name="download-a-packaged-model"></a>Descarga del modelo empaquetado

En el ejemplo siguiente se muestra cómo compilar una imagen, que se registra en el registro de contenedor de Azure del área de trabajo:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Después de crear un paquete, puede usar `package.pull()` para extraer la imagen en el entorno de Docker local. La salida de este comando mostrará el nombre de la imagen. Por ejemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Después de descargar el modelo, use el comando `docker images` para ver en una lista las imágenes locales:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Para iniciar un contenedor local basándose en esta imagen, use el siguiente comando para iniciar un contenedor con nombre desde el shell o desde la línea de comandos. Reemplace el valor `<imageid>` por el identificador de la imagen que el comando `docker images` devuelve.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia la versión más reciente de la imagen con nombre `myimage`. Asigna el puerto local 6789 al puerto del contenedor en el que el servicio web está escuchando (5001). También asigna el nombre `mycontainer` al contenedor, lo que facilita la detención de este. Una vez iniciado el contenedor, puede enviar solicitudes a `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Generación de un Dockerfile y dependencias

En el ejemplo siguiente se muestra cómo descargar el Dockerfile, el modelo y otros recursos necesarios para compilar una imagen localmente. El parámetro `generate_dockerfile=True` indica que quiere los archivos, no una imagen completamente compilada.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Este código descarga los archivos necesarios para compilar la imagen en el directorio `imagefiles`. El Dockerfile incluido en los archivos guardados hace referencia a una imagen base almacenada en un registro de contenedor de Azure. Al compilar la imagen en la instalación de Docker local, necesita usar la dirección, el nombre de usuario y la contraseña para autenticarse en el registro. Siga estos pasos para compilar la imagen mediante una instalación local de Docker:

1. Desde un shell o una sesión de línea de comandos, use el comando siguiente para autenticar Docker con el registro de contenedor de Azure. Reemplace `<address>`, `<username>` y `<password>` por los valores recuperados con `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para compilar la imagen, utilice el siguiente comando. Reemplace `<imagefiles>` por la ruta de acceso del directorio donde `package.save()` guardó los archivos.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando establece el nombre de la imagen en `myimage`.

Para comprobar la imagen está compilada, use el comando `docker images`. Debería ver la imagen `myimage` en la lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Para iniciar un nuevo contenedor basado en esta imagen, use el comando siguiente:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia la versión más reciente de la imagen con nombre `myimage`. Asigna el puerto local 6789 al puerto del contenedor en el que el servicio web está escuchando (5001). También asigna el nombre `mycontainer` al contenedor, lo que facilita la detención de este. Una vez iniciado el contenedor, puede enviar solicitudes a `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Cliente de ejemplo para probar el contenedor local

El código siguiente es un ejemplo de un cliente de Python que se puede usar con el contenedor:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Para ver clientes de ejemplo en otros lenguajes de programación, vea [Consumir modelos implementados como servicios web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Detención del contenedor de Docker

Para detener el contenedor, use el siguiente comando desde una línea de comandos o un shell diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para eliminar un servicio web implementado, use `service.delete()`.
Para eliminar un modelo registrado, use `model.delete()`.

Para más información, consulte la documentación para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) y [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Pasos siguientes

* [Cómo implementar un modelo con una imagen personalizada de Docker](how-to-deploy-custom-docker-image.md)
* [Solución de problemas de implementación](how-to-troubleshoot-deployment.md)
* [Protección de los servicios web de Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Consumir un modelo de Azure Machine Learning que está implementado como un servicio web](how-to-consume-web-service.md)
* [Supervisión de los modelos de Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Recopilar datos de modelos en producción](how-to-enable-data-collection.md)

