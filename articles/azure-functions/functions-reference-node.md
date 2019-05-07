---
title: Referencia para desarrolladores de JavaScript para Azure Functions | Microsoft Docs
description: Obtenga información sobre cómo desarrollar funciones con JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 2eea1a1d30558765a2f8320b0b23efdbe3368807
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140956"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guía para el desarrollador de JavaScript para Azure Functions

Esta guía contiene información acerca de las complejidades de la escritura de Azure Functions con JavaScript.

Una función de JavaScript es una función exportada (`function`) que ejecuta al desencadenarse ([los desencadenadores están configurados en function.json](functions-triggers-bindings.md)). El primer argumento pasado a cada función es un `context` objeto, que se usa para el envío y recepción enlace de datos, registro y la comunicación con el tiempo de ejecución.

En este artículo se supone que ya ha leído [Referencia para desarrolladores de Azure Functions](functions-reference.md). Completar el inicio rápido de Functions para crear su primera función, mediante [Visual Studio Code](functions-create-first-function-vs-code.md) o [en el portal de](functions-create-first-azure-function.md).

En este artículo también admite [desarrollo de aplicaciones de TypeScript](#typescript).

## <a name="folder-structure"></a>Estructura de carpetas

La estructura de carpetas necesaria para un proyecto de JavaScript tiene el siguiente aspecto. Este valor predeterminado se puede cambiar. Para más información, consulte la sección [scriptFile](#using-scriptfile) a continuación.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

En la raíz del proyecto, hay un archivo [host.json](functions-host-json.md) compartido que se puede usar para configurar la aplicación de función. Cada función tiene una carpeta con su propio archivo de código (.js) y archivo de configuración de enlace (function.json). El nombre del directorio primario de `function.json` siempre es el nombre de la función.

Las extensiones de enlace necesarias en la [versión 2.x](functions-versions.md) del tiempo de ejecución de Functions se definen en el archivo `extensions.csproj`, con los archivos de biblioteca de la carpeta `bin`. Al desarrollar de forma local, debe [registrar las extensiones de enlace](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Al desarrollar funciones en Azure Portal, este registro se realiza automáticamente.

## <a name="exporting-a-function"></a>Exportación de una función

Deben exportarse las funciones de JavaScript mediante [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (o [`exports`](https://nodejs.org/api/modules.html#modules_exports)). La función exportada debe ser una función JavaScript que se ejecute al desencadenarse.

De forma predeterminada, el tiempo de ejecución de Functions busca la función en `index.js`, donde `index.js` comparte el mismo directorio primario que su `function.json` correspondiente. En el caso predeterminado, la función exportada debe ser la única exportación de su archivo, o bien la exportación denominada `run` o `index`. Para configurar la ubicación del archivo y el nombre de exportación de la función, obtenga información sobre la [configuración del punto de entrada de la función](functions-reference-node.md#configure-function-entry-point) a continuación.

Varios argumentos se pasan a la función exportada durante la ejecución. El primer argumento que toma siempre es un objeto `context`. Si la función es sincrónica (no devuelve una promesa), debe pasar el objeto `context`, ya que se requiere la llamada a `context.done` para que el uso sea correcto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportación de una función asincrónica
Cuando se usa la declaración [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) o las [promesas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) de JavaScript sin formato en la versión 2.x del tiempo de ejecución de Functions, no tiene que llamar explícitamente a la devolución de llamada [`context.done`](#contextdone-method) para indicar que la función se ha completado. La función se completa cuando la función asincrónica o la promesa exportada se completa. Para las funciones destinadas a la versión 1.x del tiempo de ejecución, debe llamar a [`context.done`](#contextdone-method) cuando el código finalice la ejecución.

El ejemplo siguiente es una función simple que registra que se ha desencadenado y completa la ejecución inmediatamente.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Al exportar una función asincrónica, también puede configurar en enlace de salida para tomar el valor `return`. Esto se recomienda si solo tiene un enlace de salida.

Para asignar una salida mediante `return`, cambie la propiedad `name` a `$return` en `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

En este caso, la función debe tener el aspecto del ejemplo siguiente:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Enlaces 
En JavaScript, los [enlaces](functions-triggers-bindings.md) se configuran y definen en el archivo function.json de una función. Las funciones interactúan con los enlaces de varias maneras.

### <a name="inputs"></a>Entradas
Las entradas se dividen en dos categorías Azure Functions: una es la entrada del desencadenador y la otra es una entrada adicional. Una función puede leer los enlaces del desencadenador y de entrada (enlaces de `direction === "in"`) de tres maneras:
 - **_[Recomendada]_ Como parámetros pasados a la función.** Se pasan a la función en el mismo orden en que se definen en *function.json*. El `name` propiedad definida en *function.json* no necesita coincidir con el nombre del parámetro, aunque lo que debería.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como miembros del objeto [`context.bindings`](#contextbindings-property).** Cada miembro se denomina mediante la propiedad `name` definida en el archivo *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas mediante el objeto [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) de JavaScript.** Es básicamente lo mismo que pasar entradas como parámetros, pero permite controlar las entradas de manera dinámica.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Salidas
Las salidas (enlaces de `direction === "out"`) se pueden escribir mediante una función de varias maneras. En todos los casos, la propiedad `name` del enlace tal como se define en el archivo *function.json* corresponde al nombre del miembro de objeto escrito en la función. 

Puede asignar datos a los enlaces de salida en una de las maneras siguientes (no combinar estos métodos):

- **_[Recomendado para varias salidas]_ Devolución de un objeto.** Si usas un async/promesa devuelve la función, puede devolver un objeto con datos de salida asignado. En el ejemplo siguiente, los enlaces de salida se denominan "httpResponse" y "queueOutput" en el archivo *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Si utiliza una función sincrónica, puede devolver este objeto mediante [`context.done`](#contextdone-method) (vea el ejemplo).
- **_[Recomendado para una salida única]_ Devolución de un valor directamente y uso del nombre de enlace $return.** Este método solo funciona con las funciones de devolución asincrónicas o de promesa. Vea el ejemplo de [Exportación de una función asincrónica](#exporting-an-async-function). 
- **Asignación de valores a `context.bindings`**. Puede asignar valores directamente a context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo de datos de enlaces

Para definir el tipo de datos para un enlace de entrada, use la propiedad `dataType` de la definición del enlace. Por ejemplo, para leer el contenido de una solicitud HTTP en formato binario, use el tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Las opciones para `dataType` son: `binary`, `stream` y `string`.

## <a name="context-object"></a>objeto de contexto
El sistema en tiempo de ejecución usa un objeto `context` para transmitir datos desde la función y hacia esta, así como para posibilitar la comunicación con dicho sistema en tiempo de ejecución. El objeto de contexto se puede usar para leer y establecer los datos de los enlaces, escribir registros y usar la devolución de llamada `context.done` cuando la función exportada es sincrónica.

El objeto `context` siempre es el primer parámetro para una función. Debe incluirse porque tiene métodos importantes, tales como `context.done` y `context.log`. Puede asignar el nombre que desee al objeto (por ejemplo, `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propiedad context.bindings

```js
context.bindings
```

Devuelve un objeto con nombre que se usa para leer o asignar el enlace de datos. Desencadenador de enlace de datos de entrada y salida pueden obtenerse mediante la lectura de propiedades en `context.bindings`. Enlace de datos de salida se puede asignar mediante la adición de datos a `context.bindings`

Por ejemplo, las siguientes definiciones de enlace del archivo function.json permiten acceder al contenido de una cola desde `context.bindings.myInput` y asignar salidas a una cola mediante `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Puede optar por definir datos de enlace de salida a través del método `context.done` en lugar del objeto `context.binding` (consulte a continuación).

### <a name="contextbindingdata-property"></a>Propiedad context.bindingData

```js
context.bindingData
```

Devuelve un objeto con nombre que contiene los metadatos de desencadenar y los datos de invocación de función (`invocationId`, `sys.methodName`, `sys.utcNow` y `sys.randGuid`). Para obtener un ejemplo de metadatos de desencadenador, consulte este [ejemplo de centros de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Método context.done

```js
context.done([err],[propertyBag])
```

Permite que el tiempo de ejecución sepa que el código se ha completado. Si su función usa la declaración [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), no es necesario que utilice `context.done()`. La devolución de llamada `context.done` se realiza implícitamente. Las funciones asincrónicas están disponibles en Node 8 o una versión posterior, que requiere la versión 2.x del tiempo de ejecución de Functions.

Si la función no es una función asincrónica, **debe llamar a** `context.done` para informar al entorno en tiempo de ejecución que la función está completa. La ejecución agota el tiempo de espera si no está presente.

El método `context.done` le permite devolver un error definido por el usuario al entorno en tiempo de ejecución y un objeto JSON que contiene los datos de enlace de salida. Las propiedades que se pasan a `context.done` sobrescriben lo que ha definido en el objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Método context.log  

```js
context.log(message)
```

Permite escribir en los registros de la función de streaming en el nivel de seguimiento predeterminado. Hay métodos de registro adicionales disponibles en `context.log` que permiten escribir registros de la función en otros niveles de seguimiento:


| Método                 | DESCRIPCIÓN                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Escribe en el registro de nivel de error o inferior.   |
| **warn(_message_)**    | Escribe en el registro de nivel de advertencia o inferior. |
| **info(_message_)**    | Escribe en el registro de nivel de información o inferior.    |
| **verbose(_message_)** | Escribe en el registro de nivel detallado.           |

En el ejemplo siguiente se escribe un registro en el nivel de seguimiento de advertencia:

```javascript
context.log.warn("Something has happened."); 
```

Puede [configurar el umbral de nivel de seguimiento de los registros](#configure-the-trace-level-for-console-logging) en el archivo host.json. Para más información sobre cómo escribir registros, consulte cómo [escribir resultados de seguimiento](#writing-trace-output-to-the-console) a continuación.

Consulte cómo [supervisar Azure Functions](functions-monitoring.md) para obtener más información sobre cómo ver y consultar registros de la función.

## <a name="writing-trace-output-to-the-console"></a>Escribir las salidas de seguimiento en la consola 

En Functions, use los métodos `context.log` para escribir la salida de seguimiento en la consola. En Functions 2.x, las salidas de seguimiento mediante `console.log` se capturan en el nivel de Function App. Esto significa que las salidas de `console.log` no están asociados a una invocación de función específicos y no se muestran en los registros de una función específica. No obstante, se propagan a Application Insights. En Functions 1.x, no puede usar `console.log` para escribir en la consola.

Cuando se llama a `context.log()`, el mensaje se escribe en la consola en el nivel de seguimiento predeterminado, que es el nivel de seguimiento de _información_. El siguiente código escribe en la consola en el nivel de seguimiento de información:

```javascript
context.log({hello: 'world'});  
```

Este código es equivalente al código anterior:

```javascript
context.log.info({hello: 'world'});  
```

Este código escribe en la consola en el nivel de seguimiento de error:

```javascript
context.log.error("An error has occurred.");  
```

Dado que _error_ es el nivel de seguimiento más alto, este seguimiento se escribe en la salida en todos los niveles de seguimiento, siempre y cuando el registro esté habilitado.

Todos los métodos `context.log` admiten el mismo formato de parámetro que el [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node.js. Considere el siguiente código que escribe registros de función mediante el nivel de seguimiento predeterminado:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

También puede escribir el mismo código con el formato siguiente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configuración del nivel de seguimiento para el registro de la consola

Functions 1.x permite definir el nivel de seguimiento de umbral para escribir en la consola, que facilita el control de la forma en que se escriben los seguimientos en la consola desde la función. Para establecer el umbral para todos los seguimientos que se escriben en la consola, use la propiedad `tracing.consoleLevel` en el archivo host.json . Esta configuración se aplica a todas las funciones de Function App. En el ejemplo siguiente se establece el umbral de seguimiento para habilitar el registro detallado:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Los valores de **consoleLevel** corresponden a los nombres de los métodos `context.log`. Para deshabilitar todos los registros de seguimiento en la consola, establezca **consoleLevel** en _off_. Para más información, consulte la [referencia sobre host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Desencadenadores y enlaces HTTP

Los desencadenadores HTTP y de webhook trigger y los enlaces de salida HTTP usan objetos de solicitud y respuesta para representar la mensajería HTTP.  

### <a name="request-object"></a>Objeto de solicitud

El objeto `context.req` (solicitud) tiene las siguientes propiedades:

| Propiedad      | DESCRIPCIÓN                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objeto que contiene el cuerpo de la solicitud.               |
| _headers_     | Objeto que contiene los encabezados de la solicitud.                   |
| _method_      | Método HTTP de la solicitud.                                |
| _originalUrl_ | Dirección URL de la solicitud.                                        |
| _params_      | Objeto que contiene los parámetros de enrutamiento de la solicitud. |
| _consulta_       | Objeto que contiene los parámetros de consulta.                  |
| _rawBody_     | Cuerpo del mensaje como una cadena.                           |


### <a name="response-object"></a>Objeto de respuesta

El objeto `context.res` (respuesta) tiene las siguientes propiedades:

| Propiedad  | DESCRIPCIÓN                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objeto que contiene el cuerpo de la respuesta.         |
| _headers_ | Objeto que contiene los encabezados de la respuesta.             |
| _isRaw_   | Indica que se omite el formato en la respuesta.    |
| _estado_  | Código de estado HTTP de la respuesta.                     |

### <a name="accessing-the-request-and-response"></a>Acceso a solicitudes y respuestas 

Cuando se trabaja con desencadenadores HTTP, hay varias maneras de acceder a los objetos de solicitud y respuesta HTTP:

+ **Desde las propiedades `req` y `res` del objeto `context`.** De esta manera, puede usar el patrón convencional para acceder a los datos HTTP desde el objeto de contexto, en lugar de tener que utilizar el patrón `context.bindings.name` completo. En el ejemplo siguiente se muestra cómo acceder a los objetos `req` y `res` en `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Desde los enlaces de entrada y salida con nombre.** De esta manera, el desencadenador HTTP y los enlaces funcionan igual que cualquier otro enlace. En el ejemplo siguiente se establece el objeto de respuesta mediante un enlace `response` con nombre: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Solo respuesta]_ Llamando a `context.res.send(body?: any)`.** Se crea una respuesta HTTP con la entrada `body` como cuerpo de la respuesta. Se llama a `context.done()` implícitamente.

+ **_[Solo respuesta]_ Llamando a `context.done()`.** Un tipo especial de enlace HTTP que devuelve la respuesta que se pasa al método `context.done()`. El enlace de salida HTTP siguiente define un parámetro de salida `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versión de Node

En la tabla siguiente se muestra la versión de Node.js que se usa en cada versión principal del entorno de tiempo de ejecución de Functions:

| Versión de Functions | Versión de Node.js | 
|---|---|
| 1.x | 6.11.2 (bloqueado por el entorno de tiempo de ejecución) |
| 2.x  | _Active LTS_ y las versiones _actuales_ de Node.js con la numeración correspondiente (se recomienda 8.11.1 y 10.14.1). Establezca la versión con la [configuración de aplicación](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

Puede ver la versión actual que el entorno de tiempo de ejecución usa consultando la configuración de aplicación anterior o mediante la impresión de `process.version` desde cualquier función.

## <a name="dependency-management"></a>Administración de dependencias
Para poder utilizar bibliotecas de la comunidad en el código de JavaScript, como se muestra en el ejemplo siguiente, debe asegurarse de que todas las dependencias estén instaladas en Function App en Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Debe definir un archivo `package.json` en la raíz de Function App. La definición del archivo permite que todas las funciones de la aplicación compartan los mismos paquetes almacenados en caché, de tal manera que se ofrece el mejor rendimiento. Cuando hay conflictos con una versión, puede resolverlo mediante la adición de un archivo `package.json` en la carpeta de una función específica.  

Al implementar aplicaciones de función desde el control de código fuente, cualquier archivo `package.json` presente en el repositorio desencadenará un elemento `npm install` en su carpeta durante la implementación. Sin embargo, al implementarlas mediante el portal o la CLI, tendrá que instalar los paquetes manualmente.

Hay dos maneras de instalar paquetes en Function App: 

### <a name="deploying-with-dependencies"></a>Implementación con dependencias
1. Instale todos los paquetes necesarios localmente mediante la ejecución de `npm install`.

2. Implemente el código y asegúrese de que la carpeta `node_modules` se incluye en la implementación. 


### <a name="using-kudu"></a>Mediante Kudu
1. Vaya a `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **Consola de depuración** > **CMD**.

3. Vaya a `D:\home\site\wwwroot` y luego arrastre el archivo package.json a la carpeta **wwwroot** en la mitad superior de la página.  
    También puede cargar archivos en Function App de otras formas. Para obtener más información, vea [Actualización de los archivos de aplicación de función](functions-reference.md#fileupdate). 

4. Una vez cargado el archivo package.json, ejecute el comando`npm install` en la **consola de ejecución remota de Kudu**.  
    Esta acción descarga los paquetes indicados en el archivo package.json y se reinicia Function App.

## <a name="environment-variables"></a>Variables de entorno

En Functions, [la configuración de la aplicación](functions-app-settings.md), como las cadenas de conexión del servicio, se exponen como variables de entorno durante la ejecución. Puede acceder a estos ajustes mediante `process.env`, como se muestra a continuación en la función `GetEnvironmentVariable`:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Cuando se ejecuta localmente, la configuración de la aplicación se lee desde el archivo del proyecto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="configure-function-entry-point"></a>Configuración del punto de entrada de la función

Las propiedades de `function.json` `scriptFile` y `entryPoint` pueden usarse para configurar la ubicación y el nombre de la función exportada. Estas propiedades pueden ser importantes si se transpila el código JavaScript.

### <a name="using-scriptfile"></a>Uso de `scriptFile`

De forma predeterminada, se ejecuta una función de JavaScript desde `index.js`, un archivo que comparte el mismo directorio primario que su archivo `function.json` correspondiente.

`scriptFile` se puede usar para obtener una estructura de carpetas que tenga el aspecto del ejemplo siguiente:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json` para `myNodeFunction` debe incluir una propiedad `scriptFile` que señala al archivo con la función exportada que ejecutar.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Uso de `entryPoint`

En `scriptFile` (o `index.js`), una función debe exportarse con `module.exports` para que se pueda encontrar y ejecutar. De forma predeterminada, la función que se ejecuta cuando se desencadena es la única exportación de ese archivo, la exportación denominada `run` o la exportación denominada `index`.

Se puede configurar mediante `entryPoint` en `function.json`, como en el ejemplo siguiente:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

En Functions 2.x, que admite el parámetro `this` en funciones de usuario, el código de la función podría ser similar al del ejemplo siguiente:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

En este ejemplo, es importante tener en cuenta que aunque se exporta un objeto, no hay ninguna garantía para conservar el estado entre ejecuciones.

## <a name="local-debugging"></a>Depuración local

Cuando se inicia con la `--inspect` parámetro, un proceso de Node.js escucha para un cliente de depuración en el puerto especificado. En Azure Functions 2.x, puede especificar argumentos para pasar el proceso de Node.js que se ejecuta el código mediante la adición de la variable de entorno o configuración de la aplicación `languageWorkers:node:arguments = <args>`. 

Para depurar de forma local, agregue `"languageWorkers:node:arguments": "--inspect=5858"` en `Values` en su [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) de archivos y adjuntar un depurador al puerto 5858.

Al depurar con VS Code, la `--inspect` parámetro se agrega automáticamente mediante el `port` valor en el archivo del proyecto launch.json.

En la versión 1.x, establecer `languageWorkers:node:arguments` no funcionará. Se puede seleccionar el puerto de depuración con el [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parámetro en Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Cuando el destino versión 2.x de Functions runtime, ambos [Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md) y [Azure Functions Core Tools](functions-run-local.md) le permiten crear aplicaciones de función mediante una plantilla que admiten Proyectos de aplicación de función de TypeScript. La plantilla genera `package.json` y `tsconfig.json` archivos de proyecto que resulte más fácil para transpilar, ejecutar y publicar las funciones de JavaScript desde el código de TypeScript con estas herramientas.

Una generada `.funcignore` archivo se usa para indicar qué archivos se excluyen cuando se publica un proyecto en Azure.  

Archivos de TypeScript (TS) están transpilado a archivos JavaScript (.js) en el `dist` directorio de salida. Usan plantillas de TypeScript el [ `scriptFile` parámetro](#using-scriptfile) en `function.json` para indicar la ubicación del archivo .js correspondiente en el `dist` carpeta. La ubicación de salida se establece mediante la plantilla mediante `outDir` parámetro en el `tsconfig.json` archivo. Si cambia esta configuración o el nombre de la carpeta, el tiempo de ejecución no es capaz de encontrar el código que se ejecutará.

> [!NOTE]
> Compatibilidad experimental con TypeScript existe versión 1.x de Functions runtime. Los archivos de TypeScript en archivos de JavaScript cuando se invoca la función de versión experimental transpiles. En la versión 2.x, esta compatibilidad experimental se ha sustituido por el método controlado por la herramienta que no transpilación antes de que se inicializa el host y durante el proceso de implementación.

La manera en que desarrolla e implementa desde un proyecto de TypeScript localmente depende de la herramienta de desarrollo.

### <a name="visual-studio-code"></a>Visual Studio Code

El [Azure Functions para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extensión le permite desarrollar las funciones con TypeScript. Core Tools es un requisito de la extensión de Azure Functions.

Para crear una aplicación de función de TypeScript en Visual Studio Code, basta con elegir `TypeScript` al crear una aplicación de función y se le pide que elija el idioma.

Al presionar **F5** para ejecutar la aplicación localmente, transpilación se realiza antes de inicializa el host (func.exe). 

Al implementar la aplicación de función en Azure mediante el **implementar en la aplicación de función...**  botón, la extensión de Azure Functions genera primero una compilación lista para producción de archivos JavaScript desde los archivos de origen de TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Para crear un proyecto de aplicación de función de TypeScript con herramientas de Core, debe especificar la opción de lenguaje typescript al crear la aplicación de función. Puede hacerlo en una de las maneras siguientes:

- Ejecute el `func init` comandos, seleccione `node` como la pila de lenguaje y, a continuación, seleccione `typescript`.

- Ejecute el comando `func init --worker-runtime typescript`.

Para ejecutar el código de aplicación de función localmente mediante herramientas de Core, use el `npm start` comando, en lugar de `func host start`. El `npm start` comando es equivalente a los siguientes comandos:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Antes de usar el [ `func azure functionapp publish` ] de comandos para implementar en Azure, primero debe ejecutar el `npm run build:production` comando. Este comando crea una compilación lista para producción de archivos JavaScript desde los archivos de origen de TypeScript que se pueden implementar mediante [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Consideraciones para las funciones de JavaScript

Cuando se trabaja con las funciones de JavaScript, tenga en cuenta las consideraciones de las secciones siguientes.

### <a name="choose-single-vcpu-app-service-plans"></a>Elección de los planes de App Service de una sola vCPU

Al crear una aplicación de función que usa el plan de App Service, se recomienda que seleccione un plan de una sola vCPU, en lugar de un plan con varias vCPU. En la actualidad, Functions ejecuta funciones de JavaScript con más eficacia en VM con una sola vCPU; el uso de máquinas virtuales más grandes no produce las mejoras de rendimiento esperadas. Cuando sea necesario, manualmente, puede escalar horizontalmente agregando más instancias de máquina virtual única vCPU, o puede habilitar el escalado automático. Para obtener más información, consulte [Escalación del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Arranque en frío

Al desarrollar Azure Functions en el modelo de hospedaje sin servidor, los arranques en frío son una realidad. *Arranque en frío* hace referencia al hecho de que cuando se inicia Function App por primera vez tras un período de inactividad, tarda más tiempo en iniciarse. Especialmente para las funciones de JavaScript con árboles de dependencias de gran tamaño, el arranque en frío puede ser importante. Para acelerar el proceso de arranque en frío, [ejecute sus funciones como un archivo de paquete](run-functions-from-deployment-package.md) cuando sea posible. Muchos métodos de implementación utilizan el modelo de ejecución desde paquete de forma predeterminada, pero si experimenta arranques en frío prolongados y no ejecuta sus funciones de este modo, este cambio puede mejorar la situación considerablemente.

### <a name="connection-limits"></a>Límites de conexiones

Cuando se usa un cliente específico de servicio en una aplicación de Azure Functions, no cree a un nuevo cliente con cada invocación de función. En su lugar, cree a un cliente único y estático en el ámbito global. Para obtener más información, consulte [administrar conexiones en Azure Functions](manage-connections.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

+ [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
+ [Referencia para desarrolladores de Azure Functions](functions-reference.md)
+ [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)

['func azure functionapp publish']: functions-run-local.md#project-file-deployment
