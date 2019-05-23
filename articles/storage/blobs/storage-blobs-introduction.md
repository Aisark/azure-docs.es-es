---
title: 'Introducción a Blob Storage: almacenamiento de objetos en Azure'
description: Azure Blob Storage almacena grandes cantidades de datos de objetos no estructurados, como datos de texto o binarios. Azure Blob Storage ofrece alta escalabilidad y disponibilidad. Los clientes pueden acceder a objetos de Blob Storage desde PowerShell o la CLI de Azure, mediante programación con las bibliotecas cliente de Azure Storage o mediante REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 01/03/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 498dd3be75cb27263c811d9db541b2e040599156
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788023"
---
# <a name="introduction-to-azure-blob-storage"></a>Introducción a Azure Blob Storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Recursos de Blob Storage

Blob Storage ofrece tres tipos de recursos:

- La **cuenta de almacenamiento**. 
- Un **contenedor** en la cuenta de almacenamiento
- Un **blob** en un contenedor 

En el siguiente diagrama se muestra la relación entre estos recursos.

![Diagrama de arquitectura de Blob Storage](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Cuentas de almacenamiento

Una cuenta de almacenamiento le proporciona un espacio de nombres único en Azure para sus datos. Cada objeto que almacena en Azure Storage tiene una dirección que incluye su nombre de cuenta único. La combinación del nombre de la cuenta y el punto de conexión del blob de Azure Storage constituye la dirección base de los objetos de la cuenta de almacenamiento.

Por ejemplo, si la cuenta de almacenamiento se llama *mystorageaccount*, los puntos de conexión predeterminados para Blob Storage son:

```
http://mystorageaccount.blob.core.windows.net 
```

Para crear una cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../common/storage-quickstart-create-account.md). Para más información sobre las cuentas de almacenamiento, consulte la [Información general acerca de la cuenta de Azure Storage](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Contenedores

Un contenedor organiza un conjunto de blobs, de forma parecida a un directorio en un sistema de archivos. Una cuenta de almacenamiento puede contener un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs. 

  > [!NOTE]
  > El nombre del contenedor debe estar en minúsculas. Para más información sobre la asignación de nombre a los contenedores, consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Asignación de nombres y referencia a contenedores, blobs y metadatos).

### <a name="blobs"></a>Blobs
 
Azure Storage admite tres tipos de blobs:

* Los **blobs en bloques** almacenan texto y datos binarios, hasta unos 4,7 TB. Los blobs en bloques se componen de bloques de datos que se pueden administrar de forma individual.
* Los **blobs en anexos** constan de bloques, como los blobs en bloques, pero están optimizados para operaciones de anexión. Los blobs en anexos resultan muy convenientes para escenarios como el registro de datos de máquinas virtuales.
* Los **blobs en páginas** almacenan archivos de acceso aleatorio con un tamaño de hasta 8 TB. Los blobs en páginas almacenan los archivos del disco duro virtual (VHD) que sirven como discos para las máquinas virtuales de Azure. Para más información sobre los blobs en páginas, consulte la [Introducción a los blobs en páginas de Azure](storage-blob-pageblob-overview.md)

Para más información sobre los distintos tipos de blobs, consulte [Understanding Block Blobs, Append Blobs, and Page Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Descripción de los blobs en bloques, en anexos y en páginas).

## <a name="move-data-to-blob-storage"></a>Movimiento de datos a Blob Storage

Existen varias soluciones para migrar datos existentes a Blob Storage:

- **AzCopy** es una herramienta de línea de comandos fácil de usar para Windows y Linux que realiza operaciones de copia con Blob Storage en ambas direcciones, entre contenedores o entre cuentas de almacenamiento. Para más información sobre AzCopy, consulte [Transferencia de datos con AzCopy v10 (versión preliminar)](../common/storage-use-azcopy-v10.md). 
- La **Biblioteca de movimiento de datos de Azure Storage** es una biblioteca de .NET para mover datos entre los servicios de Azure Storage. La utilidad AzCopy está creada con la Biblioteca de movimiento de datos. Para más información, consulte la [documentación de referencia](/dotnet/api/microsoft.azure.storage.datamovement) de la Biblioteca de movimiento de datos. 
- **Azure Data Factory** admite operaciones de copia de datos con Blob Storage en ambas direcciones mediante el uso de una clave de cuenta, una firma de acceso compartido, una entidad de servicio o identidades administradas paras las autenticaciones de recursos de Azure. Para más información, consulte [Copia de datos con Azure Blob Storage como origen o destino mediante Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** es un controlador de sistema de archivos virtual para Azure Blob Storage. Puede usar blobfuse para acceder a los datos de blob de bloque existentes en la cuenta de Storage a través del sistema de archivos de Linux. Para más información, consulte [Cómo montar el almacenamiento de blobs como sistema de archivos con blobfuse](storage-how-to-mount-container-linux.md).
- **Azure Data Box Disk** es un servicio para transferir datos locales al almacenamiento de blobs cuando los grandes conjuntos de datos o las restricciones de red hacen que la carga de datos a través del cable no sea realista. Puede usar [discos de Azure Data Box](../../databox/data-box-disk-overview.md) para solicitar discos de estado sólido (SSD) a Microsoft. A continuación, puede copiar los datos en esos discos y enviarlos de vuelta a Microsoft para su carga en Blob Storage.
- El **servicio Azure Import/Export** proporciona una forma de exportar grandes cantidades de datos de la cuenta de almacenamiento a unidades de discos duros que usted proporciona y que Microsoft después le devuelve con los datos. Para más información, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blog Storage](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Pasos siguientes

* [Cree una cuenta de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Objetivos de escalabilidad y rendimiento de Azure Storage](../common/storage-scalability-targets.md)
