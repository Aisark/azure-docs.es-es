---
title: Implementación de modelos de Machine Learning en aplicaciones de Azure Functions (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar Azure Machine Learning para implementar un modelo en una aplicación de Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 9fba3221656405f2bf2b1654b43d687f1915cca6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2019
ms.locfileid: "74542291"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Implementación de un modelo de Machine Learning en Azure Functions (versión preliminar)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Obtenga información sobre cómo implementar un modelo de Azure Machine Learning como una aplicación de funciones en Azure Functions.

> [!IMPORTANT]
> Aunque tanto Azure Machine Learning como Azure Functions están disponibles con carácter general, la posibilidad de empaquetar un modelo desde Machine Learning Service en Functions está en versión preliminar.

Con Azure Machine Learning, puede crear una imagen de Docker a partir de modelos de aprendizaje automático entrenados. Ahora, Azure Machine Learning tiene la funcionalidad en versión preliminar para crear estos modelos de Machine Learning en las aplicaciones de funciones, que se pueden implementar en [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo de Azure Machine Learning. Para más información, consulte el artículo [Crear un área de trabajo](how-to-manage-workspace.md).
* La[CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un modelo de aprendizaje automático entrenado registrado en el área de trabajo. Si no tiene un modelo, use el [Tutorial: Entrenamiento de modelos de clasificación de imágenes](tutorial-train-models-with-aml.md) para entrenar y registrar uno.

    > [!IMPORTANT]
    > En los fragmentos de código de este artículo se supone que se han establecido las siguientes variables:
    >
    > * `ws`: su área de trabajo de Azure Machine Learning.
    > * `model`: el modelo registrado que se implementará.
    > * `inference_config`: la configuración de inferencia del modelo.
    >
    > Para más información sobre la definición de estas variables, consulte [Implementación de modelos con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparación de la implementación

Antes de realizar la implementación, debe definir qué necesita para ejecutar el modelo como un servicio web. En la lista siguiente se describen los elementos básicos necesarios para una implementación:

* Un __script de entrada__. Este script acepta solicitudes, puntúa la solicitud mediante el modelo y devuelve los resultados.

    > [!IMPORTANT]
    > El script de entrada es específico del modelo; debe comprender el formato de los datos de la solicitud entrante, el formato de los datos que espera el modelo y el formato de los datos que se devuelven a los clientes.
    >
    > Si los datos de la solicitud están en un formato que el modelo no puede usar, el script puede transformarlos a un formato aceptable. También puede transformar la respuesta antes de devolverla al cliente.
    >
    > Cuando se empaqueta para Functions, la entrada se trata de forma predeterminada como texto. Si está interesado en consumir los bytes sin formato de la entrada (por ejemplo, con desencadenadores de blobs), conviene usar [AMLRequest para aceptar datos sin procesar](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Dependencias**, como scripts de asistente o paquetes de Python/Conda, necesarias para ejecutar el modelo o el script de entrada.

Estas entidades se encapsulan en una __configuración de inferencia__. La configuración de inferencia hace referencia al script de entrada y a otras dependencias.

> [!IMPORTANT]
> Al crear una configuración de inferencia para su uso con Azure Functions, debe usar un objeto [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py). En el ejemplo siguiente se muestra cómo crear un objeto de entorno y cómo usarlo con una configuración de inferencia:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Para obtener más información sobre los entornos, consulte el tema sobre la [creación y administración de entornos de entrenamiento e implementación](how-to-use-environments.md).

Para más información sobre la configuración de inferencia, consulte [Implementación de modelos con Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Al realizar la implementación en Functions, no es necesario crear una __configuración de implementación__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalación del paquete de versión preliminar de SDK para la compatibilidad con Functions

Para compilar paquetes para Azure Functions, debe instalar el paquete de versión preliminar de SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Crear la imagen

Para crear la imagen de Docker que se implementa en Azure Functions, use [azureml.comtrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) o la función de paquete específica del desencadenador que le interese usar. El siguiente fragmento de código muestra cómo crear un paquete con un desencadenador de blobs a partir del modelo y la configuración de inferencia:

> [!NOTE]
> En el fragmento de código se da por supuesto que `model` contiene un modelo registrado y que `inference_config` contiene la configuración del entorno de inferencia. Para más información, consulte [Implementación de modelos con Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Si `show_output=True`, se muestra la salida del proceso de compilación de Docker. Una vez finalizado el proceso, la imagen se ha creado en Azure Container Registry para su área de trabajo. Una vez creada la imagen, se muestra la ubicación en Azure Container Registry. La ubicación devuelta tiene el formato `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Actualmente, el empaquetado para Functions admite desencadenadores HTTP, desencadenadores de blobs y desencadenadores de Service Bus. Para más información sobre los desencadenadores, vea [Enlaces de Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Guarde la información de la ubicación, ya que se usa al implementar la imagen.

## <a name="deploy-image-as-a-web-app"></a>Implementación de la imagen como una aplicación web

1. Use el siguiente comando para obtener las credenciales de inicio de sesión del registro de contenedor de Azure que contiene la imagen. Reemplace `<acrinstance>` por el valor devuelto anteriormente de `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    La salida de este comando es similar al siguiente documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Guarde el valor de __username__ y uno de los dos de __passwords__.

1. Si aún no tiene un grupo de recursos o un plan de App Service para implementar el servicio, los siguientes comandos muestran cómo crear ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    En este ejemplo se usa un plan de tarifa _Premium de Linux_ (`--sku EP1`).

    > [!IMPORTANT]
    > Las imágenes que crea Azure Machine Learning usan Linux, por lo que debe usar el parámetro `--is-linux`.

1. Para crear la aplicación de funciones, use el siguiente comando. Reemplace `<app-name>` por el nombre que desea usar. Reemplace `<acrinstance>` y `<imagename>` por los valores de `package.location`devueltos anteriormente:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > Llegado este punto, la aplicación de funciones ya se ha creado, pero como no hemos proporcionado la cadena de conexión del desencadenador de blobs ni las credenciales a la instancia de Azure Container Registry que contiene la imagen, la aplicación de funciones no está activa. En los siguientes pasos, facilitaremos la información de autenticación y de cadena de conexión del registro de contenedor. 

1. Cree la cuenta de almacenamiento que se usará como desencadenador y obtenga la cadena de conexión correspondiente.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Registre esta cadena de conexión para proporcionarla a la aplicación de funciones, ya que la usaremos más adelante cuando se nos pida `<triggerConnectionString>`.

1. Cree los contenedores de la entrada y de la salida en la cuenta de almacenamiento. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Deberemos recuperar la etiqueta asociada al contenedor creado, usando el siguiente comando:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    La etiqueta más reciente que se muestra será `imagetag`.

1. Use el siguiente comando para proporcionar a la aplicación de funciones las credenciales necesarias para acceder al registro de contenedor. Reemplace `<app-name>` por el nombre que desea usar. Reemplace `<acrinstance>` y `<imagetag>` por los valores de la llamada de CLI de Azure del paso anterior. Reemplace `<username>` y `<password>` por la información de inicio de sesión de ACR recuperada anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Este comando devuelve información similar al siguiente documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

En este momento, la aplicación de funciones comienza a cargar la imagen.

> [!IMPORTANT]
> Pueden transcurrir varios minutos hasta que se cargue la imagen. Puede supervisar el progreso en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo configurar la aplicación de Functions en la documentación de [Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-linux-custom-imag).
* Obtenga más información sobre los desencadenadores de Blob Storage en [Enlaces de Azure Blob Storage](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Implementación del modelo en Azure App Service](how-to-deploy-app-service.md).
* [Consumir un modelo de ML que está implementado como un servicio web](how-to-consume-web-service.md)
* [Referencia de API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)