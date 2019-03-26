---
title: Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack | Microsoft Docs
description: Guía de introducción a las herramientas de desarrollo de almacenamiento de Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 02/27/2019
ms.topic: conceptual
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 1640e06d2d6eec19d516fb3ddf0e98c579e667a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080801"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introducción a las herramientas de desarrollo de almacenamiento de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Microsoft Azure Stack proporciona un conjunto de servicios de almacenamiento que incluye almacenamiento de blobs, tablas y colas.

Utilice este artículo como guía de introducción a las herramientas de desarrollo de almacenamiento de Azure Stack. Puede encontrar información más detallada y código de ejemplo en los tutoriales correspondientes de Azure Storage.

> [!NOTE]  
> Hay diferencias conocidas entre Azure Storage y almacenamiento de Azure Stack, incluidos los requisitos específicos de cada plataforma. Por ejemplo, hay requisitos de bibliotecas de cliente y de sufijos de puntos de conexión que son específicos de Azure Stack. Para más información, consulte [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de clientes de Azure

En lo que respecta a las bibliotecas de cliente de almacenamiento, debe conocer cuál es la versión compatible con la API REST. También debe especificar el punto de conexión de Azure Stack en el código.

### <a name="1811-update-or-newer-versions"></a>Actualización 1811 o versiones más recientes

| Biblioteca de cliente | Versión compatible de Azure Stack | Vínculo | Especificación de punto de conexión |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Paquete NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0 | archivo app.config |
| Java | 7.0.0 | Paquete Maven:<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0 | Configuración de la cadena de conexión |
| Node.js | 2.8.3 | Vínculo NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Ejecutar: `npm install azure-storage@2.8.3`)<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3 | Declaración de instancia de servicio |
| C++ | 5.2.0 | Paquete NuGet:<br>https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0 | Configuración de la cadena de conexión |
| PHP | 1.2.0 | Versión de GitHub:<br>Comunes: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob<br>Queue:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue<br>Table: https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table<br> <br>Instalación mediante Composer (para obtener más información, [vea los siguientes detalles](#install-php-client-via-composer---current)). | Configuración de la cadena de conexión |
| Python | 1.1.0 | Versión de GitHub:<br>Común:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob<br>Queue:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue | Declaración de instancia de servicio |
| Ruby | 1.0.1 | Paquete de RubyGems:<br>Común:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versión de GitHub:<br>Comunes: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Queue: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Table: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configuración de la cadena de conexión |

#### <a name="install-php-client-via-composer---current"></a>Instalación del cliente PHP a través de compositor: actual

Para instalar a través de Composer: (usaremos el blob como ejemplo).

1. Cree un archivo llamado **composer.json** en la raíz del proyecto con el código siguiente:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Descargue [composer.phar](https://getcomposer.org/composer.phar) en la raíz del proyecto.
3. Ejecute `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Versiones anteriores (actualización de 1802 a 1809)

| Biblioteca de cliente | Versión compatible de Azure Stack | Vínculo | Especificación de punto de conexión |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Paquete NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | archivo app.config |
| Java | 6.1.0 | Paquete Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Configuración de la cadena de conexión |
| Node.js | 2.7.0 | Vínculo NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Ejecutar: `npm install azure-storage@2.7.0`)<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Declaración de instancia de servicio |
| C++ | 3.1.0 | Paquete NuGet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Configuración de la cadena de conexión |
| PHP | 1.0.0 | Versión de GitHub:<br>Comunes: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Queue:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Table: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalación a través de Composer (consulte los detalles a continuación). | Configuración de la cadena de conexión |
| Python | 1.0.0 | Versión de GitHub:<br>Común:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Queue:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Declaración de instancia de servicio |
| Ruby | 1.0.1 | Paquete de RubyGems:<br>Común:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versión de GitHub:<br>Comunes: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Queue: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Table: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configuración de la cadena de conexión |

#### <a name="install-php-client-via-composer---previous"></a>Instalación del cliente PHP a través de compositor: anterior

Para instalar a través de compositor: (usaremos blob como ejemplo).

1. Cree un archivo llamado **composer.json** en la raíz del proyecto con el código siguiente:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Descargue [composer.phar](https://getcomposer.org/composer.phar) en la raíz del proyecto.
3. Ejecute `php composer.phar install`.

## <a name="endpoint-declaration"></a>Declaración de punto de conexión

Un punto de conexión de Azure Stack incluye dos partes: el nombre de una región y el dominio de Azure Stack.
En el Kit de desarrollo de Azure Stack, el punto de conexión predeterminado es **local.azurestack.external**.
Si no está seguro de cuál es su punto de conexión, póngase en contacto con el administrador de la nube.

## <a name="examples"></a>Ejemplos

### <a name="net"></a>.NET

Para Azure Stack, el sufijo del punto de conexión se especifica en el archivo app.config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para Azure Stack, el sufijo del punto de conexión se especifica en la instancia de la declaración:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para Azure Stack, el sufijo del punto de conexión se especifica en la instancia de la declaración:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Almacenamiento de blobs

Los siguientes tutoriales de Azure Blob Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción al Almacenamiento de blobs de Azure mediante .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Uso de Blob Storage en Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Uso de almacenamiento de blobs en Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Uso del almacenamiento de blobs en C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Uso del almacenamiento de blobs de PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Uso de Azure Blob Storage desde Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Uso de Blob Storage en Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Los siguientes tutoriales de Azure Queue Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción al Almacenamiento en cola de Azure mediante .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Uso del almacenamiento de colas de Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Uso del almacenamiento de colas de Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Uso del almacenamiento de colas en C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Uso del almacenamiento de colas de PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Uso del almacenamiento de colas de Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Uso del almacenamiento de colas de Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Almacenamiento de tablas

Los siguientes tutoriales de Azure Table Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción al Almacenamiento de tablas de Azure mediante .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Uso de Table Storage en Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Uso de Azure Table Storage en Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Uso de Table Storage desde C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Uso del almacenamiento de tablas de PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Uso de Table Storage en Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Uso del almacenamiento de tablas de Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Microsoft Azure Storage](../../storage/common/storage-introduction.md)
