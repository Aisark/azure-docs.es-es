---
title: 'Inicio rápido: Traducción de texto, C# (Translator Text)'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido se traduce texto de un idioma a otro mediante Translator Text API con C#.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: fc52512f48050c46894996d5a79b26f36fc6ad6c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183839"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-c"></a>Inicio rápido: Uso de Translator Text API para traducir una cadena mediante C#

En esta guía de inicio rápido, aprenderá a traducir una cadena de texto de inglés a italiano y alemán con .NET Core y Translator Text REST API.

En esta guía de inicio rápido, se requiere una [cuenta de Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con un recurso de Translator Text. Si no tiene una cuenta, puede usar la [evaluación gratuita](https://azure.microsoft.com/try/cognitive-services/) para obtener una clave de suscripción.

## <a name="prerequisites"></a>Requisitos previos

* [SDK de .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Paquete NuGet de Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o su editor favorito de código.
* Una clave de suscripción de Azure para Translator Text

## <a name="create-a-net-core-project"></a>Creación de un proyecto de .NET Core

Abra un nuevo símbolo del sistema (o una sesión de terminal) y ejecute estos comandos:

```console
dotnet new console -o translate-sample
cd translate-sample
```

El primer comando hace dos cosas. Crea una nueva aplicación de consola .NET y crea un directorio denominado `translate-sample`. El segundo comando cambia al directorio del proyecto.

A continuación, deberá instalar Json.Net. En el directorio del proyecto, ejecute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Incorporación de los espacios de nombres necesarios al proyecto

El comando `dotnet new console` que ejecutó anteriormente creó un proyecto que incluía `Program.cs`. En este archivo es donde deberá colocar el código de la aplicación. Abra `Program.cs` y reemplace las instrucciones using existentes. Estas instrucciones garantizan que tiene acceso a todos los tipos necesarios para compilar y ejecutar la aplicación de ejemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-translate-text"></a>Creación de una función para traducir texto

Dentro de la clase `Program`, cree una función denominada `TranslateText`. Esta clase encapsula el código que se usa para llamar al recurso Translate e imprime el resultado en la consola.

```csharp
static void TranslateText()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Establecimiento de la clave de suscripción, nombre de host y la ruta de acceso

Agregue estas líneas a la función `TranslateText`. Observe que junto con la `api-version`, se han anexado dos parámetros adicionales a `route`. Estos parámetros se usan para establecer las salidas de la traducción. En este ejemplo, está establecida en alemán (`de`) e italiano (`it`). Asegúrese de actualizar el valor de la clave de suscripción.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/translate?api-version=3.0&to=de&to=it";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

A continuación, se debe crear y serializar el objeto JSON que incluye el texto que desea traducir. Tenga en cuenta que puede pasar más de un objeto en la matriz `body`.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Hello world!" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Creación de una instancia de cliente y realización de una solicitud

Estas líneas crean instancias de `HttpClient` y `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construcción de la solicitud e impresión de la respuesta

Dentro de `HttpRequestMessage`:

* Declarará el método HTTP
* Construirá el URI de solicitud
* Insertará el cuerpo de la solicitud (el objeto JSON serializado)
* Agregará los encabezados necesarios
* Realizará una solicitud asincrónica
* Impresión de la respuesta

Agregue este código a `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Colocación de todo junto

El último paso consiste en llamar a `TranslateText()` en la función `Main`. Busque `static void Main(string[] args)` y agregue estas líneas:

```csharp
TranslateText();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Eso es todo, ya está listo para ejecutar la aplicación de ejemplo. Desde la línea de comandos (o sesión de terminal), vaya al directorio del proyecto y ejecute:

```console
dotnet run
```

## <a name="sample-response"></a>Respuesta de muestra

Busque la abreviatura del país en esta [lista de idiomas](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Asegúrese de quitar cualquier información confidencial del código fuente de la aplicación de ejemplo como, por ejemplo, las claves de suscripción.

## <a name="next-steps"></a>Pasos siguientes

Explore el código de ejemplo de esta guía de inicio rápido y otros, incluida la transliteración y la identificación del idioma, así como otros proyectos de Translator Text de ejemplo de GitHub.

> [!div class="nextstepaction"]
> [Explorar ejemplos de C# en GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Otras referencias

* [Transliterar texto](quickstart-csharp-transliterate.md)
* [Identificar el idioma de entrada](quickstart-csharp-detect.md)
* [Obtener traducciones alternativas](quickstart-csharp-dictionary.md)
* [Obtener una lista de idiomas admitidos](quickstart-csharp-languages.md)
* [Determinar la longitud de las oraciones de una entrada](quickstart-csharp-sentences.md)
