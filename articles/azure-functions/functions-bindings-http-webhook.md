---
title: Enlaces y desencadenadores HTTP de Azure Functions
description: Aprenda a usar desencadenadores y enlaces HTTP en Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: fb219316afa798dfda777c4d0d406d5b8cabf7f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472364"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Enlaces y desencadenadores HTTP de Azure Functions

En este artículo se explica cómo trabajar con desencadenadores y enlaces de salida HTTP en Azure Functions.

Es posible personalizar un desencadenador HTTP para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

El código en este artículo toma como valor predeterminado la sintaxis de Functions 2.x, que usa .NET Core. Para obtener información sobre la sintaxis de 1.x, consulte las [plantillas de Functions 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Paquetes: Functions 1.x

Los enlaces HTTP se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http), versión 1.x. El código fuente del paquete está en el repositorio [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) de GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paquetes: Functions 2.x

Los enlaces HTTP se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http), versión 3.x. El código fuente del paquete está en el repositorio [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) de GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Desencadenador

El desencadenador HTTP permite invocar una función con una solicitud HTTP. Puede usar un desencadenador HTTP para crear API sin servidor y responder a webhooks.

De forma predeterminada, un desencadenador HTTP devuelve HTTP 200 OK con un cuerpo vacío en Functions 1.x, o HTTP 204 No Content con un cuerpo vacío en Functions 2.x. Para modificar la respuesta, configure un [enlace de salida HTTP](#output).

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP. Tenga en cuenta que el valor devuelto se utiliza para el enlace de salida, pero no se requiere un atributo de valor devuelto.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de script de C# que se enlaza a `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Puede enlazar a un objeto personalizado en lugar de `HttpRequest`. Este objeto se crea a partir del cuerpo de la solicitud y se analiza como JSON. Del mismo modo, puede pasarse un tipo al enlace de la salida de respuesta HTTP y se devuelve como el cuerpo de la respuesta, con el código de estado 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Desencadenador: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de F#:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Necesita un archivo `project.json` que use NuGet para hacer referencia a los ensamblados `FSharp.Interop.Dynamic` y `Dynamitey`, como se muestra en el ejemplo siguiente:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Este es el archivo *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### <a name="trigger---python-example"></a>Ejemplo de desencadenador de Python

En el ejemplo siguiente se muestra un enlace de desencadenador en un archivo *function.json* y una [función de Python](functions-reference-python.md) que usa el enlace. La función busca un parámetro `name` en la cadena de consulta o en el cuerpo de la solicitud HTTP.

Este es el archivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de Python:

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Desencadenador: ejemplos de Java

* [Leer el parámetro de la cadena de consulta](#read-parameter-from-the-query-string-java)
* [Leer el cuerpo de una solicitud POST](#read-body-from-a-post-request-java)
* [Leer el parámetro de una ruta](#read-parameter-from-a-route-java)
* [Leer el cuerpo POJO de una solicitud POST](#read-pojo-body-from-a-post-request-java)

En el ejemplo siguiente se muestra un enlace de desencadenador HTTP de un archivo *function.json* y las [funciones de Java](functions-reference-java.md) correspondientes que usan el enlace. 

Este es el archivo *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Leer el parámetro de la cadena de consulta (Java)  

En este ejemplo se lee un parámetro, denominado ```id```, desde la cadena de consulta, y se usa para generar un documento JSON devuelto al cliente, con el tipo de contenido ```application/json```. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Leer el cuerpo de una solicitud POST (Java)  

En este ejemplo se lee el cuerpo de una solicitud POST, como ```String```, y se usa para generar un documento JSON devuelto al cliente, con el tipo de contenido ```application/json```.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Leer el parámetro de una ruta (Java)  

En este ejemplo se lee un parámetro obligatorio, denominado ```id```, y un parámetro opcional ```name``` de la ruta de acceso, y se usa para generar un documento JSON devuelto al cliente, con el tipo de contenido ```application/json```. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Leer el cuerpo POJO de una solicitud POST (Java)  

Este es el código para la clase ```ToDoItem```, a la que se hace referencia en este ejemplo:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

En este ejemplo se lee el cuerpo de una solicitud POST. El cuerpo de la solicitud se deserializa automáticamente en un objeto ```ToDoItem``` y se devuelve al cliente, con el tipo de contenido ```application/json```. El parámetro ```ToDoItem``` se serializa en runtime de Functions, ya que está asignado a la propiedad ```body``` de la clase ```HttpMessageResponse.Builder```.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

Puede establecer el nivel de autorización y los métodos HTTP permitidos en los parámetros del constructor de atributo. Además, existen propiedades para la plantilla de ruta y el tipo de webhook. Para más información sobre estos valores, consulte [Desencadenador: configuración](#trigger---configuration). A continuación, se muestra un atributo `HttpTrigger` en una signatura de método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `HttpTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
| **type** | N/D| Requerida: se debe establecer en `httpTrigger`. |
| **dirección** | N/D| Requerida: se debe establecer en `in`. |
| **name** | N/D| Requerida: nombre de variable que se usa en el código de la función para la solicitud o el cuerpo de la solicitud. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina qué claves, si las hubiera, deben estar presentes en la solicitud para poder invocar a la función. El nivel de autorización puede ser uno de los siguientes: <ul><li><code>anonymous</code>: no se requiere ninguna clave de API.</li><li><code>function</code>: se requiere una clave de API específica de la función. Este es el valor predeterminado si no se proporciona ninguno.</li><li><code>admin</code>: se requiere la clave maestra.</li></ul> Para más información, consulte la sección sobre las [claves de autorización](#authorization-keys). |
| **methods** |**Métodos** | Una matriz de los métodos HTTP a los que responde la función. Si no se especifica, la función responde a todos los métodos HTTP. Consulte cómo [personalizar el punto de conexión HTTP](#customize-the-http-endpoint). |
| **route** | **Route** | Define la plantilla de ruta y controla las direcciones URL de solicitud a las que responde la función. El valor predeterminado es `<functionname>` si no se proporciona ninguno. Para más información, consulte cómo [personalizar el punto de conexión HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Compatible solo con la versión 1.x del runtime._<br/><br/>Configura el desencadenador HTTP para que actúe como un receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para el proveedor especificado. No establezca la propiedad `methods` si establece esta propiedad. El tipo de webhook puede ser uno de los valores siguientes:<ul><li><code>genericJson</code>: un punto de conexión de webhook de uso general sin lógica para un proveedor concreto. Este valor restringe las solicitudes a solo aquellas que usan HTTP POST y con el tipo de contenido `application/json`.</li><li><code>github</code>&mdash;La función responde a [webhooks de GitHub](https://developer.github.com/webhooks/). No use la propiedad _authLevel_ con webhooks de GitHub. Para más información, consulte la sección sobre webhooks de GitHub que aparece más adelante en este artículo.</li><li><code>slack</code>&mdash;La función responde a [webhooks de Slack](https://api.slack.com/outgoing-webhooks). No use la propiedad _authLevel_ con webhooks de Slack. Para más información, consulte la sección sobre webhooks de Slack que aparece más adelante en este artículo.</li></ul>|

## <a name="trigger---usage"></a>Desencadenador: uso

Para las funciones C# y F #, puede declarar que el tipo de entrada del desencadenador sea `HttpRequest` o un tipo personalizado. Si elige `HttpRequest`, obtiene acceso completo al objeto de solicitud. En un tipo personalizado, el runtime intenta analizar el cuerpo de la solicitud JSON para establecer las propiedades del objeto.

Para las funciones de JavaScript, el sistema en tiempo de ejecución de Funciones proporciona el cuerpo de la solicitud en lugar del objeto de solicitud. Para más información, consulte el [ejemplo de desencadenador de JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Personalización del punto de conexión HTTP

De forma predeterminada, al crear una función para un desencadenador HTTP, la función se puede dirigir con una ruta que tenga el siguiente formato:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Puede personalizar esta ruta mediante el parámetro opcional `route` del enlace de entrada del desencadenador HTTP. Por ejemplo, el siguiente archivo *function.json* define una propiedad `route` para un desencadenador HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Al usar esta configuración, ya se podrá dirigir la función con la ruta de abajo, en lugar de con la original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Esto permite que el código de la función admita dos parámetros en la dirección: _category_ e _id_. Puede usar cualquier [restricción de ruta de API web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) con sus parámetros. El siguiente código de función de C# emplea los dos parámetros.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

A continuación, encontrará el código de función de Node.js que usa los mismos parámetros de ruta.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

De forma predeterminada, todas las rutas de la función tienen el prefijo *api*. También puede personalizar o quitar el prefijo con la propiedad `http.routePrefix` del archivo [host.json](functions-host-json.md). En el ejemplo siguiente, se quita el prefijo de ruta *api* utilizando una cadena vacía del prefijo del archivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Uso de identidades de cliente

Si la aplicación de función está usando la [autenticación/autorización de App Service](../app-service/overview-authentication-authorization.md), puede ver información sobre los clientes autenticados desde el código. Esta información está disponible como [encabezados de solicitud insertados por la plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

También puede leer esta información desde los datos de enlace. Esta funcionalidad solo está disponible para el entorno de ejecución de Functions 2.x. Actualmente, también está disponible para lenguajes .NET.

En lenguajes .NET, esta información está disponible como [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal está disponible como parte del contexto de solicitud, tal como se muestra en el ejemplo siguiente:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Como alternativa, ClaimsPrincipal se puede incluir como un parámetro adicional en la firma de función:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Claves de autorización

Functions permite usar claves para dificultar el acceso a los puntos de conexión de función HTTP durante el desarrollo.  Un desencadenador HTTP estándar puede necesitar que haya una clave de API de este tipo en la solicitud. 

> [!IMPORTANT]
> Aunque las claves pueden ayudar a ofuscar los puntos de conexión HTTP durante el desarrollo, no están diseñadas como una manera de proteger un desencadenador HTTP en producción. Para obtener más información, vea [Proteger un punto de conexión HTTP en producción](#secure-an-http-endpoint-in-production).

> [!NOTE]
> En el runtime 1.x de Functions, los proveedores de webhooks pueden usar claves para autorizar solicitudes de varias maneras, según lo que admita el proveedor. Esto se trata en [Webhooks y claves](#webhooks-and-keys). La versión 2.x del runtime no incluye compatibilidad integrada con proveedores de webhooks.

Existen dos tipos de claves:

* **Claves de host**: estas claves se comparten entre todas las funciones dentro de la aplicación de función. Cuando se usan como una clave de API, permiten el acceso a cualquier función dentro de la aplicación de función.
* **Claves de función**: estas claves se aplican solo a las funciones específicas en las que se definen. Cuando se usan como una clave de API, solo permiten el acceso a esa función.

Para cada clave se usa un nombre fácilmente referenciable y hay una clave predeterminada (denominada "predeterminada") en el nivel de función y host. Las claves de función tienen prioridad sobre las claves de host. Si se definen dos claves con el mismo nombre, siempre se usa la clave de función.

Cada aplicación de función además tiene una **clave maestra** especial. Esta clave es una clave de host denominada `_master`, que proporciona acceso administrativo a las API en tiempo de ejecución. No se puede revocar esta clave. Al establecer un nivel de autorización `admin`, las solicitudes deben usar la clave maestra; cualquier otra clave da lugar a un error de autorización.

> [!CAUTION]  
> Debido a los permisos elevados de la aplicación de función otorgados por la clave maestra, no debe compartir esta clave con terceros ni distribuirla en aplicaciones cliente nativas. Tenga cuidado al elegir el nivel de autorización de administrador.

### <a name="obtaining-keys"></a>Obtención de claves

Las claves se almacenan como parte de la aplicación de función en Azure y se cifran en reposo. Para ver las claves, crear nuevas o asignarles nuevos valores, vaya a una de las funciones desencadenadas por HTTP de [Azure Portal](https://portal.azure.com) y seleccione **Administrar**.

![Administre las claves de función en el portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

No hay ninguna API compatible para obtener claves de función mediante programación.

### <a name="api-key-authorization"></a>Autorización de la clave de API

La mayoría de las plantillas de desencadenador HTTP requieren una clave de API en la solicitud. Por lo tanto, la solicitud HTTP normalmente se parece a la siguiente dirección URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

La clave se puede incluir en una variable de cadena de consulta denominada `code`, como arriba. También puede incluirse en un encabezado HTTP `x-functions-key`. El valor de la clave puede ser cualquier clave de función definida para la función o cualquier clave de host.

Puede permitir solicitudes anónimas, que no requieren claves. También puede exigir que se use la clave principal. Cambie el nivel de autorización predeterminado mediante la propiedad `authLevel` en el JSON de enlace. Para más información, consulte [Desencadenador: configuración](#trigger---configuration).

> [!NOTE]
> Cuando las funciones se ejecutan localmente, la autorización se deshabilita independientemente del valor del nivel de autenticación especificado. Después de publicar en Azure, se aplica el valor `authLevel` del desencadenador.



### <a name="secure-an-http-endpoint-in-production"></a>Proteger un punto de conexión HTTP en producción

Para proteger totalmente los puntos de conexión de función en producción, considere la posibilidad de implementar una de las siguientes opciones de seguridad de nivel de aplicación de función:

* Activar la autenticación o autorización de App Service para la aplicación de función. La plataforma App Service permite usar Azure Active Directory (AAD) y varios proveedores de identidades de terceros para autenticar a los clientes. Se puede usar para implementar reglas de autorización personalizadas para las funciones y permite trabajar con información de usuario desde el código de función. Para obtener más información, vea [Autenticación y autorización en Azure App Service](../app-service/overview-authentication-authorization.md) y [Uso de identidades de cliente](#working-with-client-identities).

* Usar Azure API Management (APIM) para autenticar las solicitudes. APIM proporciona una serie de opciones de seguridad de API para las solicitudes entrantes. Para obtener más información, vea [Directivas de autenticación de Azure API Management](../api-management/api-management-authentication-policies.md). Con APIM, puede configurar la aplicación de función de modo que solo acepte solicitudes de la dirección IP de la instancia de APIM. Para obtener más información, vea [Restricciones de las direcciones IP](ip-addresses.md#ip-address-restrictions).

* Implementar la aplicación de función en una instancia de Azure App Service Environment (ASE). ASE proporciona un entorno de hospedaje dedicado en el que ejecutar las funciones. ASE le permite configurar una puerta de enlace de front-end única que se puede usar para autenticar todas las solicitudes entrantes. Para obtener más información, vea [Configuración de un firewall de aplicaciones web (WAF) para entornos de App Service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Cuando use alguno de estos métodos de seguridad de nivel de aplicación de función, debe establecer el nivel de autenticación de función desencadenada por HTTP en `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> El modo de webhook solo está disponible para la versión 1.x del runtime de Functions. Este cambio se realizó para mejorar el rendimiento de los desencadenadores HTTP en la versión 2.x.

En la versión 1.x, las plantillas de webhook proporcionan una validación adicional para las cargas de webhook. En la versión 2.x, el desencadenador HTTP base todavía funciona y es el modo recomendado para webhooks. 

#### <a name="github-webhooks"></a>Webhooks de GitHub

Para responder a webhooks de GitHub, primero cree la función con un desencadenador HTTP y establezca la propiedad **webHookType** en `github`. Luego copie su dirección URL y clave de API en la página **Agregar webhook** del repositorio GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Webhooks de Slack

El webhook de Slack genera un token en lugar de permitirle especificarlo, por lo que debe configurar una clave específica de función con el token desde Slack. Consulte [Claves de autorización](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks y claves

La autorización de webhook se controla mediante el componente receptor de webhook, parte del desencadenador HTTP; el mecanismo varía según el tipo de webhook. Cada mecanismo se basa en una clave. De forma predeterminada, se usa la clave de función denominada "default". Para usar otra clave, configure el proveedor de webhook de modo que envíe el nombre de la clave con la solicitud de una de las siguientes maneras:

* **Cadena de consulta**: el proveedor pasa el nombre de la clave en el parámetro de la cadena de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Encabezado de solicitud**: el proveedor pasa el nombre de clave en el encabezado `x-functions-clientid`.

## <a name="trigger---limits"></a>Desencadenador: límites

La longitud de la solicitud HTTP está limitada a 100 MB (104 857 600 bytes) y la longitud de la dirección URL a 4 KB (4 096 bytes). El elemento `httpRuntime` del [archivo Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) especifica estos límites.

Si una función que usa el desencadenador HTTP no se completa en menos de 2,5 minutos, la puerta de enlace agota el tiempo de espera y devuelve un error HTTP 502. La función seguirá ejecutándose, pero no podrá devolver una respuesta HTTP. En el caso de funciones de ejecución prolongada, se recomienda que siga patrones asincrónicos y que devuelva una ubicación donde pueda hacer ping con el estado de la solicitud. Para más información sobre cuánto tiempo se puede ejecutar una función, consulte [Escalado y hospedaje: Plan de consumo](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](functions-host-json.md) contiene opciones de configuración que controlan el comportamiento de desencadenador HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Salida

Use el enlace de salida HTTP para responder al remitente de la solicitud HTTP. Este enlace requiere un desencadenador HTTP y le permite personalizar la respuesta asociada con la solicitud del desencadenador. Si no se proporciona un enlace de salida HTTP, un desencadenador HTTP devuelve HTTP 200 OK con un cuerpo vacío en Functions 1.x, o HTTP 204 No Content con un cuerpo vacío en Functions 2.x.

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*. En las bibliotecas de clases de C#, no hay ninguna propiedad de atributo que corresponda a estas propiedades *function.json*.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
| **type** |Se debe establecer en `http`. |
| **dirección** | Se debe establecer en `out`. |
|**name** | Nombre de la variable usado en el código de la función para la respuesta, o `$return` para usar el valor devuelto. |

## <a name="output---usage"></a>Uso de salidas

Para enviar una respuesta HTTP, use los patrones de respuesta estándar del lenguaje. En C# o un script de C#, haga que la función devuelva el tipo `IActionResult` o `Task<IActionResult>`. En C#, no se requiere un atributo de valor devuelto.

Para obtener respuestas de ejemplo, vea el [ejemplo de desencadenador](#trigger---example).

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
