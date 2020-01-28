---
title: 'Tutorial: Entrenamiento e implementación de un modelo de TensorFlow de clasificación de imágenes con la extensión Azure Machine Learning para Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Obtenga información sobre el entrenamiento y la implementación de un modelo de TensorFlow de clasificación de imágenes con la extensión Azure Machine Learning para Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 899681f2bb9c3ef2a0368015a58db30a843738f5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157386"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Entrenamiento e implementación de un modelo de TensorFlow de clasificación de imágenes con la extensión Azure Machine Learning para Visual Studio Code

Obtenga información sobre el entrenamiento de un modelo de TensorFlow de clasificación de imágenes para el reconocimiento de números manuscritos con la extensión Azure Machine Learning para Visual Studio Code.

En este tutorial, ha aprendido las tareas siguientes:

> [!div class="checklist"]
> * Comprendiendo el código
> * Crear un área de trabajo
> * Creación de un experimento
> * Configuración de destinos de proceso
> * Ejecución de un archivo de configuración
> * Entrenamiento de un modelo
> * Registrar un modelo
> * Implementar un modelo

## <a name="prerequisites"></a>Prerequisites

- Suscripción de Azure. Si no tiene una, regístrese para probar la [versión gratuita o de pago de Azure Machine Learning](https://aka.ms/AMLFree).
- Instale [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), un editor de código ligero y multiplataforma.
- Extensión Azure Machine Learning Studio para Visual Studio Code Para obtener instrucciones de instalación, consulte el [tutorial Configuración de la extensión Azure Machine Learning para Visual Studio Code](./tutorial-setup-vscode-extension.md).

## <a name="understand-the-code"></a>Comprendiendo el código

El código de este tutorial usa TensorFlow para entrenar un modelo de aprendizaje automático de clasificación de imágenes que clasifica dígitos manuscritos de 0 a 9. Para ello, crea una red neuronal que toma los valores de píxeles de una imagen de 28 x 28 píxeles como entrada y genera una lista de 10 probabilidades, una para cada uno de los dígitos que se están clasificando. A continuación se muestra un ejemplo del aspecto de los datos.  

![Dígitos de MNIST](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Para obtener el código de este tutorial, descargue y descomprima el [repositorio VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) donde quiera en el equipo.

## <a name="create-a-workspace"></a>Crear un área de trabajo

Lo primero que hay que hacer para compilar una aplicación en Azure Machine Learning es crear un área de trabajo. Un área de trabajo contiene los recursos para entrenar modelos, así como los propios modelos entrenados. Para más información, consulte [Qué es un área de trabajo](./concept-workspace.md). 

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure** para abrir la vista de Azure Machine Learning.
1. Haga clic con el botón derecho en su suscripción de Azure y seleccione **Crear área de trabajo**. 
    
    > [!div class="mx-imgBorder"]
    > ![Creación de un área de trabajo](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. De forma predeterminada, se crea un nombre que contiene la fecha y la hora de la creación. En la paleta de comandos, cambie el nombre a "TeamWorkspace" y presione **Entrar**.
1. Seleccione **Crear un grupo de recursos nuevo** en la paleta de comandos. 
1. Escriba "TeamWorkspace-rg" en el cuadro de texto de la paleta de comandos y presione **Entrar**. 
1. En la paleta de comandos, elija una ubicación para el área de trabajo. Se recomienda seleccionar la ubicación que esté más cerca de la ubicación donde quiere implementar el modelo. En este caso, elija **Oeste de EE. UU. 2**.
1. Cuando se le pida que seleccione una SKU de área de trabajo, seleccione **Basic** para crear un área de trabajo básica. Para más información sobre las distintas ofertas de área de trabajo, consulte [Información general de Azure Machine Learning](./overview-what-is-azure-ml.md#sku).

En este momento, se realiza una solicitud a Azure para crear una nueva área de trabajo en su cuenta. Después de unos minutos, la nueva área de trabajo aparece en el nodo de la suscripción. 

## <a name="create-an-experiment"></a>Creación de un experimento

Se pueden crear uno o más experimentos en el área de trabajo y así poder realizar un seguimiento y analizar la ejecuciones de entrenamiento de modelos individuales. Las ejecuciones se pueden realizar en la nube de Azure o en su máquina local.

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning.
1. Expanda el nodo de la suscripción.
1. Expanda el nodo **TeamWorkspace**. 
1. Haga clic con el botón derecho en el nodo **Experimentos**.
1. Seleccione **Crear experimento** en el menú contextual.

    > [!div class="mx-imgBorder"]
    > ![Creación de un experimento](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. En el símbolo de la paleta de comandos, asigne al experimento el nombre "MNIST" y presione **Entrar** para crear el nuevo experimento. 

Al igual que con las áreas de trabajo, se envía una solicitud a Azure para crear un experimento con las configuraciones proporcionadas. Después de unos minutos, el nuevo experimento aparece en el nodo *Experimentos* del área de trabajo. 

## <a name="configure-compute-targets"></a>Configuración de destinos de proceso

Un destino de proceso es el entorno o recurso informático en el que se ejecutan scripts y se implementan modelos entrenados. Para más información, consulte la [documentación sobre destinos de proceso de Azure Machine Learning](./concept-compute-target.md).

Para crear un destino de proceso:

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning. 
1. Expanda el nodo de la suscripción. 
1. Expanda el nodo **TeamWorkspace**. 
1. En el nodo del área de trabajo, haga clic con el botón derecho en el nodo **Proceso** y elija **Crear proceso**. 

    > [!div class="mx-imgBorder"]
    > ![Creación de un destino de proceso](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Seleccione **Proceso de Azure Machine Learning (AmlCompute)** . Proceso de Azure Machine Learning es una infraestructura de proceso administrado que permite al usuario crear fácilmente un proceso de uno o varios nodos que puede usarse con otros usuarios en el área de trabajo.
1. Elija el tamaño de la máquina virtual. En el símbolo de la paleta de comandos, seleccione **Standard_F2s_v2**. El tamaño de la máquina virtual afecta a la cantidad de tiempo que se tarda en entrenar los modelos. Para más información sobre los tamaños de máquina virtual, consulte [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. En el símbolo de la paleta de comandos, asigne el nombre "TeamWkspc-com" al proceso y presione **Entrar** para crear el proceso.

Después de unos minutos, el nuevo destino de proceso aparece en el nodo *Proceso* del área de trabajo.

## <a name="create-a-run-configuration"></a>Creación de una configuración de ejecución

Cuando envía una ejecución de entrenamiento a un destino de proceso, también envía la configuración necesaria para ejecutar el trabajo de entrenamiento. Por ejemplo, el script que contiene el código de entrenamiento y las dependencias de Python necesarias para ejecutarlo.

Para crear una configuración de ejecución:

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning. 
1. Expanda el nodo de la suscripción. 
1. Expanda el nodo **TeamWorkspace**. 
1. En el nodo del área de trabajo, haga clic con el botón derecho en el nodo de ejecución **TeamWkspc-com** y elija **Crear configuración de ejecución**.

    > [!div class="mx-imgBorder"]
    > ![Creación de una configuración de ejecución](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. En el símbolo de la paleta de comandos, asigne el nombre "MNIST-rc" a la configuración de ejecución y presione **Entrar** para crear el proceso.
1. A continuación, seleccione **TensorFlow Single-Node Training** (Entrenamiento de nodo único de TensorFlow) como tipo de trabajo de entrenamiento.
1. Presione **Entrar** para buscar el archivo de script que se va a ejecutar en el proceso. En este caso, el script para entrenar el modelo es el archivo `train.py` dentro del directorio `vscode-tools-for-ai/mnist-vscode-docs-sample`.
1. Escriba lo siguiente en el símbolo de la paleta de comandos para especificar los paquetes necesarios.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Un archivo denominado `MNIST-rc.runconfig` aparece en VS Code con un contenido similar al siguiente:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Cuando esté satisfecho con la configuración, seleccione **Ver > Paleta de comandos** para abrir la paleta de comandos.
1. Escriba el siguiente comando en la paleta de comandos para guardar el archivo de configuración de ejecución.

    ```text
    Azure ML: Save and Continue
    ```

La configuración de ejecución `MNIST-rc` se agrega en el nodo de ejecución *TeamWkspc-com*.

## <a name="train-the-model"></a>Entrenamiento del modelo

Durante el proceso de entrenamiento, se crea un modelo de TensorFlow al procesar los datos de entrenamiento y los patrones de aprendizaje insertados en ellos para cada uno de los dígitos correspondientes que se están clasificando. 

Para ejecutar un experimento de Azure Machine Learning:

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning. 
1. Expanda el nodo de la suscripción. 
1. Expanda el nodo **TeamWorkspace > Experimentos**. 
1. Haga clic con el botón derecho en el experimento **MNIST**.
1. Seleccione **Run Experiment** (Ejecutar experimento).

    > [!div class="mx-imgBorder"]
    > ![Ejecución de un experimento](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. En la paleta de comandos, seleccione el destino de proceso **TeamWkspc-com**.
1. A continuación, seleccione la configuración de ejecución **MNIST-rc**.
1. En este momento, se envía una solicitud a Azure para ejecutar el experimento en el destino de proceso seleccionado en el área de trabajo. Este proceso tarda varios minutos. La cantidad de tiempo que se tarda en ejecutar el trabajo de entrenamiento se ve afectada por varios factores, como el tipo de proceso y el tamaño de los datos de entrenamiento. Para realizar un seguimiento del progreso del experimento, haga clic con el botón derecho en el nodo de ejecución actual y seleccione **View Run in Azure Portal** (Ver ejecución en Azure Portal).
1. Cuando aparezca el cuadro de diálogo que solicita abrir un sitio web externo, seleccione **Abrir**.

    > [!div class="mx-imgBorder"]
    > ![Seguimiento del progreso del experimento](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Cuando se realiza el entrenamiento del modelo, la etiqueta de estado junto al nodo de ejecución se actualiza a "Completado".

## <a name="register-the-model"></a>Registro del modelo

Ahora que ha entrenado el modelo, puede registrarlo en el área de trabajo. 

Para registrar el modelo:

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning.
1. Expanda el nodo de la suscripción. 
1. Expanda el nodo **TeamWorkspace > Experimentos > MNIST**.
1. Obtenga las salidas del modelo generadas a partir del entrenamiento del modelo. Haga clic con el botón derecho en el nodo de ejecución **Ejecución 1** y seleccione **Download outputs** (Descargar salidas). 

    > [!div class="mx-imgBorder"]
    > ![Descarga de salidas de modelo](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Elija el directorio en el que desea guardar las salidas descargadas. De forma predeterminada, las salidas se colocan en el directorio abierto actualmente en Visual Studio Code.
1. Haga clic con el botón derecho en el nodo **Modelos** y elija **Register Model** (Registrar modelo).

    > [!div class="mx-imgBorder"]
    > ![Registro de un modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. En la paleta de comandos, asigne al modelo el nombre "MNIST-TensorFlow-model" y presione **Entrar**.
1. Un modelo de TensorFlow se compone de varios archivos. Seleccione **Model folder** (Carpeta de modelos) como formato de la ruta de acceso del modelo en la paleta de comandos. 
1. Seleccione el directorio `azureml_outputs/Run_1/outputs/Run_1/outputs/outputs/model`.

    Un archivo que contiene las configuraciones del modelo aparece en Visual Studio Code con contenido similar al siguiente:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Una vez que esté satisfecho con la configuración, abra la paleta de comandos y escriba el siguiente comando para guardarla:

    ```text
    Azure ML: Save and Continue
    ```

Después de unos minutos, el modelo aparece en el nodo *Modelos*.

## <a name="deploy-the-model"></a>Implementación del modelo

En Visual Studio Code, puede implementar el modelo como un servicio web para lo siguiente:

+ Azure Container Instances (ACI).
+ Azure Kubernetes Service (AKS).

No es necesario crear un contenedor ACI para realizar pruebas por adelantado, ya que estos contenedores se crean según se necesitan. Sin embargo, es necesario configurar clústeres AKS de antemano. Para más información sobre las opciones de implementación, consulte [Implementación de modelos con Azure Machine Learning](how-to-deploy-and-where.md).

Para implementar un servicio web como ACI:

1. En la barra de actividad de Visual Studio Code, haga clic en el icono de **Azure**. Aparecerá la vista de Azure Machine Learning.
1. Expanda el nodo de la suscripción. 
1. Expanda el nodo **TeamWorkspace > Modelos**. 
1. Haga clic con el botón derecho en **MNIST-TensorFlow-model** y seleccione **Deploy Service from Registered Model** (Implementar el servicio a partir del modelo registrado).

    > [!div class="mx-imgBorder"]
    > ![Implementación del modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. En la paleta de comandos, seleccione **Azure Container Instances**.
1. En la paleta de comandos, asigne al servicio el nombre "mnist-tensorflow-svc" y presione **Entrar**.
1. Elija el script que se va a ejecutar en el contenedor; para ello, presione **Entrar** en la paleta de comandos y busque el archivo `score.py` en el directorio `mnist-vscode-docs-sample`.
1. Proporcione las dependencias necesarias para ejecutar el script; para ello, presione **Entrar** en la paleta de comandos y busque el archivo `env.yml` en el directorio `mnist-vscode-docs-sample`.

    Un archivo que contiene las configuraciones del modelo aparece en Visual Studio Code con contenido similar al siguiente:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. Una vez que esté satisfecho con la configuración, abra la paleta de comandos y escriba el siguiente comando para guardarla:

    ```text
    Azure ML: Save and Continue
    ```

En este momento, se envía una solicitud a Azure para implementar el servicio web. Este proceso tarda varios minutos. Una vez implementado, el nuevo servicio aparece en el nodo *Puntos de conexión*.

## <a name="next-steps"></a>Pasos siguientes

* Para ver un tutorial sobre cómo entrenar con Azure Machine Learning fuera de Visual Studio Code, consulte [Tutorial: Entrenamiento de modelos con Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para ver un tutorial sobre cómo editar, ejecutar y depurar código de forma local, consulte el [tutorial de hello world de Python](https://code.visualstudio.com/docs/Python/Python-tutorial).

