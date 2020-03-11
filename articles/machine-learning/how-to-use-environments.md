---
title: Creación de entornos de Azure Machine Learning reutilizables
titleSuffix: Azure Machine Learning
description: Cree y administre entornos para el entrenamiento y la implementación de modelos. Administre paquetes de Python y otros valores para el entorno.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 0cb76884fd46a45bb45fa3e29a03a6f9dbd0250b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920304"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Reutilización de entornos para entrenamiento e implementación con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

En este artículo, aprenda a crear y administrar [entornos](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning. Use los entornos para realizar un seguimiento de las dependencias de software de sus proyectos y reproducirlas a medida que evolucionan.

La administración de dependencias de software es una tarea común para los desarrolladores. Quiere asegurarse de que las compilaciones se puedan reproducir sin demasiada configuración manual del software. La clase `Environment` de Azure Machine Learning representa las soluciones para el desarrollo local, como pip y Conda, y ofrece una solución para el desarrollo en la nube local y distribuido.

En los ejemplos de este artículo se muestran los siguientes procedimientos:

* Crear un entorno y especificar las dependencias de paquetes.
* Recuperar y actualizar los entornos.
* Usar un entorno para entrenamiento.
* Usar un entorno para la implementación de servicios web.

Para obtener información general de alto nivel sobre cómo funcionan los entornos en Azure Machine Learning, consulte [¿Qué son los entornos de ML?](concept-environments.md)

## <a name="prerequisites"></a>Prerrequisitos

* El [SDK de Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Un [área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Creación de un entorno

En las secciones siguientes se exploran las diversas formas en que puede crear un entorno para sus experimentos.

### <a name="use-a-curated-environment"></a>Uso de un entorno mantenido

Puede seleccionar uno de los entornos mantenidos para empezar: 

* El entorno _AzureML-minimal_ contiene un conjunto mínimo de paquetes para habilitar el seguimiento de ejecución y la carga de recursos. Puede utilizarlo como punto inicial para su propio entorno.

* El entorno de _AzureML-Tutorial_ contiene paquetes de ciencia de datos comunes. Estos paquetes incluyen Scikit-Learn, Pandas, Matplotlib y un conjunto mayor de paquetes de azureml-sdk.

Los entornos mantenidos están respaldados por imágenes de Docker en caché. Este respaldo reduce el costo de preparación de la ejecución.

Use el método `Environment.get` para seleccionar uno de los entornos mantenidos:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Puede enumerar los entornos mantenidos y sus paquetes mediante el código siguiente:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  No empiece el nombre de su propio entorno con el prefijo _AzureML_. Este prefijo está reservado para entornos mantenidos.

### <a name="instantiate-an-environment-object"></a>Creación de instancias de un objeto de entorno

Para crear manualmente un entorno, importe la clase `Environment` desde el SDK. A continuación, use el siguiente código para crear instancias de un objeto de entorno.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Uso de archivos de especificación de Conda y pip

También puede crear un entorno a partir de una especificación de Conda o de un archivo de requisitos de pip. Use el método [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) o el método [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-). En el argumento del método, incluya el nombre del entorno y la ruta de acceso del archivo que desee.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Uso de entornos de Conda existentes

Si tiene un entorno de Conda en el equipo local, puede usar el servicio para crear un objeto de entorno. Mediante esta estrategia, puede volver a usar el entorno interactivo local en ejecuciones remotas.

En el código siguiente se crea un objeto de entorno a partir del entorno Conda existente `mycondaenv`. Utiliza el método [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Creación automática de entornos

Cree un entorno automáticamente mediante el envío de una ejecución de entrenamiento. Envíe la ejecución con el método `submit()`. Cuando envía una ejecución de entrenamiento, la compilación del nuevo entorno puede tardar varios minutos. La duración de la compilación depende del tamaño de las dependencias necesarias. 

Si no especifica un entorno en la configuración de ejecución antes de enviar la ejecución, se crea un entorno predeterminado de manera automática.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Del mismo modo, si usa un objeto [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para el entrenamiento, puede enviar la instancia del estimador directamente como ejecución sin tener que especificar un entorno. El objeto `Estimator` ya encapsula el entorno y el destino de proceso.

## <a name="add-packages-to-an-environment"></a>Adición de paquetes a un entorno

Agregue paquetes a un entorno con los archivos de Conda, pip o Private Wheel. Especifique cada dependencia del paquete mediante la clase [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py). Agréguelo a `PythonSection` del entorno.

### <a name="conda-and-pip-packages"></a>Paquetes Conda y pip

Si hay un paquete disponible en un repositorio de paquetes Conda, se recomienda usar la instalación de Conda en lugar de la instalación de pip. Los paquetes de Conda suelen incluir archivos binarios pregenerados que hacen que la instalación sea más confiable.

En el ejemplo siguiente se agrega al entorno. Agrega la versión 0.21.3 de `scikit-learn`. También agrega el paquete de `pillow`, `myenv`. En el ejemplo se usan el método [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) y el método [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-), respectivamente.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Archivos de Private Wheel

Puede usar archivos de Private Wheel de pip si primero los carga en el almacenamiento del área de trabajo. Se cargan mediante un método [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) estático. A continuación, capture la dirección URL de almacenamiento y pase la dirección URL al método `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Administración de entornos

Administre los entornos para que pueda actualizarlos, realizarles un seguimiento y reutilizarlos en todos los destinos de proceso y con otros usuarios del área de trabajo.

### <a name="register-environments"></a>Registro de entornos

El entorno se registra automáticamente en el área de trabajo cuando se envía una ejecución o se implementa un servicio web. También puede registrar manualmente el entorno mediante el método [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-). Esta operación convierte el entorno en una entidad con control de versiones y de la que se realiza el seguimiento en la nube. La entidad se puede compartir entre los usuarios del área de trabajo.

En el código siguiente se registra el entorno `myenv` en el área de trabajo `ws`.

```python
myenv.register(workspace=ws)
```

Cuando se usa el entorno por primera vez en el entrenamiento o la implementación, se registra en el área de trabajo. A continuación, se compila e implementa en el destino de proceso. El servicio almacena los entornos en caché. La reutilización de un entorno almacenado en caché tarda mucho menos tiempo que el uso de un nuevo servicio o uno que se haya actualizado.

### <a name="get-existing-environments"></a>Obtención de los entornos existentes

La clase `Environment` ofrece métodos que le permiten recuperar entornos existentes en el área de trabajo. Puede recuperar entornos por nombre, como lista, por ejecución de entrenamiento específica. Esta información es útil para la solución de problemas, la auditoría y la reproducibilidad.

#### <a name="view-a-list-of-environments"></a>Visualización de una lista de entornos

Vea los entornos en el área de trabajo mediante la clase [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-). A continuación, seleccione un entorno para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obtención de un entorno por nombre

También puede obtener un entorno específico por nombre y versión. En el código siguiente se usa el método [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) para recuperar la versión `1` del entorno `myenv` del área de trabajo `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Entrenamiento de un entorno específico de la ejecución

Para obtener el entorno que se usó para una ejecución específica una vez finalizado el entrenamiento, use el método [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) en la clase `Run`.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Actualización de un entorno existente

Imagine que cambia un entorno existente, por ejemplo, al agregar un paquete de Python. Después, se crea una nueva versión del entorno cuando se envía una ejecución, se implementa un modelo o se registra manualmente el entorno. El control de versiones le permite ver los cambios en el entorno a lo largo del tiempo.

Para actualizar la versión de un paquete de Python en un entorno existente, especifique el número de versión de ese paquete. Si no usa el número de versión exacto, Azure Machine Learning volverá a usar el entorno existente con sus versiones de paquete originales.

### <a name="debug-the-image-build"></a>Depuración de la compilación de la imagen

En el ejemplo siguiente se usa el método [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) para crear manualmente un entorno como imagen de Docker. Supervisa los registros de salida de la compilación de imágenes mediante [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). La imagen compilada aparece después en la instancia de Azure Container Registry del área de trabajo. Esta información resulta útil para la depuración.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Habilitación de Docker

 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) de la clase `Environment` de Azure Machine Learning le permite personalizar y controlar en detalle el sistema operativo invitado en el que se ejecuta el entrenamiento.

Cuando habilita Docker, el servicio compila una imagen de Docker. También crea un entorno de Python que usa sus especificaciones dentro de ese contenedor de Docker. Esta funcionalidad ofrece aislamiento y reproducibilidad adicionales para las ejecuciones del entrenamiento.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

De manera predeterminada, la imagen de Docker recién creada aparece en el registro de contenedor que está asociado al área de trabajo.  El nombre del repositorio tiene el formato *azureml/azureml_\<uuid\>* . La parte del identificador único (*uuid*) del nombre corresponde a un hash calculado a partir de la configuración del entorno. Esta correspondencia permite que el servicio determine si ya existe para reutilizar una imagen del entorno dado.

Además, el servicio usa automáticamente una de las [imágenes base](https://github.com/Azure/AzureML-Containers) basadas en Ubuntu Linux. Instala los paquetes de Python especificados. La imagen base tiene versiones de CPU y de GPU. Azure Machine Learning detecta automáticamente qué versión se va a usar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Como alternativa, puede especificar un Dockerfile personalizado. Es más sencillo empezar a partir de una de las imágenes base de Azure Machine Learning mediante el comando ```FROM``` de Docker y, después, agregar sus propios pasos personalizados. Use este enfoque si necesita instalar paquetes que no son de Python como dependencias.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

> [!NOTE]
> Si especifica `environment.python.user_managed_dependencies=False` mientras usa una imagen de Docker personalizada, el servicio compilará un entorno de Conda dentro de la imagen. La ejecución se ejecutará en ese entorno en lugar de usar las bibliotecas de Python que haya instalado en la imagen base. Establezca el parámetro en `True` para usar sus propios paquetes instalados.

## <a name="use-environments-for-training"></a>Uso de entornos para el entrenamiento

Para enviar una ejecución de entrenamiento, debe combinar el entorno, el [destino de proceso](concept-compute-target.md) y el script de Python de entrenamiento en una configuración de ejecución. Esta configuración es un objeto contenedor que se usa para enviar ejecuciones.

Cuando envía una ejecución de entrenamiento, la compilación de un nuevo entorno puede tardar varios minutos. La duración depende del tamaño de las dependencias necesarias. Los entornos se almacenan en caché en el servicio. Por tanto, siempre y cuando la definición del entorno permanezca inalterada, incurrirá en el tiempo de configuración completo una sola vez.

A continuación se proporciona un ejemplo de ejecución de script local en el que se usaría [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) como objeto contenedor.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Para deshabilitar el historial de ejecución o ejecutar instantáneas, use el valor de `ScriptRunConfig.run_config.history`.

Si no especifica el entorno en la configuración de ejecución, el servicio crea un entorno predeterminado cuando envía la ejecución.

### <a name="use-an-estimator-for-training"></a>Uso de un estimador para el entrenamiento

Si usa un [estimador](how-to-train-ml-models.md) para el entrenamiento, puede enviar la instancia del estimador directamente. Este ya encapsula el entorno y el destino de proceso.

En el código siguiente se usa un estimador para una ejecución de entrenamiento de nodo único. Se ejecuta en un proceso remoto para un modelo `scikit-learn`. Se supone que ha creado previamente un objeto de destino de proceso, `compute_target`, y un objeto de almacén de objetos, `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Uso de entornos para la implementación de servicios web

Puede usar entornos al implementar el modelo como servicio web. Esta funcionalidad permite un flujo de trabajo conectado y reproducible. En este flujo de trabajo, puede entrenar, probar e implementar el modelo con las mismas bibliotecas tanto en el proceso de entrenamiento como en el proceso de inferencia.

Para implementar un servicio web, combine el entorno, el proceso de inferencia, el script de puntuación y el modelo registrado en el objeto de implementación, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Para obtener más información, consulte [How and where to deploy models](how-to-deploy-and-where.md) (Cómo y dónde implementar modelos).

En este ejemplo, supongamos que ha completado una ejecución de entrenamiento. Ahora desea implementar ese modelo en Azure Container Instances. Al compilar el servicio web, los archivos de modelo y puntuación se montan en la imagen, y la pila de inferencia de Azure Machine Learning se agrega a la imagen.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Cuadernos de ejemplo

Este [cuaderno de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) amplía los conceptos y métodos que se muestran en este artículo.

En [Implementación de un modelo mediante una imagen base personalizada de Docker](how-to-deploy-custom-docker-image.md), se muestra cómo implementar un modelo mediante una imagen base personalizada de Docker.

En este [cuaderno de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) se muestra cómo implementar un modelo de Spark como servicio web.

## <a name="create-and-manage-environments-with-the-cli"></a>Creación y administración de entornos con la CLI

La [CLI de Azure Machine Learning](reference-azure-machine-learning-cli.md) refleja la mayor parte de la funcionalidad del SDK de Python. Puede usarla para crear y administrar entornos. Los comandos que se describen en esta sección muestran la funcionalidad básica.

El siguiente comando aplica la técnica scaffolding a los archivos para una definición de entorno predeterminada en el directorio especificado. Estos archivos son archivos JSON. Funcionan como la clase correspondiente en el SDK. Puede usar los archivos para crear nuevos entornos que tengan una configuración personalizada. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Ejecute el siguiente comando para registrar un entorno desde un directorio especificado.

```azurecli-interactive
az ml environment register -d myenvdir
```

Ejecute el comando siguiente para enumerar todos los entornos registrados.

```azurecli-interactive
az ml environment list
```

Descargue un entorno registrado con el siguiente comando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Pasos siguientes

* Para usar un destino de proceso administrado para entrenar un modelo, consulte [Tutorial: Entrenamiento de un modelo](tutorial-train-models-with-aml.md).
* Después de tener un modelo entrenado, aprenda [cómo y dónde implementar los modelos](how-to-deploy-and-where.md).
* Consulte la [referencia de SDK de la clase `Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Para obtener más información sobre los conceptos y métodos que se describen en este artículo, consulte el [cuaderno de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
