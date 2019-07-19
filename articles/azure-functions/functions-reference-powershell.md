---
title: Referencia para desarrolladores de PowerShell para Azure Functions
description: Aprenda a desarrollar funciones con PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a75bdaf0e26193a5b2792b52923c085eff89b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706397"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guía del desarrollador de PowerShell para Azure Functions

En este artículo se proporcionan detalles sobre cómo escribir en Azure Functions con PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Una función de Azure de PowerShell (función) se representa como un script de PowerShell que se ejecuta cuando se desencadena. Cada script de función tiene un archivo `function.json` relacionado donde se define el comportamiento de la función, como su desencadenamiento y sus parámetros de entrada y salida. Para más información, consulte el [artículo sobre los desencadenadores y los enlaces](functions-triggers-bindings.md). 

Al igual que otros tipos de funciones, las funciones de script de PowerShell toman parámetros que coinciden con los nombres de todos los enlaces de entrada definidos en el archivo `function.json`. También se pasa un parámetro `TriggerMetadata` que contiene información adicional sobre el desencadenador que inició la función.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md). Debe haber completado también el [inicio rápido de Functions para PowerShell](functions-create-first-function-powershell.md) para crear su primera función de PowerShell.

## <a name="folder-structure"></a>Estructura de carpetas

La estructura de carpetas necesaria para un proyecto de PowerShell tiene el siguiente aspecto. Este valor predeterminado se puede cambiar. Para más información, consulte la sección [scriptFile](#configure-function-scriptfile) a continuación.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

En la raíz del proyecto, hay un archivo [`host.json`](functions-host-json.md) compartido que se puede usar para configurar la aplicación de función. Cada función tiene una carpeta con su propio archivo de código (.ps1) y archivo de configuración de enlace (`function.json`). El nombre del directorio primario del archivo function.json es siempre es el nombre de la función.

Determinados enlaces requieren la presencia de un archivo `extensions.csproj`. Las extensiones de enlace, necesarias en la [versión 2.x](functions-versions.md) de Functions Runtime, se definen en el archivo `extensions.csproj`, con los archivos de biblioteca reales en la carpeta `bin`. Al desarrollar de forma local, debe [registrar las extensiones de enlace](functions-bindings-register.md#extension-bundles). Al desarrollar funciones en Azure Portal, este registro se realiza automáticamente.

En las aplicaciones de funciones de PowerShell, podría tener opcionalmente un `profile.ps1` que se ejecute cuando una aplicación de funciones empiece a ejecutarse (lo que también se conoce como *[arranque en frío](#cold-start)* . Para más información, consulte el [Perfil de PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definición de un script de PowerShell como función

De forma predeterminada, el tiempo de ejecución de Functions busca la función en `run.ps1`, donde `run.ps1` comparte el mismo directorio primario que su `function.json` correspondiente.

Varios argumentos se pasan al script durante la ejecución. Para controlar estos parámetros, agregue un bloque `param` a la parte superior del script como en el siguiente ejemplo:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parámetro TriggerMetadata

El parámetro `TriggerMetadata` se usa para proporcionar información adicional acerca del desencadenador. Los metadatos adicionales varían de un enlace a otro, pero todos ellos contienen una propiedad `sys` con los siguientes datos:

```powershell
$TriggerMetadata.sys
```

| Propiedad   | DESCRIPCIÓN                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Cuándo se desencadenó la función (en formato UTC)        | Datetime |
| MethodName | Nombre de la función desencadenada     | string   |
| RandGuid   | GUID único para esta ejecución de la función | string   |

Cada tipo de desencadenador tiene un conjunto diferente de metadatos. Por ejemplo, `$TriggerMetadata` para `QueueTrigger` contiene `InsertionTime`, `Id` y `DequeueCount`, entre otras cosas. Para más información sobre los metadatos del desencadenador de cola, vaya a la [documentación oficial de los desencadenadores de cola](functions-bindings-storage-queue.md#trigger---message-metadata). Consulte la documentación sobre los [desencadenadores](functions-triggers-bindings.md) con los que está trabajando para ver lo que está incluido en los metadatos de desencadenador.

## <a name="bindings"></a>Enlaces

En PowerShell, los [enlaces](functions-triggers-bindings.md) se configuran y definen en el archivo function.json de una función. Las funciones interactúan con los enlaces de varias maneras.

### <a name="reading-trigger-and-input-data"></a>Lectura de datos del desencadenador y de entrada

Los desencadenadores y los enlaces de entrada se leen como parámetros que se pasan a la función. Los enlaces de entrada tienen un `direction` establecido en `in` en function.json. La propiedad `name` definida en `function.json` es el nombre del parámetro, en el bloque `param`. Dado que PowerShell utiliza parámetros con nombre para el enlace, no importa el orden de estos. Sin embargo, es un procedimiento recomendado seguir el orden de los enlaces definidos en el archivo `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Escritura de datos de salida

En Functions, un enlace de salida tiene un `direction` establecido en `out` en el archivo function.json. Puede escribir en un enlace de salida mediante el cmdlet `Push-OutputBinding`, disponible para Functions Runtime. En cualquier caso, la propiedad `name` del enlace tal como se define en `function.json` corresponde al parámetro `Name` del cmdlet `Push-OutputBinding`.

A continuación se muestra cómo llamar a `Push-OutputBinding` en el script de la función:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

También puede pasar un valor para un enlace específico mediante la canalización.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` se comporta de forma distinta según el valor especificado para `-Name`:

* Cuando no se puede resolver el nombre especificado en un enlace de salida válido, se produce un error.

* Cuando el enlace de salida acepta una colección de valores, puede llamar a `Push-OutputBinding` varias veces para insertar varios valores.

* Cuando el enlace de salida solo acepta un valor singleton, volver a llamar a`Push-OutputBinding` produce un error.

#### <a name="push-outputbinding-syntax"></a>Sintaxis de `Push-OutputBinding`

Los siguientes son parámetros válidos para llamar a `Push-OutputBinding`:

| NOMBRE | type | Posición | DESCRIPCIÓN |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Cadena | 1 | Nombre del enlace de salida que quiere establecer. |
| **`-Value`** | Object | 2 | Valor del enlace de salida que desea establecer, aceptado desde la canalización ByValue. |
| **`-Clobber`** | SwitchParameter | con nombre | (Opcional) Cuando se especifica, se fuerza el establecimiento del valor para un enlace de salida especificado. | 

También se admiten los siguientes parámetros habituales: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para más información, consulte [About Common Parameters](https://go.microsoft.com/fwlink/?LinkID=113216) (Acerca de los parámetros habituales).

#### <a name="push-outputbinding-example-http-responses"></a>Ejemplo de Push-OutputBinding: Respuestas HTTP

Un desencadenador HTTP devuelve una respuesta con un enlace de salida denominado `response`. En el siguiente ejemplo, el enlace de salida de `response` tiene el valor de "output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Dado que la salida es HTTP, que acepta solo un valor singleton, al volver a llamar a `Push-OutputBinding` se produce un error.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para las salidas que solo aceptan valores singleton, puede usar el parámetro `-Clobber` para invalidar el antiguo valor en lugar de intentar agregarlo a una colección. En el siguiente ejemplo se da por supuesto que ya ha agregado un valor. Mediante el uso de `-Clobber`, la respuesta de ejemplo siguiente invalida el valor existente para devolver un valor de "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Ejemplo de Push-OutputBinding: Enlace de salida de cola

`Push-OutputBinding` se usa para enviar datos a los enlaces de salida como [enlace de salida de Azure Queue Storage](functions-bindings-storage-queue.md#output). En el siguiente ejemplo, el mensaje que se escribe en la cola tiene un valor de "output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

El enlace de salida para una cola de Storage acepta varios valores de salida. En este caso, llamar al siguiente ejemplo después del primero hace que se escriba en la cola una lista con dos elementos: "output #1" y "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Cuando se llama al siguiente ejemplo después de los dos anteriores, se agregan dos valores más a la colección de salida:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Cuando se escribe en la cola, el mensaje contiene estos cuatro valores: "output #1", "output #2", "output #3" y "output #4".

#### <a name="get-outputbinding-cmdlet"></a>cmdlet `Get-OutputBinding`

Puede usar el cmdlet `Get-OutputBinding` para recuperar los valores establecidos actualmente para los enlaces de salida. Este cmdlet recupera una tabla hash que contiene los nombres de los enlaces de salida con sus respectivos valores. 

El siguiente es un ejemplo del uso de `Get-OutputBinding` para devolver valores de enlace actuales:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` también contiene un parámetro denominado `-Name` que se puede usar para filtrar el enlace devuelto, como en el siguiente ejemplo:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

En `Get-OutputBinding` se admiten caracteres comodín (*).

## <a name="logging"></a>Registro

El inicio de sesión en las funciones de PowerShell funciona como el inicio de sesión normal de PowerShell. Puede usar los cmdlets de registro para escribir en cada flujo de salida. Cada cmdlet se asigna a un nivel de registro utilizado por Functions.

| Nivel de registro de Functions | cmdlet de registro |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| Advertencia | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Escribe en nivel _Información_ de registro. |
| Depurar | **`Write-Debug`** |
| Seguimiento | **`Write-Progress`** <br /> **`Write-Verbose`** |

Además de estos cmdlets, todo lo escrito en la canalización se redirige al nivel de registro `Information` y se muestra con el formato predeterminado de PowerShell.

> [!IMPORTANT]
> El uso de los cmdlets `Write-Verbose` o `Write-Debug` no es suficiente para ver los niveles de registro detallado y de depuración. También debe configurar el umbral de nivel de registro que declara qué nivel de registro es realmente importante para usted. Para más información, consulte el artículo de [Configuración del nivel de registro de la aplicación de funciones](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configuración del nivel de registro de la aplicación de funciones

Azure Functions permite definir el nivel umbral para facilitar el control de la manera en que Functions escribe en los registros. Para establecer el umbral para todos los seguimientos que se escriben en la consola, use la propiedad `logging.logLevel.default` en el [`host.json`archivo host.json][referencia de host.json]. Esta configuración se aplica a todas las funciones de Function App.

En el siguiente ejemplo se establece el umbral para habilitar el registro detallado para todas las funciones, pero establece el umbral para habilitar el registro de depuración para una función denominada `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Para más información, consulte la [Referencia de host.json].

### <a name="viewing-the-logs"></a>Visualización de los registros

Si la aplicación de funciones se ejecuta en Azure, puede usar Application Insights para supervisarla. Consulte cómo [supervisar Azure Functions](functions-monitoring.md) para obtener más información sobre cómo ver y consultar registros de la función.

Si la aplicación de funciones se ejecuta localmente para el desarrollo, los registros están de manera predeterminada en el sistema de archivos. Para ver los registros en la consola, establezca la variable de entorno `AZURE_FUNCTIONS_ENVIRONMENT` en `Development` antes de iniciar la aplicación de funciones.

## <a name="triggers-and-bindings-types"></a>Tipos de desencadenadores y enlaces

Hay varios desencadenadores y enlaces disponibles para usar con la aplicación de funciones. La lista completa de los desencadenadores y enlaces se encuentra [aquí](functions-triggers-bindings.md#supported-bindings).

Todos los desencadenadores y enlaces se representan en código como tipos de datos reales:

* Hashtable
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Los cinco primeros tipos de esta lista son tipos de .NET estándar. Los dos últimos los usa únicamente el [desencadenador HttpTrigger](#http-triggers-and-bindings).

Todos los parámetros de enlace de las funciones deben ser de uno de estos tipos.

### <a name="http-triggers-and-bindings"></a>Desencadenadores y enlaces HTTP

Los desencadenadores HTTP y de webhook trigger y los enlaces de salida HTTP usan objetos de solicitud y respuesta para representar la mensajería HTTP.

#### <a name="request-object"></a>Objeto de solicitud

El objeto de solicitud que se pasa al script es del tipo `HttpRequestContext`, con las siguientes propiedades:

| Propiedad  | DESCRIPCIÓN                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objeto que contiene el cuerpo de la solicitud. `Body` se serializa al mejor tipo en función de los datos. Por ejemplo, si los datos son JSON, se pasa como tabla hash. Si los datos están una cadena, se pasan en forma de cadena. | objeto |
| **`Headers`** | Diccionario que contiene los encabezados de la solicitud.                | Diccionario<cadena,cadena><sup>*</sup> |
| **`Method`** | Método HTTP de la solicitud.                                | string                    |
| **`Params`**  | Objeto que contiene los parámetros de enrutamiento de la solicitud. | Diccionario<cadena,cadena><sup>*</sup> |
| **`Query`** | Objeto que contiene los parámetros de consulta.                  | Diccionario<cadena,cadena><sup>*</sup> |
| **`Url`** | Dirección URL de la solicitud.                                        | string                    |

<sup>*</sup>Todas las claves `Dictionary<string,string>` distinguen mayúsculas de minúsculas.

#### <a name="response-object"></a>Objeto de respuesta

El objeto de respuesta que debe enviar de vuelta es de tipo `HttpResponseContext`, que tiene las siguientes propiedades:

| Propiedad      | DESCRIPCIÓN                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objeto que contiene el cuerpo de la respuesta.           | objeto                    |
| **`ContentType`** | Una mano corta para establecer el tipo de contenido para la respuesta. | string                    |
| **`Headers`** | Objeto que contiene los encabezados de la respuesta.               | Diccionario o tabla hash   |
| **`StatusCode`**  | Código de estado HTTP de la respuesta.                       | cadena o entero             |

#### <a name="accessing-the-request-and-response"></a>Acceso a solicitudes y respuestas

Al trabajar con desencadenadores HTTP, puede acceder a la solicitud HTTP del mismo modo que lo haría con cualquier otro enlace de entrada. Se encuentra en el bloque `param`.

Use un objeto `HttpResponseContext` para devolver una respuesta, tal como se muestra a continuación:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

El resultado de invocar esta función sería:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Conversión de tipos de desencadenadores y enlaces

Para determinados enlaces como el enlace de blobs, podrá especificar el tipo del parámetro.

Por ejemplo, para que los datos de Blob Storage se proporcionen como cadena, agregue la siguiente conversión de tipo de mi bloque `param`:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil de PowerShell

En PowerShell, existe el concepto de "perfil de PowerShell". Si no está familiarizado con los perfiles de PowerShell, consulte [About profiles](/powershell/module/microsoft.powershell.core/about/about_profiles) (Acerca de los perfiles).

En Functions de PowerShell el script de perfil se ejecuta al iniciarse la aplicación de funciones. Las aplicaciones de funciones se inician con la primera implementación y después de estar inactivas ([arranque en frío](#cold-start)).

Al crear una aplicación de funciones con herramientas como Visual Studio Code y Azure Functions Core Tools, el `profile.ps1` predeterminado se crea automáticamente. El perfil predeterminado se mantiene [en el repositorio de GitHub de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) y contiene:

* La autenticación automática de MSI en Azure.
* La capacidad de activar los alias de PowerShell para `AzureRM` de Azure PowerShell, si lo desea.

## <a name="powershell-version"></a>Versión de PowerShell

En la tabla siguiente se muestra la versión de PowerShell que se usa en cada versión principal de Functions Runtime:

| Versión de Functions | Versión de PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloqueado por Runtime) |
| 2.x               | PowerShell Core 6                              |

Puede ver la versión actual mediante la impresión de `$PSVersionTable` desde cualquier función.

## <a name="dependency-management"></a>Administración de dependencias

Las funciones de PowerShell admiten que el servicio administre módulos de Azure. Al modificar el archivo host.json y configurar la propiedad habilitada managedDependency en true, se procesará el archivo requirements.psd1. Los módulos de Azure más recientes se descargarán automáticamente y estarán disponibles para la función.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

El aprovechamiento de sus proprios módulos personalizados o de módulos de la [Galería de PowerShell](https://powershellgallery.com) difiere un poco de lo normal.

Al instalar el módulo en la máquina local, va a una de las carpetas con disponibilidad global de `$env:PSModulePath`. Dado que la función se ejecuta en Azure, no tendrá acceso a los módulos instalados en la máquina. Esto requiere que `$env:PSModulePath` de una aplicación de funciones de PowerShell difiera de `$env:PSModulePath` en un script de PowerShell normal.

En Functions, `PSModulePath` contiene dos rutas de acceso:

* Una carpeta `Modules` que existe en la raíz de la aplicación de funciones.
* Una ruta de acceso a una carpeta `Modules` que se encuentra en el trabajo de lenguaje de PowerShell.

### <a name="function-app-level-modules-folder"></a>Carpeta `Modules` del nivel de la aplicación de funciones

Para usar los módulos personalizados o los módulos de PowerShell desde la Galería de PowerShell, puede colocar los módulos de los que dependen las funciones en una carpeta `Modules`. Desde esta carpeta los módulos están automáticamente disponibles para Functions Runtime. Cualquier función de la aplicación de funciones puede usar estos módulos.

Para aprovechar las ventajas de esta característica, cree una carpeta `Modules` en la raíz de la aplicación de funciones. Guarde los módulos que desee usar en las funciones en esta ubicación.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Use `Save-Module` para guardar todos los módulos que usan las funciones o copie sus propios módulos personalizados en la carpeta `Modules`. Con una carpeta Modules, la aplicación de funciones debe tener la siguiente estructura de carpetas:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Al inicia la aplicación de funciones, el trabajo de lenguaje de PowerShell agrega esta carpeta `Modules` a `$env:PSModulePath`, de manera que pueda confiar en la carga automática del módulo del mismo modo que lo haría en un script de PowerShell normal.

### <a name="language-worker-level-modules-folder"></a>Carpeta `Modules` del nivel de trabajo de lenguaje

El trabajo de lenguaje de PowerShell usa varios módulos frecuentemente. Estos módulos se definen en la última posición de `PSModulePath`. 

La lista actual de módulos es como sigue:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usadas para trabajar con archivos, como `.zip`, `.nupkg` y otros.
* **ThreadJob**: implementación basada en subprocesos de las API de trabajo de PowerShell.

Functions usa la versión más reciente de estos módulos. Para usar una versión específica de estos módulos, puede colocarla en la carpeta `Modules` de la aplicación de funciones.

## <a name="environment-variables"></a>Variables de entorno

En Functions, [la configuración de la aplicación](functions-app-settings.md), como las cadenas de conexión del servicio, se exponen como variables de entorno durante la ejecución. Puede acceder a esta configuración mediante `$env:NAME_OF_ENV_VAR`, como se muestra en el siguiente ejemplo:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Cuando se ejecuta localmente, la configuración de la aplicación se lee desde el archivo del proyecto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Simultaneidad

De forma predeterminada, Function Runtime de PowerShell solo puede procesar una invocación de función a la vez. Sin embargo, este nivel de simultaneidad podría no ser suficiente en las siguientes situaciones:

* Al intentar controlar un gran número de invocaciones a la vez.
* Al disponer de funciones que invocan a otras dentro de la misma aplicación de funciones.

Puede cambiar este comportamiento estableciendo la variable de entorno siguiente en un valor entero:

```
PSWorkerInProcConcurrencyUpperBound
```

Establezca esta variable de entorno en la [configuración de la aplicación](functions-app-settings.md) de la aplicación de funciones.

### <a name="considerations-for-using-concurrency"></a>Consideraciones para usar la simultaneidad

De forma predeterminada, PowerShell es un _único subproceso_ de lenguaje de scripting. Sin embargo, la simultaneidad puede agregarse mediante el uso de varios espacios de ejecución de PowerShell en el mismo proceso. Esta característica es cómo funciona Azure Functions Runtime de PowerShell.

Existen algunos inconvenientes con este enfoque.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>La simultaneidad es solo tan buena como la máquina donde se ejecuta

Si la aplicación de funciones se ejecuta en un [plan de App Service](functions-scale.md#app-service-plan) que solo admite un único núcleo, la simultaneidad no ayuda demasiado. Esto es así porque no hay núcleos adicionales para ayudar a equilibrar la carga. En este caso, el rendimiento puede variar cuando el núcleo único tiene intercambios de contexto entre espacios de ejecución.

El [plan Consumo](functions-scale.md#consumption-plan) se ejecuta con un solo núcleo,por lo que la simultaneidad no se aprovecha. Si desea aprovechar al máximo la simultaneidad, implemente las funciones a una aplicación de funciones que se ejecute en un plan de App Service dedicado con núcleos suficientes.

#### <a name="azure-powershell-state"></a>Estado de Azure PowerShell

Azure PowerShell usa algunos procesos y estados de _nivel de proceso_ para que no tenga que escribir tanto. Sin embargo, si activa la simultaneidad en la aplicación de funciones e invoca acciones que cambien el estado, puede acabar con las condiciones de carrera. Estas condiciones de carrera son difíciles de depurar, ya que una invocación se basa en un estado determinado y la otra ha cambiado el estado.

La simultaneidad es muy valiosa con Azure PowerShell, ya que algunas operaciones pueden tardar un tiempo considerable. Sin embargo, debe proceder con precaución. Si sospecha que tiene una condición de carrera, establecer la simultaneidad de nuevo en `1` e intente de nuevo la solicitud.

## <a name="configure-function-scriptfile"></a>Configuración de la función `scriptFile`

De forma predeterminada, se ejecuta una función de PowerShell desde `run.ps1`, un archivo que comparte el mismo directorio primario que su archivo `function.json` correspondiente.

La propiedad `scriptFile` de `function.json` se puede usar para obtener una estructura de carpetas que tenga el aspecto del siguiente ejemplo:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

En este caso, el archivo `function.json` para `myFunction` incluye una propiedad `scriptFile` que hace referencia al archivo con la función exportada que se va a ejecutar.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Uso de módulos de PowerShell mediante la configuración de entryPoint

En este artículo se han mostrado las funciones de PowerShell en el archivo de script `run.ps1` predeterminado generado por las plantillas.
Sin embargo, también puede incluir las funciones en módulos de PowerShell. Puede hacer referencia a su código de función específico del módulo mediante los campos `scriptFile` y `entryPoint` del archivo de configuración function.json.

En este caso, `entryPoint` es el nombre de una función o un cmdlet del módulo de PowerShell a los que se hace referencia en `scriptFile`.

Considere la siguiente estructura de carpetas:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Donde `PSFunction.psm1` contiene:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

En este ejemplo, la configuración de `myFunction` incluye una propiedad `scriptFile` que hace referencia a `PSFunction.psm1`, que es un módulo de PowerShell de otra carpeta.  La propiedad `entryPoint` hace referencia a la función `Invoke-PSTestFunc`, que es el punto de entrada del módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Con esta configuración, `Invoke-PSTestFunc` se ejecuta exactamente como lo haría `run.ps1`.

## <a name="considerations-for-powershell-functions"></a>Consideraciones para las funciones de PowerShell

Al trabajar con las funciones de PowerShell, tenga en cuenta las consideraciones de las siguientes secciones.

### <a name="cold-start"></a>Arranque en frío

Al desarrollar Azure Functions en el [modelo de hospedaje sin servidor](functions-scale.md#consumption-plan), los arranques en frío son una realidad. El *arranque en frío* se refiere al período de tiempo tarda la aplicación de funciones en empezar a ejecutarse para procesar una solicitud. El arranque en frío se produce con mayor frecuencia en el plan Consumo, puesto que la aplicación de funciones se cierra durante los períodos de inactividad.

### <a name="bundle-modules-instead-of-using-install-module"></a>Módulos de agrupación en lugar de `Install-Module`

El script se ejecuta en cada invocación. Evite el uso de `Install-Module` en el script. En su lugar, use `Save-Module` antes de publicar para que la función no tenga que perder tiempo descargando el módulo. Si los arranques en frío afectan a las funciones, considere la posibilidad de implementar la aplicación de funciones en un [plan de App Service](functions-scale.md#app-service-plan) establecido en *AlwaysOn* o [plan Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* [Referencia para desarrolladores de Azure Functions](functions-reference.md)
* [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)

[Referencia de host.json]: functions-host-json.md
