---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186929"
---
## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con el comando [az group create](/cli/azure/group). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Las cuentas de almacenamiento de uso general se crean con el comando [az storage account create](/cli/azure/storage/account). Las cuentas de almacenamiento de uso general se pueden para los cuatro servicios: blobs, archivos, tablas y colas. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificación de las credenciales de la cuenta de almacenamiento

La CLI de Azure necesita las credenciales de la cuenta de almacenamiento para la mayoría de los comandos en este tutorial. Aunque hay varias opciones para hacerlo, una de las formas más sencillas de especificarlas es establecer las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_KEY`.

En primer lugar, muestre las claves de la cuenta de almacenamiento con el comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ahora, establezca las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_KEY`. Puede hacerlo en el shell de Bash utilizando el comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
