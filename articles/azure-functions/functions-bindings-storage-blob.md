---
title: Enlaces de Azure Blob Storage para Azure Functions
description: Descubra cómo utilizar desencadenadores y enlaces de Azure Blob Storage en Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 3e67737e26edfee94a5a4d740d6c575817c66ff0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766182"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Enlaces de Azure Blob Storage para Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Blob Storage en Azure Functions. Azure Functions admite enlaces de desencadenador, entrada y salida para blobs. El artículo incluye una sección para cada enlace:

* [Desencadenador de blobs](#trigger)
* [Enlace de entrada de blob](#input)
* [Enlace de salida de blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Utilice el desencadenador de Event Grid en lugar del desencadenador de Blob Storage para las cuentas de almacenamiento solo para blobs, para operaciones a gran escala o para reducir la latencia. Para obtener más información, consulte la sección [Desencadenador](#trigger).

## <a name="packages---functions-1x"></a>Paquetes: Functions 1.x

Los enlaces de Blob Storage se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versión 2.x. El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) de GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paquetes: Functions 2.x

Los enlaces de almacenamiento de Blob se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versión 3.x. El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) de GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Desencadenador

El desencadenador de Blob Storage inicia una función cuando se detecta un blob nuevo o actualizado. El contenido del blob se proporciona a modo de entrada para la función.

El [desencadenador de Event Grid](functions-bindings-event-grid.md) tiene compatibilidad integrada para [eventos de blob](../storage/blobs/storage-blob-event-overview.md) y también puede utilizarse para iniciar una función cuando se detecta un blob nuevo o actualizado. Para obtener un ejemplo, consulte el tutorial [Cambio de tamaño de una imagen con Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

Utilice Event Grid en lugar del desencadenador de Blob Storage en los escenarios siguientes:

* Cuentas de Almacenamiento de blobs
* Gran escala
* Minimización de la latencia

### <a name="blob-storage-accounts"></a>Cuentas de Almacenamiento de blobs

Las [cuentas de Blob Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) se admiten para enlaces de entrada y salida de blobs, pero no para desencadenadores de blobs. Los desencadenadores de Blob Storage necesitan una cuenta de almacenamiento de uso general.

### <a name="high-scale"></a>Gran escala

Gran escala se aplica generalmente a contenedores que tienen más de 100 000 blobs en ellos o a cuentas de almacenamiento que tienen más de 100 actualizaciones de blob por segundo.

### <a name="latency-issues"></a>Problemas de latencia

Si la aplicación de función está en un plan de consumo, puede haber un retraso de hasta 10 minutos en el procesamiento de nuevos blobs si una aplicación de función ha quedado inactiva. Para evitar esta latencia, puede cambiar a un plan de App Service con Always On habilitado. También puede usar un [desencadenador de Event Grid](functions-bindings-event-grid.md) con su cuenta de almacenamiento de blobs. Para ver un ejemplo, consulte el [tutorial de Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Desencadenador de Queue Storage

Además de Event Grid, otra alternativa para el procesamiento de blobs es el desencadenador de Queue Storage, pero no tiene ninguna compatibilidad integrada para los eventos de blobs. Debería crear mensajes en cola al crear o actualizar blobs. Para ver un ejemplo que presupone que ya ha realizado eso, consulte el [ejemplo de enlaces de entrada de blob más adelante en este artículo](#input---example).

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que escribe un registro cuando se agrega o se actualiza un blob en el contenedor `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La cadena `{name}` en la ruta de acceso del desencadenador de blobs `samples-workitems/{name}` crea una [expresión de enlace](./functions-bindings-expressions-patterns.md) que puede usar en el código de función para acceder al nombre de archivo del blob de desencadenamiento. Para más información, consulte [Patrones de nombre de blob](#trigger---blob-name-patterns) que aparece más adelante en este artículo.

Para obtener más información sobre el atributo `BlobTrigger`, consulte [Desencadenador: atributos](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de blobs en un archivo *function.json* y el [código de Python](functions-reference-python.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el [contenedor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La cadena `{name}` en la ruta de acceso del desencadenador de blobs `samples-workitems/{name}` crea una [expresión de enlace](./functions-bindings-expressions-patterns.md) que puede usar en el código de función para acceder al nombre de archivo del blob de desencadenamiento. Para más información, consulte [Patrones de nombre de blob](#trigger---blob-name-patterns) que aparece más adelante en este artículo.

Para más información sobre las propiedades del archivo *function.json*, consulte la sección [Configuración](#trigger---configuration) donde se explican estas propiedades.

Este es el código de script de C# que se enlaza a `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Este es el código de script de C# que se enlaza a `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de blob en un archivo *function.json* y el [código de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el contenedor `samples-workitems`.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La cadena `{name}` en la ruta de acceso del desencadenador de blobs `samples-workitems/{name}` crea una [expresión de enlace](./functions-bindings-expressions-patterns.md) que puede usar en el código de función para acceder al nombre de archivo del blob de desencadenamiento. Para más información, consulte [Patrones de nombre de blob](#trigger---blob-name-patterns) que aparece más adelante en este artículo.

Para más información sobre las propiedades del archivo *function.json*, consulte la sección [Configuración](#trigger---configuration) donde se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Ejemplo de desencadenador de Python

En el ejemplo siguiente se muestra un enlace de desencadenador de blobs en un archivo *function.json* y el [código de Python](functions-reference-python.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el [contenedor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Este es el archivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La cadena `{name}` en la ruta de acceso del desencadenador de blobs `samples-workitems/{name}` crea una [expresión de enlace](./functions-bindings-expressions-patterns.md) que puede usar en el código de función para acceder al nombre de archivo del blob de desencadenamiento. Para más información, consulte [Patrones de nombre de blob](#trigger---blob-name-patterns) que aparece más adelante en este artículo.

Para más información sobre las propiedades del archivo *function.json*, consulte la sección [Configuración](#trigger---configuration) donde se explican estas propiedades.

Este es el código de Python:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Desencadenador: ejemplo de Java

En el ejemplo siguiente se muestra un enlace de desencadenador de blob en un archivo *function.json* y el [código de Java](functions-reference-java.md) que usa el enlace. La función escribe un registro cuando se agrega o actualiza un blob en el contenedor `myblob`.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Este es el código de Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Desencadenador: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice los siguientes atributos para configurar un desencadenador de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  El constructor del atributo toma una cadena de ruta de acceso que indica al contenedor que inspeccione y, opcionalmente, un [patrón de nombre de blob](#trigger---blob-name-patterns). Este es un ejemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Proporciona otra manera de especificar la cuenta de almacenamiento que se debe usar. El constructor toma el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. El atributo se puede aplicar en el nivel de clase, método o parámetro. En el ejemplo siguiente se muestran el nivel de clase y de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

La cuenta de almacenamiento que se debe usar se determina en el orden siguiente:

* La propiedad `Connection` del atributo `BlobTrigger`.
* El atributo `StorageAccount` aplicado al mismo parámetro que el atributo `BlobTrigger`.
* El atributo `StorageAccount` aplicado a la función.
* El atributo `StorageAccount` aplicado a la clase.
* La cuenta de almacenamiento predeterminada de la aplicación de función (configuración de aplicación "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `BlobTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blobTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. Las excepciones se indican en la sección [uso](#trigger---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función. |
|**path** | **BlobPath** |El [contenedor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) que se va a supervisar.  Puede ser un [patrón de nombre de blob](#trigger---blob-name-patterns). |
|**conexión** | **Connection** | El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de Blob Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Desencadenador: uso

En C# y el script de C#, puede usar los tipos de parámetros siguientes para el blob de desencadenamiento:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Un objeto POCO serializable como JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Si intenta enlazar a uno de los tipos de SDK de Storage y recibe un mensaje de error, asegúrese de que hace referencia a [la versión de SDK de Storage correcta](#azure-storage-sdk-version-in-functions-1x).

El enlace a `string`, `Byte[]` o POCO solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`. Para obtener más información, consulte [Uso de simultaneidad y memoria](#trigger---concurrency-and-memory-usage) más adelante en este artículo.

En JavaScript, acceda a los datos de blob de entrada mediante `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Desencadenador: patrones de nombre de blob

Puede especificar un patrón de nombre de blob en la propiedad `path` en *function.json* o en el constructor de atributos `BlobTrigger`. El patrón de nombre puede ser una [expresión de filtro o enlace](./functions-bindings-expressions-patterns.md). En las siguientes secciones se proporcionan ejemplos.

### <a name="get-file-name-and-extension"></a>Obtener el nombre y la extensión de archivo

En el ejemplo siguiente se muestra cómo enlazar a nombre de archivo del blob y la extensión por separado:

```json
"path": "input/{blobname}.{blobextension}",
```
Si el blob se denomina *original-Blob1.txt*, los valores de las variables `blobname` y `blobextension` del código de la función son *original-Blob1* y *txt*.

### <a name="filter-on-blob-name"></a>Filtrar por nombre de blob

El ejemplo siguiente se desencadena solo en blobs del contenedor `input` que comienzan con la cadena "original-":

```json
"path": "input/original-{name}",
```

Si el nombre de blob es *original Blob1.txt*, el valor de la variable `name` en el código de la función es `Blob1`.

### <a name="filter-on-file-type"></a>Filtrar por tipo de archivo

El siguiente ejemplo se desencadena solo en archivos *.png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar por llaves en nombres de archivo

Para buscar llaves en nombres de archivo, escape las llaves mediante dos llaves. En el ejemplo siguiente se filtran blobs que tienen llaves en el nombre:

```json
"path": "images/{{20140101}}-{name}",
```

Si el blob se denomina *{20140101}-soundfile.mp3*, el valor de variable `name` en el código de la función es *soundfile.mp3*.

## <a name="trigger---metadata"></a>Desencadenador: metadatos

El desencadenador de blobs proporciona varias propiedades de metadatos. Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estos valores tienen la misma semántica que el tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

|Propiedad  |Escriba  |DESCRIPCIÓN  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ruta de acceso del blob de desencadenamiento.|
|`Uri`|`System.Uri`|Es el identificador URI del blob correspondiente a la ubicación principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Son las propiedades del sistema del blob. |
|`Metadata` |`IDictionary<string,string>`|Son los metadatos definidos por el usuario para el blob.|

Por ejemplo, los ejemplos de JavaScript y el script de C# siguientes registran la ruta de acceso al blob de desencadenamiento, incluido el contenedor:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Desencadenador: recepciones de blobs

El entorno en tiempo de ejecución de Azure Functions garantiza que no se llame más de una vez a ninguna función de desencadenador de blobs para un mismo blob, ya sea nuevo o actualizado. Mantiene *recepciones de blobs* para determinar si se ha procesado una determinada versión de blob.

Azure Functions almacena confirmaciones de blobs en un contenedor llamado *azure-webjobs-hosts* en la cuenta de almacenamiento de Azure de la aplicación de función (que se especifica mediante la configuración de la aplicación `AzureWebJobsStorage`). Una recepción de blobs tiene la información siguiente:

* La función desencadenada ("*&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*", por ejemplo: "MyFunctionApp.Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre del blob
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar el reprocesamiento de un blob, puede eliminar manualmente la recepción de ese blob desde el contenedor *azure-webjobs-hosts* . Mientras reprocesamiento podría no producirse inmediatamente, está garantizada para que se produzca en un momento posterior en el tiempo.

## <a name="trigger---poison-blobs"></a>Desencadenador: blobs dudosos

Si se produce un error en una función de desencadenador de blob, Azure Functions vuelve a intentar ejecutar esa función hasta 5 veces de forma predeterminada.

Si se produce un error en los 5 intentos, Azure Functions agregará un mensaje a una cola de Storage llamada *webjobs-blobtrigger-poison*. El mensaje de cola para los blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (con el formato *&lt;nombre de aplicación de función>*.Functions.*&lt;nombre de función>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (un identificador de la versión del blob, por ejemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Desencadenador: uso de simultaneidad y memoria

El desencadenador de blob utiliza una cola internamente, por lo que el número máximo de invocaciones de funciones simultáneas lo controla la [configuración de colas en host.json](functions-host-json.md#queues). La configuración predeterminada limita la simultaneidad a 24 invocaciones. Este límite se aplica por separado a cada función que usa un desencadenador de blob.

[El plan de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita una aplicación de función de una máquina virtual (VM) a 1,5 GB de memoria. Tanto las instancias de función que se ejecutan de forma simultánea como el entorno de ejecución de Functions utilizan la memoria. Si una función desencadenada por un blob carga el blob entero a la memoria, la memoria máxima utilizada por esa función solo para blobs tiene un tamaño máximo de blob de 24 *. Por ejemplo, una aplicación de función con tres funciones desencadenadas por un blob y la configuración predeterminada tendrían una simultaneidad máxima por máquina virtual de 3*24 = 72 invocaciones de función.

Las funciones de JavaScript y Java cargan el blob entero a la memoria, mientras que las funciones de C# lo hacen si establece un enlace a `string`, `Byte[]` o POCO.

## <a name="trigger---polling"></a>Desencadenador: sondeo

Si el contenedor de blobs supervisado contiene más de 10 000 blobs (en todos los contenedores), el tiempo de ejecución de Functions examinará los archivos para detectar los blobs nuevos o modificados de registro. Este proceso puede provocar retrasos. Una función podría tardar en desencadenarse varios minutos o más después de crear el blob.

> [!WARNING]
> Además, [se crean registros de almacenamiento basados en el principio del "mejor esfuerzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). No hay ninguna garantía de que se capturen todos los eventos. En algunos casos, podrían faltar registros.
> 
> Si necesita un procesamiento de blobs más rápido o confiable, considere crear un [mensaje de cola](../storage/queues/storage-dotnet-how-to-use-queues.md) al crear el blob. A continuación, use un [desencadenador de cola](functions-bindings-storage-queue.md), en lugar de un desencadenador de blob, para procesar el blob. Otra opción consiste en usar Event Grid; consulte el tutorial [Automatizar el cambio de tamaño de imágenes cargadas mediante Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Entrada

Utilice un enlace de entrada de almacenamiento de blobs para leer blobs.

## <a name="input---example"></a>Ejemplo de entrada

Vea el ejemplo específico del lenguaje:

* [C#](#input---c-example)
* [Script de C# (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Entrada: ejemplo de C#

El ejemplo siguiente es una [función de C#](functions-dotnet-class-library.md) que utiliza un desencadenador de cola y un enlaces de blobs de entrada. El mensaje de cola contiene el nombre del blob y la función registra el tamaño del blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="input---c-script-example"></a>Entrada: ejemplo de script de C#

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa los enlaces. La función realiza una copia de un blob de texto. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Entrada: ejemplo de JavaScript

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de JavaScript](functions-reference-node.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Entrada: ejemplo de Python

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y el [código de Python](functions-reference-python.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

En la sección de [configuración](#input---configuration) se explican estas propiedades.

Este es el código de Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Entrada: ejemplos de Java

En esta sección se incluyen los ejemplos siguientes:

* [Desencadenador de HTTP, búsqueda de nombre de blob en la cadena de consulta](#http-trigger-look-up-blob-name-from-query-string-java)
* [Desencadenador de cola, recepción del nombre del blob de la cola de mensajes](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Desencadenador de HTTP, búsqueda nombre de blob en la cadena de consulta (Java)

 El ejemplo siguiente muestra una función de Java que usa la anotación ```HttpTrigger``` para recibir un parámetro que contiene el nombre de un archivo en un contenedor de Blob Storage. Posteriormente, la anotación ```BlobInput``` lee el archivo y pasa el contenido a la función como ```byte[]```.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Desencadenador de cola, recepción del nombre del blob de la cola de mensajes (Java)

 El ejemplo siguiente muestra una función de Java que usa la anotación ```QueueTrigger``` para recibir un mensaje que contiene el nombre de un archivo en un contenedor de Blob Storage. Posteriormente, la anotación ```BlobInput``` lee el archivo y pasa el contenido a la función como ```byte[]```.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

En la [biblioteca en tiempo de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación `@BlobInput` en los parámetros cuyo valor provendría de un blob.  Esta anotación se puede usar con tipos nativos de Java, POJO o valores que aceptan valores NULL mediante `Optional<T>`.

## <a name="input---attributes"></a>Entrada: atributos

En las [bibliotecas de clase C#](functions-dotnet-class-library.md), use [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

El constructor del atributo toma la ruta de acceso al blob y un parámetro `FileAccess` que indica lectura o escritura, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Desencadenador: atributos](#trigger---attributes).

## <a name="input---configuration"></a>Entrada: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que establece en el archivo *function.json* y el atributo `Blob`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blob`. |
|**dirección** | N/D | Se debe establecer en `in`. Las excepciones se indican en la sección [uso](#input---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función.|
|**path** |**BlobPath** | Ruta de acceso al blob. |
|**conexión** |**Connection**| El nombre de una configuración de aplicación que contiene la [cadena de conexión de almacenamiento](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions utiliza la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de almacenamiento solo de blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N/D | **Acceder** | Indica si va a leer o escribir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Uso de entradas

En C# y el script de C#, puede usar los tipos de parámetros siguientes para el enlace de entrada de blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Si intenta enlazar a uno de los tipos de SDK de Storage y recibe un mensaje de error, asegúrese de que hace referencia a [la versión de SDK de Storage correcta](#azure-storage-sdk-version-in-functions-1x).

El enlace a `string` o `Byte[]` solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`. Para más información, consulte [Uso de simultaneidad y memoria](#trigger---concurrency-and-memory-usage) que apareció anteriormente en este artículo.

En JavaScript, acceda a los datos de blob mediante `context.bindings.<name from function.json>`.

## <a name="output"></a>Salida

Use enlaces de salida de almacenamiento de blobs para escribir blobs.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

El ejemplo siguiente es una [función de C#](functions-dotnet-class-library.md) que utiliza un desencadenador de blobs y dos enlaces de blobs de salida. La función se activa por la creación de un blob de imágenes en el contenedor *sample-images*. Crea copias pequeñas y medianas del blob de imágenes.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa los enlaces. La función realiza una copia de un blob de texto. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y código de [script de JavaScript](functions-reference-node.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Salida: ejemplo de Python

<!--Same example for input and output. -->

En el ejemplo siguiente se muestran enlaces de entrada y salida de blobs en un archivo *function.json* y el [código de Python](functions-reference-python.md) que usa los enlaces. La función realiza una copia de un blob. La función se activa mediante un mensaje de cola que contiene el nombre del blob que se va a copiar. El nuevo blob se denomina *{nombreoriginaldelblob}-Copy*.

En el archivo *function.json*, la propiedad de metadatos `queueTrigger` se utiliza para especificar el nombre del blob en las propiedades de `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Salida: ejemplos de Java

En esta sección se incluyen los ejemplos siguientes:

* [Desencadenador HTTP, uso de OutputBinding](#http-trigger-using-outputbinding-java)
* [Desencadenador de cola, uso del valor devuelto de la función](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Desencadenador HTTP, uso de OutputBinding (Java)

 El ejemplo siguiente muestra una función de Java que usa la anotación ```HttpTrigger``` para recibir un parámetro que contiene el nombre de un archivo en un contenedor de Blob Storage. Posteriormente, la anotación ```BlobInput``` lee el archivo y pasa el contenido a la función como ```byte[]```. La anotación ```BlobOutput``` enlaza a ```OutputBinding outputItem```. La función usa este posteriormente para escribir el contenido del blob de entrada en el contenedor de almacenamiento configurado.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Desencadenador de cola, uso del valor devuelto de la función (Java)

 El ejemplo siguiente muestra una función de Java que usa la anotación ```QueueTrigger``` para recibir un mensaje que contiene el nombre de un archivo en un contenedor de Blob Storage. Posteriormente, la anotación ```BlobInput``` lee el archivo y pasa el contenido a la función como ```byte[]```. La anotación ```BlobOutput``` enlaza al valor devuelto de la función. El entorno de ejecución usa este valor posteriormente para escribir el contenido del blob de entrada en el contenedor de almacenamiento configurado.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 En la [biblioteca en tiempo de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación `@BlobOutput` en los parámetros de función cuyo valor se escribiría en un objeto del almacenamiento de blobs.  El parámetro type debe ser `OutputBinding<T>`, donde T es cualquier tipo nativo de Java o un POJO.

## <a name="output---attributes"></a>Salida: atributos

En las [bibliotecas de clase C#](functions-dotnet-class-library.md), use [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

El constructor del atributo toma la ruta de acceso al blob y un parámetro `FileAccess` que indica lectura o escritura, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Desencadenador: atributos](#trigger---attributes).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Blob`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `blob`. |
|**dirección** | N/D | Debe establecerse en `out` para un enlace de salida. Las excepciones se indican en la sección [uso](#output---usage). |
|**name** | N/D | Nombre de la variable que representa el blob en el código de la función.  Se establece en `$return` para hacer referencia al valor devuelto de la función.|
|**path** |**BlobPath** | La ruta de acceso al blob. |
|**conexión** |**Connection**| El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions utiliza la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.<br><br>La cadena de conexión debe ser para una cuenta de almacenamiento de uso general, no una [cuenta de almacenamiento solo de blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|N/D | **Acceder** | Indica si va a leer o escribir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

En C# y script de C#, puede enlazar con los tipos siguientes para la escritura de blobs:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requiere un enlace "in" `direction` en *function.json* o `FileAccess.Read` en una biblioteca de clases de C#. Sin embargo, puede usar el objeto de contenedor que proporciona el tiempo de ejecución para escribir operaciones, como cargar blobs en el contenedor.

<sup>2</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Si intenta enlazar a uno de los tipos de SDK de Storage y recibe un mensaje de error, asegúrese de que hace referencia a [la versión de SDK de Storage correcta](#azure-storage-sdk-version-in-functions-1x).

En las funciones asincrónicas, use el valor devuelto o `IAsyncCollector` en lugar de un parámetro `out`.

El enlace a `string` o `Byte[]` solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`. Para más información, consulte [Uso de simultaneidad y memoria](#trigger---concurrency-and-memory-usage) que apareció anteriormente en este artículo.


En JavaScript, acceda a los datos de blob mediante `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace |  Referencia |
|---|---|
| Blob | [Códigos de error de blobs](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, tabla, cola |  [Códigos de error de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabla, cola |  [Solución de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
