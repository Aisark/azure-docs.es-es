---
title: 'Inicio rápido: Uso de C# para llamar a Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Obtenga información y ejemplos de código que le ayuden a empezar a usar rápidamente Text Analytics API.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: bc4553df239dbb8b62a31414539b10998cd74f02
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189655"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Inicio rápido: Uso de C# para llamar a Text Analytics de Cognitive Services
<a name="HOLTop"></a>

En este artículo se muestra cómo detectar el idioma, analizar las opiniones y extraer las frases clave mediante  [instancias de Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711)  con C#. El código se escribió para que funcione en una aplicación de .NET Core, con referencias mínimas a bibliotecas externas, por lo que también se podría ejecutar en Linux o MacOS.

Consulte las [definiciones de API](//go.microsoft.com/fwlink/?LinkID=759346) para obtener la documentación técnica de las API.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

También debe tener la [clave de acceso y punto de conexión](../How-tos/text-analytics-how-to-access-key.md) que se generó automáticamente durante el registro.

## <a name="install-the-nuget-sdk-package"></a>Instalación del paquete SDK de NuGet
1. Cree una solución de consola en Visual Studio.
1. Haga clic con el botón derecho en la solución y seleccione **Administrar paquetes NuGet para la solución**
1. Active la casilla **Incluir versión preliminar**.
1. Seleccione la pestaña **Examinar** y busque **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.
1. Seleccione el paquete NuGet e instálelo. Puede que necesite v2.8.0 por ahora (3-18-2019), en lugar de v3.0.0, hasta que se resuelva un error en el software.

> [!Tip]
>  Aunque puede llamar a los [puntos de conexión HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) directamente desde C#, el SDK de Microsoft.Azure.CognitiveServices.Language, hace que resulte más fácil llamar al servicio sin tener que preocuparse sobre la serialización y deserialización de JSON.
>
> Algunos vínculos útiles:
> - [Página del SDK de NuGet](<https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics>)
> - [Código del SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>Llamada a la API de Text Analytics mediante el SDK

1. Reemplace Program.cs por el código que se proporciona a continuación. En este programa, se muestran las funciones de Text Analytics API en tres secciones (extracción del idioma, extracción de frases clave y análisis de la opinión).
1. Reemplace el valor de encabezado `Ocp-Apim-Subscription-Key` por una clave de acceso válida para la suscripción.
1. Reemplace la región en `Endpoint`. Puede encontrar el punto de conexión en la sección de información general de los recursos de Text Analytics en [Azure Portal](<https://ms.portal.azure.com>). Incluya solo esta parte del punto de conexión: "https://[region].api.cognitive.microsoft.com".
1. Ejecute el programa.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Salida de la aplicación

La aplicación muestra la siguiente información:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Text Analytics con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Otras referencias

 [Introducción a Text Analytics](../overview.md) [Preguntas más frecuentes](../text-analytics-resource-faq.md)

