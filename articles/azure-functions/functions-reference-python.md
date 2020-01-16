---
title: Referencia para desarrolladores de Python para Azure Functions
description: Aprenda a desarrollar funciones con Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 55eb1fe53aa4256f1b7eee44547703328816cd32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75409084"
---
# <a name="azure-functions-python-developer-guide"></a>Guía de Azure Functions para desarrolladores de Python

Este artículo es una introducción al desarrollo de Azure Functions mediante Python. En lo que va a leer a continuación se supone que ya ha leído la [guía para desarrolladores de Azure Functions](functions-reference.md). 

Para ver los proyectos de ejemplo de funciones independientes de Python, examine los [ejemplos de funciones de Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modelo de programación

Azure Functions espera que una función sea un método sin estado de un script de Python que procese entradas y genere salidas. De forma predeterminada, el runtime espera que el modelo se implemente como un método global denominado `main()` en el archivo `__init__.py`. También puede [especificar un punto de entrada alternativo](#alternate-entry-point).

Los datos de los desencadenadores y enlaces se enlazan a la función a través de los atributos del método con la propiedad `name` definida en el archivo *function.json*. Por ejemplo, en el archivo _function.json_ siguiente se describe una función simple desencadenada por una solicitud HTTP denominada `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

El archivo `__init__.py` contiene el código de función siguiente:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

también puede declarar explícitamente los tipos de parámetros y el tipo de valor devuelto de la función mediante las anotaciones de tipos de Python. Esto le ayuda a usar las características de IntelliSense y Autocompletar proporcionadas por muchos editores de código de Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilice las anotaciones de Python incluidas en el paquete [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para enlazar las entradas y las salidas a los métodos.

## <a name="alternate-entry-point"></a>Punto de entrada alternativo

Puede cambiar el comportamiento predeterminado de una función si especifica opcionalmente las propiedades `scriptFile` y `entryPoint` en el archivo *function.json*. Por ejemplo, el archivo _function.json_ siguiente indica al runtime que use el método `customentry()` del archivo  _main.py_, como punto de entrada para la instancia de Azure Functions.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Estructura de carpetas

La estructura de carpetas recomendada para un proyecto de Python en Azure Functions tiene la siguiente apariencia:

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
La carpeta de proyecto principal (\_\_app\_\_) puede contener los siguientes archivos:

* *local.settings.json*: se usa para almacenar la configuración y las cadenas de conexión de la aplicación cuando se ejecuta localmente. Este archivo no se publica en Azure. Para más información, consulte [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: contiene la lista de paquetes que se instalan al publicar en Azure.
* *host.json*: contiene las opciones de configuración global que afectan a todas las funciones de una aplicación de funciones. Este archivo se publica en Azure. No todas las opciones se admiten cuando se ejecuta localmente. Para más información, consulte [host.json](functions-host-json.md).
* *funcignore*: (opcional) declara los archivos que no deben publicarse en Azure.
* *gitignore*: (opcional) declara los archivos que se excluyen de un repositorio de Git, como local.settings.json.

Cada función tiene su propio archivo de código y archivo de configuración de enlace (function.json). 

El código compartido debe mantenerse en una carpeta independiente en \_\_app\_\_. Para hacer referencia a los módulos en la carpeta SharedCode, puede usar la sintaxis siguiente:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Para hacer referencia a los módulos locales de una función, puede usar la sintaxis de importación relativa de la siguiente manera:

```python
from . import example
```

Al implementar el proyecto en una aplicación de funciones de Azure, debe incluirse en el paquete todo el contenido de la carpeta *FunctionApp*, pero no la propia carpeta. Se recomienda mantener las pruebas en una carpeta independiente de la carpeta del proyecto; en este ejemplo `tests`. Esto evita que se implemente código de prueba con la aplicación. Para más información, consulte [Pruebas unitarias](#unit-testing).

## <a name="triggers-and-inputs"></a>Desencadenadores y entradas

Las entradas se dividen en dos categorías dentro de Azure Functions: una entrada del desencadenador y otra adicional. Aunque son diferentes en el archivo `function.json`, se usan igual en el código de Python.  Las cadenas de conexión o los secretos de los orígenes de entrada y el desencadenador se asignan a valores en el archivo `local.settings.json` al ejecutarse localmente y a la configuración de la aplicación al ejecutarse en Azure. 

Por ejemplo, el siguiente código muestra la diferencia entre las dos:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Cuando se invoca la función, la solicitud HTTP se pasa a la función como `req`. Se recuperará una entrada de Azure Blob Storage según el _identificador_ de la dirección URL de la ruta y estará disponible como `obj` en el cuerpo de la función.  Aquí, la cuenta de almacenamiento especificada es la cadena de conexión encontrada, que es la misma cuenta de almacenamiento que usa la aplicación de funciones.


## <a name="outputs"></a>Salidas

Las salidas se pueden expresar como valores devueltos y como parámetros de salida. Si hay una única salida, se recomienda usar el valor devuelto. Para varias salidas, deberá utilizar parámetros de salida.

Para usar el valor devuelto de una función como valor de un enlace de salida, la propiedad `name` del enlace debe establecerse como `$return` en `function.json`.

Si desea generar varias salidas, utilice el método `set()` que la interfaz [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) ofrece para asignar un valor al enlace. Por ejemplo, la siguiente función puede insertar un mensaje en una cola y también devolver una respuesta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registro

El acceso al registrador del entorno de ejecución de Azure Functions está disponible a través de un controlador [`logging`](https://docs.python.org/3/library/logging.html#module-logging) raíz en la aplicación de función. Este registrador está asociado a Application Insights y permite marcar las advertencias y los errores detectados durante la ejecución de la función.

En el ejemplo siguiente se registra un mensaje de información cuando la función se invoca con un desencadenador HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Hay métodos de registro adicionales disponibles que permiten escribir en la consola en otros niveles de seguimiento:

| Método                 | Descripción                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Escribe un mensaje con el nivel CRÍTICO en el registrador de raíz.  |
| **`error(_message_)`**   | Escribe un mensaje con el nivel ERROR en el registrador de raíz.    |
| **`warning(_message_)`**    | Escribe un mensaje con el nivel ADVERTENCIA en el registrador de raíz.  |
| **`info(_message_)`**    | Escribe un mensaje con el nivel INFO en el registrador de raíz.  |
| **`debug(_message_)`** | Escribe un mensaje con el nivel DEBUG en el registrador de raíz.  |

Para más información sobre el registro, consulte [Supervisión de Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Desencadenadores y enlaces HTTP

El desencadenador HTTP se define en el archivo function.json. El valor de `name` del enlace debe coincidir con el parámetro con nombre de la función. En los ejemplos anteriores, se usa un nombre de enlace `req`. Este parámetro es un objeto [HttpRequest] y se devuelve un objeto [HttpResponse].

Desde el objeto [HttpRequest], puede obtener encabezados de solicitud, parámetros de consulta, parámetros de ruta y el cuerpo del mensaje. 

El ejemplo siguiente es de la [plantilla de desencadenador HTTP para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

En esta función, el valor del parámetro de consulta `name` se obtiene del parámetro `params` del objeto [HttpRequest]. El cuerpo del mensaje con codificación JSON se lee mediante el método `get_json`. 

Del mismo modo, puede establecer `status_code` y `headers` para el mensaje de respuesta en el objeto [HttpResponse] devuelto.

## <a name="scaling-and-concurrency"></a>Escalado y simultaneidad

De forma predeterminada, Azure Functions supervisa automáticamente la carga en la aplicación y crea instancias de host adicionales para Python según sea necesario. Functions usa umbrales integrados (no configurables por el usuario) en diferentes tipos de desencadenadores para decidir cuándo se deben agregar instancias, como la antigüedad de los mensajes y el tamaño de la cola para QueueTrigger. Para más información, consulte [Cómo funcionan los planes de consumo y Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Este comportamiento de escalado es suficiente para muchas aplicaciones. Sin embargo, es posible que las aplicaciones con alguna de las siguientes características no se escalen de forma tan eficaz:

- La aplicación necesita administrar muchas invocaciones simultáneas.
- La aplicación procesa un gran número de eventos de E/S.
- La aplicación es dependiente de las operaciones de E/S.

En tales casos, puede mejorar aún más el rendimiento si emplea patrones asincrónicos y usa varios procesos de trabajo de lenguaje.

### <a name="async"></a>Async

Dado que Python es un entorno de ejecución de un solo subproceso, una instancia de host para Python solo puede procesar una invocación de función cada vez. En el caso de las aplicaciones que procesan un gran número de eventos de E/S o que son dependientes de las operaciones de E/S, puede mejorar el rendimiento mediante la ejecución de funciones de forma asincrónica.

Para ejecutar una función de forma asincrónica, use la instrucción `async def`, que ejecuta la función directamente con [asyncio](https://docs.python.org/3/library/asyncio.html):

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Una función sin la palabra clave `async` se ejecuta automáticamente en un grupo de subprocesos de asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Uso de procesos de trabajo de varios lenguajes

De forma predeterminada, cada instancia de host de Functions tiene un único proceso de trabajo de lenguaje. Puede aumentar el número de procesos de trabajo por host (hasta 10) mediante la configuración de la aplicación [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). Al hacerlo, Azure Functions intenta distribuir uniformemente las invocaciones de función simultáneas en estos trabajos. 

FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que Functions crea al escalar horizontalmente la aplicación para satisfacer la demanda. 

## <a name="context"></a>Context

Para obtener el contexto de invocación de una función durante la ejecución, incluya el argumento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) en su firma. 

Por ejemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La clase [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) tiene los atributos de cadena siguientes:

`function_directory`  
El directorio en que se ejecuta la función.

`function_name`  
El nombre de la función.

`invocation_id`  
El identificador de la invocación de la función actual.

## <a name="global-variables"></a>Variables globales

No se garantiza la conservación del estado de la aplicación para las ejecuciones futuras. Sin embargo, Azure Functions Runtime suele reutilizar el mismo proceso para varias ejecuciones de la misma aplicación. Para almacenar en caché los resultados de un cálculo costoso, debe declararse como variable global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variables de entorno

En Functions, la [configuración de la aplicación](functions-app-settings.md), como las cadenas de conexión del servicio, se exponen como variables de entorno durante la ejecución. Puede acceder a esta configuración mediante la declaración de `import os` y el uso de `setting = os.environ["setting-name"]`.

En el siguiente ejemplo se obtiene la [configuración de la aplicación](functions-how-to-use-azure-function-app-settings.md#settings), con la clave denominada `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para el desarrollo local, la configuración de la aplicación se [mantiene en el archivo local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versión de Python 

Actualmente, Azure Functions admite Python 3.6.x y 3.7.x (distribuciones oficiales de CPython). Cuando se ejecuta localmente, el entorno de ejecución usa la versión de Python disponible. Para solicitar una versión específica de Python al crear la aplicación de funciones en Azure, use la opción `--runtime-version` del comando [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create).  

## <a name="package-management"></a>Administración de paquetes

Al desarrollar localmente con Azure Functions Core Tools o Visual Studio Code, agregue los nombres y las versiones de los paquetes necesarios al archivo `requirements.txt` e instálelos mediante `pip`. 

Por ejemplo, se puede usar el comando de archivo y pip siguiente para instalar el paquete `requests` desde PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicación en Azure

Cuando esté preparado para la publicación, asegúrese de que todas las dependencias disponibles públicamente están incluidas en el archivo requirements.txt, que se encuentra en la raíz del directorio del proyecto. 

Los archivos de proyecto y las carpetas que se excluyen de la publicación, incluida la carpeta del entorno virtual, se enumeran en el archivo. funcignore.

Se admiten tres acciones de compilación para publicar el proyecto de Python en Azure:

+ Compilación remota: las dependencias se obtienen de forma remota en función del contenido del archivo requirements.txt. La [compilación remota](functions-deployment-technologies.md#remote-build) es el método de compilación recomendado. Esta es también la opción de compilación predeterminada de las herramientas de Azure. 
+ Compilación local: las dependencias se obtienen de forma local en función del contenido del archivo requirements.txt. 
+ Dependencias personalizadas: el proyecto usa paquetes que no están disponibles públicamente para nuestras herramientas. Esta opción requiere Docker.

Para crear sus dependencias y publicarlas mediante un sistema de entrega continua (CD), [use Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Compilación remota

De forma predeterminada, Azure Functions Core Tools solicita una compilación remota cuando se usa el siguiente comando [func azure functionapp publish](functions-run-local.md#publish) para publicar el proyecto de Python en Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Reemplace `<APP_NAME>` por el nombre de la aplicación de función de Azure.

La [extensión de Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) también solicita de forma predeterminada una compilación remota. 

### <a name="local-build"></a>Compilación local

Puede impedir que se lleve a cabo una compilación remota usando el siguiente comando [func azure functionapp publish](functions-run-local.md#publish) para publicar con una compilación local. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Reemplace `<APP_NAME>` por el nombre de la aplicación de función de Azure. 

Con la opción `--build local`, las dependencias del proyecto se leen del archivo requirements.txt y los paquetes dependientes se descargan e instalan localmente. Los archivos de proyecto y las dependencias se implementan desde el equipo local en Azure. Esto hace que se cargue un paquete de implementación más grande en Azure. Si, por alguna razón, Core Tools no puede obtener las dependencias del archivo requirements.txt, debe usar la opción de dependencias personalizadas para la publicación. 

### <a name="custom-dependencies"></a>Dependencias personalizadas

Si el proyecto usa paquetes que no están disponibles públicamente para nuestras herramientas, puede ponerlos a disposición de la aplicación colocándolos en el directorio \_\_app\_\_/.python_packages. Antes de la publicación, ejecute el siguiente comando para instalar las dependencias localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Al usar dependencias personalizadas, debe usar la opción de publicación `--no-build`, puesto que ya ha instalado las dependencias.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Reemplace `<APP_NAME>` por el nombre de la aplicación de función de Azure.

## <a name="unit-testing"></a>Pruebas unitarias

Las funciones escritas en Python se pueden probar como otro código de Python mediante marcos de pruebas. Para la mayoría de los enlaces, es posible crear un objeto de entrada ficticio creando una instancia de una clase adecuada a partir del paquete `azure.functions`. Dado que el paquete [`azure.functions`](https://pypi.org/project/azure-functions/) no está disponible inmediatamente, asegúrese de instalarlo a través del archivo `requirements.txt`, tal como se describe en la sección [Administración de paquetes](#package-management) anterior. 

Por ejemplo, a continuación se muestra una prueba ficticia de una función desencadenada por HTTP:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Este es otro ejemplo, con una función desencadenada por la cola:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Archivos temporales

El método `tempfile.gettempdir()` devuelve una carpeta temporal, que en Linux es `/tmp`. La aplicación puede usar este directorio para almacenar los archivos temporales generados y usados por las funciones durante la ejecución. 

> [!IMPORTANT]
> No se garantiza que los archivos escritos en el directorio temporal persistan entre invocaciones. Durante el escalado horizontal, los archivos temporales no se comparten entre instancias. 

En el ejemplo siguiente se crea un archivo temporal con nombre en el directorio temporal (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Se recomienda mantener las pruebas en una carpeta independiente de la carpeta del proyecto. Esto evita que se implemente código de prueba con la aplicación. 

## <a name="known-issues-and-faq"></a>Problemas conocidos y preguntas más frecuentes

Todos los problemas conocidos y las solicitudes de características se siguen mediante la lista de[problemas de GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Si le surge algún problema y no lo encuentra en GitHub, abra un nuevo problema e incluya una descripción detallada del mismo.

### <a name="cross-origin-resource-sharing"></a>Uso compartido de recursos entre orígenes

Azure Functions admite el uso compartido de recursos entre orígenes (CORS). CORS se configura [en el portal](functions-how-to-use-azure-function-app-settings.md#cors) y mediante la [CLI de Azure](/cli/azure/functionapp/cors). La lista de orígenes permitidos de CORS se aplica en el nivel de la aplicación de función. Con CORS habilitado, las respuestas incluyen el encabezado `Access-Control-Allow-Origin`. Para obtener más información, consulte [Uso compartido de recursos entre orígenes](functions-how-to-use-azure-function-app-settings.md#cors).

La lista de orígenes permitidos [no se admite actualmente](https://github.com/Azure/azure-functions-python-worker/issues/444) para las aplicaciones de función de Python. Debido a esta limitación, debe establecer expresamente el encabezado `Access-Control-Allow-Origin` en las funciones HTTP, tal y como se muestra en el ejemplo siguiente:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Asegúrese de actualizar también el archivo function.json para admitir el método HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

El explorador Chrome usa este método para negociar la lista de orígenes permitidos. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Documentación de la API del paquete de Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)
* [Enlaces de Blob Storage](functions-bindings-storage-blob.md)
* [Enlaces de HTTP y de Webhook](functions-bindings-http-webhook.md)
* [Enlaces de Queue Storage](functions-bindings-storage-queue.md)
* [Desencadenador de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
