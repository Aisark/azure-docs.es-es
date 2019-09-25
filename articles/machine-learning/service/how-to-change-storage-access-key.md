---
title: Cambio de las claves de acceso de la cuenta de almacenamiento
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo cambiar las claves de acceso de la cuenta de Azure Storage que usa el área de trabajo. Azure Machine Learning usa una cuenta de Azure Storage para almacenar los datos y los modelos. Al regenerar la clave de acceso de la cuenta de almacenamiento, debe actualizar Azure Machine Learning para que use las claves nuevas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/16/2019
ms.openlocfilehash: 6c87d4553c7b0fd34513d761558a06cd527c4e3b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034968"
---
# <a name="regenerate-storage-account-access-keys"></a>Regeneración de las claves de acceso de la cuenta de almacenamiento

Obtenga información sobre cómo cambiar las claves de acceso de las cuentas de Azure Storage que usa Azure Machine Learning. Azure Machine Learning puede usar cuentas de almacenamiento para almacenar datos o modelos entrenados.

Por motivos de seguridad, es posible que necesite cambiar las claves de acceso de una cuenta de Azure Storage. Cuando se regenera la clave de acceso, es necesario actualizar Azure Machine Learning para que use la nueva clave. Azure Machine Learning podría estar usando la cuenta de almacenamiento para el almacenamiento de modelos y como almacén de datos.

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo de Azure Machine Learning. Para más información, consulte el artículo [Crear un área de trabajo](how-to-manage-workspace.md).

* El [SDK de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* La [extensión de la CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>¿Qué se debe actualizar?

Las cuentas de almacenamiento pueden usarse como un espacio del área de trabajo de Azure Machine Learning para almacenar registros, modelos, instantáneas, etc. y como almacén de datos. El proceso para actualizar el área de trabajo implica un solo comando de la CLI de Azure y se puede ejecutar después de actualizar la clave de almacenamiento. El proceso de actualización de los almacenes de datos es más complicado. Conlleva detectar qué almacenes de datos usan actualmente la cuenta de almacenamiento y, después, volver a registrarlos.

> [!IMPORTANT]
> Actualice al mismo tiempo el área de trabajo con la CLI de Azure y los almacenes de datos con Python. No basta con actualizar solo uno de estos dos elementos; de hecho, podrían producirse errores mientras no actualice ambos.

Para detectar qué cuentas de almacenamiento usan los almacenes de datos, use el código siguiente:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Este código busca los almacenes de datos registrados que usa Azure Storage y muestra la información siguiente:

* Nombre del almacén de datos: nombre del almacén de datos con el que está registrada la cuenta de almacenamiento.
* Nombre de la cuenta de almacenamiento: nombre de la cuenta de Azure Storage.
* Contenedor: contenedor de la cuenta de almacenamiento usado por este registro.

También indica si el almacén de datos es para un blob de Azure o un recurso compartido de archivos de Azure, ya que hay distintos métodos para volver a registrar cada tipo de almacén de datos.

Si existe una entrada para la cuenta de almacenamiento cuyas claves de acceso planea regenerar, guarde el nombre del almacén de datos, de la cuenta de almacenamiento y del contenedor.

## <a name="update-the-access-key"></a>Actualización de la clave de acceso

Para actualizar Azure Machine Learning de modo que use la clave nueva, siga estos pasos:

> [!IMPORTANT]
> Lleve a cabo todos los pasos, sin olvidarse de actualizar el área de trabajo con la CLI y los almacenes de datos con Python. Si solo actualiza uno de estos dos elementos, podrían producirse errores mientras no actualice ambos.

1. Regenere la clave. Para obtener información sobre cómo regenerar una clave de acceso, consulte el artículo [Administración de la configuración de cuentas de almacenamiento](/azure/storage/common/storage-account-manage#access-keys). Guarde la clave nueva.

1. Para actualizar el área de trabajo de modo que use la clave nueva, siga estos pasos:

    1. Para iniciar sesión en la suscripción de Azure que contiene el área de trabajo, use el siguiente comando de la CLI de Azure:

        ```azurecli-interactive
        az login
        ```

    1. Para actualizar el área de trabajo de modo que use la clave nueva, use el comando siguiente. Reemplace `myworkspace` por el nombre del área de trabajo de Azure Machine Learning y reemplace `myresourcegroup` por el nombre del grupo de recursos de Azure que contiene el área de trabajo.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        Este comando sincroniza automáticamente las nuevas claves de la cuenta de Azure Storage que usa el área de trabajo.

1. Para volver a registrar los almacenes de datos que usan la cuenta de almacenamiento, use los valores de la sección [¿Qué se debe actualizar?](#whattoupdate) y la clave del paso 1 con el código siguiente:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Puesto que se ha especificado `overwrite=True`, este código sobrescribe el registro existente y lo actualiza para que use la clave nueva.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo registrar almacenes de datos, vea la referencia de la clase [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py).
