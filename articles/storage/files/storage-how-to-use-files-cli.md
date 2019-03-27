---
title: Guía de inicio rápido para administrar recursos compartidos de archivos de Azure mediante la CLI de Azure
description: Use esta guía de inicio rápido para aprender a usar la CLI de Azure para administrar Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: f18b2cbf31b50b27c1ae8a6d4fa4a6510781cb12
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886503"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Inicio rápido: Creación y administración de recursos compartidos de archivos de Azure mediante la CLI de Azure
En esta guía se describen los conceptos básicos sobre cómo trabajar con [recursos compartidos de archivos de Azure](storage-files-introduction.md) mediante la CLI de Azure. Los recursos compartidos de archivos de Azure son iguales a otros recursos compartidos de archivos, pero se almacenan en la nube y están respaldados por la plataforma Azure. Los recursos compartidos de archivos de Azure admiten el protocolo SMB estándar del sector y permiten el uso compartido entre varias máquinas, aplicaciones e instancias. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de Azure localmente, para los pasos de este artículo debe ejecutar la CLI de Azure versión 2.0.4 o versiones posteriores. Ejecute **az --version** para buscar la versión de la CLI de Azure. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

De forma predeterminada, los comandos de la CLI de Azure devuelven formato JSON (notación de objetos JavaScript). JSON es el método estándar para enviar y recibir mensajes desde las API REST. Para facilitar el trabajo con las respuestas JSON, algunos de los ejemplos de este artículo usan el parámetro *query* en los comandos de la CLI de Azure. Este parámetro utiliza el [lenguaje de consulta JMESPath](http://jmespath.org/) para el análisis de datos JSON. Para aprender más acerca de cómo usar los resultados de los comandos de la CLI de Azure mediante el lenguaje de consulta JMESPath, siga las indicaciones del [tutorial de JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Si usa la CLI de Azure localmente, abra un símbolo del sistema e inicie sesión en Azure si aún no lo ha hecho.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Si aún no tiene un grupo de recursos de Azure, puede utilizar el comando [az group create](/cli/azure/group) para crear uno nuevo. 

En el ejemplo siguiente se crea un grupo de recursos llamado *myResourceGroup* en la ubicación *Este de EE. UU.*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Una cuenta de almacenamiento es un grupo compartido de almacenamiento en el que puede implementar recursos compartidos de archivos de Azure u otros recursos de almacenamiento como blobs o colas. Una cuenta de almacenamiento puede contener un número ilimitado de recursos compartidos de archivos. Un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento.

En el ejemplo siguiente se crea una cuenta de almacenamiento llamada *mystorageaccount\<número aleatorio\>* con el comando [az storage account create](/cli/azure/storage/account) y, a continuación, se pasa el nombre de esa cuenta de almacenamiento en la variable `$STORAGEACCT`. Los nombres de cuenta de almacenamiento deben ser únicos, de modo que asegúrese de reemplazar "mystorageacct" por un nombre único.

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Obtención de la clave de la cuenta de almacenamiento
Las claves de cuenta de almacenamiento controlan el acceso a los recursos de una cuenta de almacenamiento. Las claves se crean automáticamente al crear una cuenta de almacenamiento. Puede obtener las claves de cuenta de almacenamiento con el comando [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure
Ahora puede crear su primer recurso compartido de archivos de Azure. Puede crear recursos compartidos de archivos con el comando [az storage share create](/cli/azure/storage/share). Este ejemplo crea un recurso compartido de archivos de Azure llamado *myshare*: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Los nombres de los recursos compartidos solo pueden contener letras minúsculas, números y guiones sencillos (pero no pueden empezar con un guion). Para obtener detalles completos sobre cómo asignar un nombre a los recursos compartidos y los archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Uso de un recurso compartido de archivos de Azure
Azure Files proporciona dos métodos para trabajar con archivos y carpetas dentro de un recurso compartido de archivos de Azure: el [protocolo de bloque de mensajes del servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) estándar del sector y el [REST de archivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar un recurso compartido de archivos con SMB, consulte el siguiente documento según su sistema operativo:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Uso de un recurso compartido de archivos de Azure con el protocolo File REST 
Se puede trabajar directamente con el protocolo REST de archivo (escribir a mano las llamadas HTTP de REST), pero la manera más habitual de usar este protocolo es con la CLI de Azure, el [módulo de Azure PowerShell](storage-how-to-use-files-powershell.md) o un SDK de Azure Storage; todos ellos proporcionan un buen contenedor para el protocolo REST de archivo en el lenguaje de programación o script de su elección.  

Es esperable que en la mayoría de casos de uso de Azure Files se trabajará con el recurso compartido de archivos de Azure a través del protocolo SMB, dado que permite usar las aplicaciones y herramientas existentes que se desean usar; sin embargo, existen varias razones por las que es beneficioso usar la API REST de archivo en lugar de SMB, como por ejemplo:

- Va a examinar el recurso compartido de archivos desde Cloud Shell de Bash de Azure (que no puede montar recursos compartidos de archivos a través de SMB).
- Debe ejecutar un script o una aplicación desde un cliente que no puede montar recursos compartidos de SMB, como los clientes locales que no tienen desbloqueado el puerto 445.
- Se quieren aprovechar las ventajas de los recursos sin servidor, como [Azure Functions](../../azure-functions/functions-overview.md). 

En los ejemplos siguientes se muestra cómo usar la CLI de Azure para manipular el recurso compartido de archivos de Azure con el protocolo REST de archivo. 

### <a name="create-a-directory"></a>Creación de directorios
Para crear un nuevo directorio llamado *myDirectory* en la raíz del recurso compartido de archivos de Azure, use el comando [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Cargar un archivo
Para demostrar cómo cargar un archivo mediante el comando [`az storage file upload`](/cli/azure/storage/file), primero cree un archivo para cargarlo en la unidad temporal de Cloud Shell. En el ejemplo siguiente, se crea y se carga el archivo:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Si está ejecutando la CLI de Azure localmente, sustituya `~/clouddrive` por una ruta de acceso que exista en la máquina.

Después de cargar el archivo, puede usar el comando [`az storage file list`](/cli/azure/storage/file) para asegurarse de que el archivo se cargó en el recurso compartido de archivos de Azure:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Descarga de un archivo
Puede usar el comando [`az storage file download`](/cli/azure/storage/file) para descargar una copia del archivo que cargó en la unidad temporal de Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copiar archivos
Copiar archivos de un recurso compartido de archivos a otro recurso compartido de archivos o desde un contenedor de Azure Blob Storage son tareas comunes. Para demostrar esta funcionalidad, cree un nuevo recurso compartido. Copie el archivo que ha cargado en este nuevo recurso compartido mediante el comando [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Ahora, si enumera los archivos del nuevo recurso compartido, debería ver el archivo copiado:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Aunque el comando `az storage file copy start` es práctico para los movimientos de archivos entre recursos compartidos de archivos de Azure y contenedores de Azure Blob Storage, se recomienda usar AzCopy para movimientos mayores. (Mayores en términos de número o tamaño de los archivos que se van a mover). Más información acerca de [AzCopy para Linux](../common/storage-use-azcopy-linux.md) y [AzCopy para Windows](../common/storage-use-azcopy.md). AzCopy debe estar instalado localmente. AzCopy no está disponible en Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Creación y administración de instantáneas de recurso compartido
Otra tarea útil que puede hacer con un recurso compartido de archivos de Azure consiste en crear instantáneas del recurso compartido. Una instantánea conserva una copia de un momento dado de un recurso compartido de archivos de Azure. Las instantáneas del recurso compartido son similares a algunas tecnologías de sistemas operativos con las que es posible que ya esté familiarizado:

- Instantáneas del [Administrador de volúmenes lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantáneas de [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS
- [Servicio de instantáneas de volumen (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de archivos Windows, como NTFS y ReFS. Puede crear una instantánea de recurso compartido mediante el comando [`az storage share snapshot`](/cli/azure/storage/share):

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Examen del contenido de la instantánea del recurso compartido
Para examinar el contenido de una instantánea del recurso compartido, pase la marca de tiempo de la instantánea del recurso compartido que ha capturado en la variable `$SNAPSHOT` al comando `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido
Puede ver la lista de instantáneas que ha realizado para el recurso compartido con el siguiente comando:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido
Puede restaurar un archivo con el comando `az storage file copy start` que ha utilizado antes. En primer lugar, elimine el archivo SampleUpload.txt que cargó, de forma que pueda restaurarlo desde la instantánea:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Para eliminar una instantánea del recurso compartido, utilice el comando [`az storage share delete`](/cli/azure/storage/share). Use la variable que contiene la referencia `$SNAPSHOT` al parámetro `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando haya acabado, puede usar el comando [`az group delete`](/cli/azure/group) para eliminar el grupo de recursos y todos los recursos relacionados: 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Como alternativa, puede eliminar los recursos individualmente.
- Para eliminar los recursos compartidos de archivos de Azure que ha creado en este artículo:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Para eliminar la propia cuenta de almacenamiento. (Esto elimina implícitamente los recursos compartidos de archivos de Azure que ha creado y cualquier otro recurso de almacenamiento que pueda haber creado, por ejemplo, un contenedor de Azure Blob Storage).

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué es Azure Files?](storage-files-introduction.md)
