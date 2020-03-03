---
title: 'Inicio rápido: Biblioteca cliente de Text Analytics para C# | Microsoft Docs'
description: Introducción a la biblioteca cliente de Text Analytics para C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/24/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 81d69f2274abf075be2f97b0edc67af2eea62327
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614477"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)

[Documentación de referencia de v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Código fuente de la biblioteca v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Paquete v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Ejemplos de v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

[Documentación de referencia de v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Código fuente de la biblioteca v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Paquete v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Ejemplos de v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
* [IDE de Visual Studio](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-net-core-application"></a>Creación de una aplicación de .NET Core

Utilice el IDE de Visual Studio para crear una aplicación de consola de .NET Core. Así se creará un proyecto "Hola mundo" con un solo archivo de origen de C#: *program.cs*.

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)

Instale la biblioteca cliente, para lo que debe hacer clic con el botón derecho en la solución en el **Explorador de soluciones** y seleccionar **Administrar paquetes NuGet**. En el administrador de paquetes que se abre, seleccione **Examinar** **Incluir versión preliminar** y busque `Azure.AI.TextAnalytics`. Seleccione la versión `1.0.0-preview.2` e **Instalar**. También puede usar la [Consola del Administrador de paquetes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Abra el archivo *program.cs* y agregue las siguientes `using` directivas:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

En la clase `Program` de la aplicación, cree variables para el punto de conexión y la clave del recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Reemplace el método de `Main` de la aplicación. Más adelante definirá los métodos a los que llama aquí.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

Instale la biblioteca cliente, para lo que debe hacer clic con el botón derecho en la solución en el **Explorador de soluciones** y seleccionar **Administrar paquetes NuGet**. En el administrador de paquetes que se abre, seleccione **Examinar** y busque `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Haga clic en él y, después, en **Instalar**. También puede usar la [Consola del Administrador de paquetes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Abra el archivo *program.cs* y agregue las siguientes `using` directivas:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

En la clase `Program` de la aplicación, cree variables para el punto de conexión y la clave del recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Reemplace el método de `Main` de la aplicación. Más adelante definirá los métodos a los que llama aquí.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objetos

El cliente de Text Analytics es un objeto `TextAnalyticsClient` que se autentica en Azure mediante su clave y que proporciona funciones para aceptar texto como cadenas individuales o como un lote. Puede enviar texto a la API de forma sincrónica o asincrónica. El objeto de respuesta contendrá la información del análisis de todos los documentos que envíe. 

Si va a usar la versión `3.0-preview`, puede emplear una instancia de `TextAnalyticsClientOptions` opcional para inicializar el cliente con distintas configuraciones predeterminadas (por ejemplo, el idioma predeterminado o la sugerencia de país). También puede autenticarse mediante un token de Azure Active Directory. 

## <a name="code-examples"></a>Ejemplos de código

* [Análisis de opiniones](#sentiment-analysis)
* [Detección de idioma](#language-detection)
* [Reconocimiento de entidades con nombre](#named-entity-recognition-ner)
* [Detección de información personal](#detect-personal-information)
* [Vinculación de entidad](#entity-linking)
* [Extracción de frases clave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar el cliente

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)

Asegúrese de que el método main anterior crea un objeto de cliente con el punto de conexión y las credenciales.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

Cree una clase `ApiKeyServiceClientCredentials` para almacenar las credenciales y agregarlas a las solicitudes del cliente. Dentro de ella, cree una invalidación para `ProcessHttpRequestAsync()` que agregue su clave al encabezado `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Cree un método para crear una instancia del objeto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) con el punto de conexión y un objeto `ApiKeyServiceClientCredentials` que contiene la clave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>análisis de opiniones

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)

Cree una función denominada `SentimentAnalysisExample()` que tome el cliente que creó anteriormente y llame a su función `AnalyzeSentiment()`. El objeto `Response<DocumentSentiment>` devuelto contendrá la etiqueta de opinión y la puntuación del documento de entrada completo, así como un análisis de opinión de cada oración si la operación se realiza correctamente. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.Offset, sentence.Length)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.SentimentScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.SentimentScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.SentimentScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

Cree una función denominada `SentimentAnalysisExample()` que tome el cliente que creó anteriormente y llame a su función [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). El objeto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) que se devuelve contendrá la opinión `Score` si la operación se ha realizado correctamente, y `errorMessage` si no ha sido así. 

Una puntuación cercana a 0 indica una opinión negativa, mientras que una puntuación cercana a 1 indica una opinión positiva.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Detección de idiomas

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)


Cree una función denominada `LanguageDetectionExample()` que tome el cliente que creó anteriormente y llame a su función `DetectLanguage()`. El objeto `Response<DetectedLanguage>` devuelto contendrá el idioma detectado junto con su nombre y el código ISO-6391. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`.

> [!Tip]
> En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `countryHint` para especificar un código de país de dos letras. De forma predeterminada, la API usa "US" como valor predeterminado de countryHint; para eliminar este comportamiento, puede restablecer este parámetro y configurarlo como una cadena `countryHint = ""` vacía. Para establecer un valor predeterminado diferente, establezca la propiedad `TextAnalyticsClientOptions.DefaultCountryHint` y pásela durante la inicialización del cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

Cree una función denominada `languageDetectionExample()` que tome el cliente que creó anteriormente y llame a su función [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). El objeto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) devuelto contendrá la lista de idiomas detectados en `DetectedLanguages` si la operación se ha realizado correctamente, y `errorMessage` si no ha sido así. Imprima el primer idioma devuelto.

> [!Tip]
> En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `countryHint` para especificar un código de país de dos letras. De forma predeterminada, la API usa "US" como valor de countryHint predeterminado; para quitar este comportamiento, puede restablecer este parámetro y configurarlo como una cadena `countryHint = ""` vacía.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Output

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Reconocimiento de entidades con nombre (NER)

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)


> [!NOTE]
> Nuevo en la versión `3.0-preview`:
> * El reconocimiento de entidades ahora incluye la capacidad de detectar información personal en texto.
> * La vinculación de entidad ahora es independiente del reconocimiento de entidades.


Cree una función denominada `EntityRecognitionExample()` que tome el cliente que creó anteriormente, llame a su función `RecognizeEntities()` y recorra en iteración los resultados. El objeto `Response<IReadOnlyCollection<CategorizedEntity>>` devuelto contendrá la lista de las entidades detectadas. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Offset: 26,     Length: 7,      Score: 0.920

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="detect-personal-information"></a>Detección de información personal

Cree una función denominada `EntityPIIExample()` que tome el cliente que creó anteriormente, llame a su función `RecognizePiiEntities()` y recorra en iteración los resultados. Igual que en la función anterior, el objeto `Response<IReadOnlyCollection<CategorizedEntity>>` devuelto contendrá la lista de las entidades detectadas. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category: None
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Vinculación de entidad

Cree una función denominada `EntityLinkingExample()` que tome el cliente que creó anteriormente, llame a su función `RecognizeLinkedEntities()` y recorra en iteración los resultados. El `Response<IReadOnlyCollection<LinkedEntity>>` devuelto representa la lista de las entidades detectadas. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`. Puesto que las entidades vinculadas se identifican de forma única, las apariciones de la misma entidad se agrupan bajo un objeto `LinkedEntity` como una lista de objetos `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

> [!NOTE]
> En la versión 2.1, la vinculación de entidad se incluye en la respuesta de NER.

Cree una función denominada `RecognizeEntitiesExample()` que tome el cliente que creó anteriormente y llame a su función [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Recorra en iteración los resultados. El objeto [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) devuelto contendrá la lista de entidades detectadas en `Entities` si la operación se ha realizado correctamente, y `errorMessage` si no ha sido así. Para cada identidad detectada, imprima su tipo, subtipo y nombre de Wikipedia (si existen) y las ubicaciones del texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extracción de la frase clave

#### <a name="version-30-preview"></a>[Versión 3.0 (versión preliminar)](#tab/version-3)

Cree una función denominada `KeyPhraseExtractionExample()` que tome el cliente que creó anteriormente y llame a su función `ExtractKeyPhrases()`. El objeto `<Response<IReadOnlyCollection<string>>` devuelto contendrá la lista de las frases clave detectadas. Si se produjeron errores, se producirá lo siguiente: `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Versión 2.1](#tab/version-2)

Cree una función denominada `KeyPhraseExtractionExample()` que tome el cliente que creó anteriormente y llame a su función [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). El resultado contendrá la lista de frases clave detectadas en `KeyPhrases` si la operación ha tenido éxito, o `errorMessage` si no. Imprima las frases clave detectadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

---
