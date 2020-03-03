---
title: 'Inicio rápido: Creación de un blob con la CLI de Azure'
titleSuffix: Azure Storage
description: En esta guía de inicio rápido, aprenderá a usar la CLI de Azure para cargar un blob en Azure Storage, descargar un blob o enumerar los blobs de un contenedor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 57ab56fe3028da9011e86c589209e7505e69e719
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650937"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Inicio rápido: Creación, descarga y enumeración de blobs mediante la CLI de Azure

La CLI de Azure es la forma de usar la línea de comandos de Azure para administrar los recursos de Azure. Puede utilizarlo en el explorador con Azure Cloud Shell. También puede instalarla en macOS, Linux o Windows y ejecutarla desde la línea de comandos. En esta guía de inicio rápido aprenderá a usar la CLI de Azure para cargar y descargar datos en Azure Blob Storage y desde este.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de Azure localmente, para este inicio rápido es preciso ejecutar la CLI de Azure, versión 2.0.46 o posterior. Ejecute `az --version` para determinar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

Si ejecuta la CLI de Azure localmente, tiene que iniciar sesión y autenticarse. Este paso no es necesario si usa Azure Cloud Shell. Para iniciar sesión en la CLI de Azure, ejecute `az login` y autentíquese en la ventana del explorador:

```azurecli
az login
```

## <a name="authorize-access-to-blob-storage"></a>Autorización del acceso al almacenamiento de blobs

Puede autorizar el acceso al almacenamiento de blobs desde la CLI de Azure con las credenciales de Azure AD o con la clave de acceso de la cuenta de almacenamiento. Se recomienda usar las credenciales de Azure AD. En este artículo se muestra cómo autorizar las operaciones de almacenamiento de blobs mediante Azure AD.

Los comandos de la CLI de Azure para operaciones de datos en el almacenamiento de blobs admiten el parámetro `--auth-mode`, que permite especificar cómo se autoriza una operación determinada. Establezca el parámetro `--auth-mode` en `login` para autorizar con las credenciales de Azure AD. Para más información consulte [Ejecución de comandos de la CLI de Azure con credenciales de Azure AD para acceder a datos de blob o de cola](../common/authorize-active-directory-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Solo las operaciones de datos de almacenamiento de blobs admiten el parámetro `--auth-mode`. Las operaciones de administración, como la creación de un grupo de recursos o una cuenta de almacenamiento, usan automáticamente las credenciales de Azure AD para la autorización.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con el comando [az group create](/cli/azure/group). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Las cuentas de almacenamiento de uso general se crean con el comando [az storage account create](/cli/azure/storage/account). Las cuentas de almacenamiento de uso general se pueden para los cuatro servicios: blobs, archivos, tablas y colas.

No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan en un contenedor. Puede organizar los grupos de blobs en contenedores de una forma similar a la que organiza los archivos en carpetas en el equipo.

Cree un contenedor para almacenar blobs con el comando [az storage container create](/cli/azure/storage/container). No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Carga de un blob

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los ejemplos en esta guía de inicio rápido muestran cómo trabajar con blobs en bloques.

En primer lugar, cree un archivo para cargarlo en un blob en bloques. Si está utilizando Azure Cloud Shell, use el siguiente comando para crear un archivo:

```bash
vi helloworld
```

Cuando se abra el archivo, presione **Insertar**. Escriba *Hello World* y, a continuación, presione **Esc**. Después, escriba *:x* y, a continuación, presione **Entrar**.

En este ejemplo, se carga un blob en el contenedor que se creó en el último paso con el comando [az storage blob upload](/cli/azure/storage/blob). No es necesario especificar una ruta de acceso de archivo, ya que el archivo se creó en el directorio raíz. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Esta operación crea el blob si todavía no existe y lo sobrescribe si ya existe. Cargue tantos archivos como desee antes de continuar.

Para cargar varios archivos al mismo tiempo, puede usar el comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs del contenedor con el comando [az storage blob list](/cli/azure/storage/blob). No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Descarga de un blob

Use el comando [az storage blob download](/cli/azure/storage/blob) para descargar el blob que cargó anteriormente. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transferencia de datos con AzCopy

La utilidad de línea de comandos AzCopy ofrece transferencia de datos de alto rendimiento mediante scripts para Azure Storage. Puede utilizar AzCopy para transferir datos a y desde Blob Storage y Azure Files. Para más información sobre AzCopy v10, la versión más reciente de AzCopy, consulte [Introducción a AzCopy](../common/storage-use-azcopy-v10.md). Para más información acerca del uso de AzCopy v10 con el almacenamiento de blobs, consulte [Transferencia de datos con AzCopy y Blob Storage](../common/storage-use-azcopy-blobs.md).

En el ejemplo siguiente se usa AzCopy para cargar un archivo local en un blob. No olvide reemplazar los valores de ejemplo por sus propios valores:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida la cuenta de almacenamiento que creó en esta guía de inicio rápido, elimine el grupo de recursos con el comando [az group delete](/cli/azure/group). No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, aprendió a transferir archivos entre un sistema de archivos local y un contenedor en Azure Blob Storage. Para más información sobre cómo trabajar con blobs en Azure Storage, continúe con el tutorial para trabajar con Azure Blob Storage.

> [!div class="nextstepaction"]
> [Cómo: Operaciones de Blob Storage con la CLI de Azure](storage-how-to-use-blobs-cli.md)
