---
title: Montaje de un volumen de Azure Files en Azure Container Instances
description: Obtenga información sobre cómo montar un volumen de Azure Files para conservar el estado con Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564028"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montaje de un recurso compartido de archivos de Azure en Azure Container Instances

De forma predeterminada, Azure Container Instances no tiene estado. Si el contenedor se bloquea o se detiene, se pierde todo su estado. Para conservar el estado más allá de la duración del contenedor, debe montar un volumen desde un almacén externo. En este artículo se explica cómo montar un recurso compartido de archivos de Azure con [Azure Files](../storage/files/storage-files-introduction.md) para su uso con Azure Container Instances. Azure Files ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante el protocolo de bloque de mensajes del servidor (SMB) estándar. El uso de un recurso compartido de archivos de Azure con Azure Container Instances ofrece características de uso compartido de archivos similares al uso de un recurso compartido de archivo de Azure con Azure Virtual Machines.

> [!NOTE]
> El montaje de un recurso compartido de Azure Files está actualmente restringido a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure en Azure Container Instances, debe crearlo. Ejecute el script siguiente para crear una cuenta de almacenamiento para hospedar el recurso compartido de archivos y el propio recurso compartido. El nombre de la cuenta de almacenamiento debe ser único globalmente, por lo que el script agrega un valor aleatorio en la cadena base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obtención de las credenciales de almacenamiento

Para montar un recurso compartido de archivos de Azure como un volumen en Azure Container Instances, necesita tres valores: el nombre de la cuenta de almacenamiento, el nombre del recurso compartido y la clave de acceso de almacenamiento.

Si ha usado el script anterior, el nombre de la cuenta de almacenamiento se almacena en la variable $ACI_PERS_STORAGE_ACCOUNT_NAME. Para ver el nombre de la cuenta, escriba:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

El nombre del recurso compartido ya se conoce (es *acishare* en el script anterior), por lo tanto, solo queda la clave de cuenta de almacenamiento, que se encuentra con el siguiente comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Implementación de un contenedor y montaje del volumen

Para montar un recurso compartido de archivos de Azure como volumen en un contenedor, especifique el recurso compartido y el punto de montaje del volumen al crear el contenedor con [az container create][az-container-create]. Si ha seguido los pasos anteriores, puede montar el recurso compartido que creó anteriormente mediante el comando siguiente para crear un contenedor:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

El valor `--dns-name-label` debe ser único dentro de la región de Azure en la que crea la instancia de contenedor. Actualice el valor del comando anterior si recibe un mensaje de error de **etiqueta de nombre DNS** al ejecutar el comando.

## <a name="manage-files-in-mounted-volume"></a>Administración de archivos en el volumen montado

Una vez que se inicia el contenedor, puede usar la aplicación web simple implementada a través de la Microsoft [aci-hellofiles] [ aci-hellofiles] imagen para crear archivos de texto pequeño en el recurso compartido de archivos de Azure en la ruta de montaje que especificó. Obtenga el nombre de dominio completo (FQDN) de la aplicación web con el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Puede usar [Azure Portal][portal] o una herramienta como el [Explorador de Microsoft Azure Storage][storage-explorer] para recuperar e inspeccionar el archivo escrito en el recurso compartido de archivos.

## <a name="mount-multiple-volumes"></a>Montaje de varios volúmenes

Para montar varios volúmenes en una instancia del contenedor, debe realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) o un archivo YAML.

Para usar una plantilla, proporcione los detalles del recurso compartido y defina los volúmenes rellenando la matriz `volumes` en la sección `properties` de la plantilla. Por ejemplo, si ha creado dos recursos compartidos de archivos de Azure llamados *share1* y *share2* en la cuenta de almacenamiento *myStorageAccount*, la matriz `volumes` sería similar a la siguiente:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

A continuación, para cada contenedor del grupo de contenedores en el que desea montar los volúmenes, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor. Por ejemplo, esto monta los dos volúmenes, *myvolume1* y *myvolume2* definidos previamente:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, vea [Implementación de un grupo de contenedores](container-instances-multi-container-group.md). Para obtener un ejemplo con un archivo YAML, vea [Implementación de un grupo de contenedores con varios contenedores mediante YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un volumen de emptyDir en Azure Container Instances](container-instances-volume-emptydir.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montaje de un volumen secreto en Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show