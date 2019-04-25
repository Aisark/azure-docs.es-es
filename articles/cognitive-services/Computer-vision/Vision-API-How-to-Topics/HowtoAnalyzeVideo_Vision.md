---
title: 'Ejemplo: Análisis de vídeo en tiempo real - Computer Vision'
titlesuffix: Azure Cognitive Services
description: Obtenga información sobre cómo realizar análisis casi en tiempo real de fotogramas procedentes de una secuencia de vídeo en directo a través de Computer Vision API.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: feafb983a7b9e4aea6091753842b03a65ccd3ca5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001565"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Análisis de vídeos en tiempo real

En esta guía se demostrará cómo realizar el análisis casi en tiempo real de fotogramas procedentes de una secuencia de vídeo en directo. Los componentes básicos en un sistema de este tipo son:

- Adquirir fotogramas desde un origen de vídeo
- Seleccionar los fotogramas que se van a analizar
- Enviar estos fotogramas a la API
- Consumir cada resultado del análisis que se devuelve de la llamada API

Estos ejemplos están escritos en C#, y el código puede encontrarse en GitHub aquí: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>El enfoque

Hay varias formas de solucionar el problema de la ejecución del análisis casi en tiempo real en secuencias de vídeo. Empezaremos por esbozar estos tres enfoques en niveles cada vez mayores de sofisticación.

### <a name="a-simple-approach"></a>Un enfoque sencillo

El diseño más sencillo para un sistema de análisis casi en tiempo real es un bucle infinito, donde en cada iteración se toma un fotograma, se analiza y, a continuación, se consume el resultado:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Si nuestro análisis consistió en un algoritmo de cliente ligero, este enfoque sería adecuado. Sin embargo, cuando el análisis tiene lugar en la nube, la latencia que conlleva significa que una llamada API puede tardar varios segundos, durante los cuales no se capturan imágenes y el subproceso básicamente no hace nada. La velocidad de fotogramas máxima está limitada por la latencia de las llamadas API.

### <a name="parallelizing-api-calls"></a>Paralelización de llamadas API

Si bien un bucle simple de un único subproceso tiene sentido para un algoritmo de cliente ligero, no se ajusta bien a la latencia involucrada en las llamadas API de la nube. La solución a este problema es permitir que las llamadas API de larga ejecución se ejecuten en paralelo con la captura de fotogramas. En C#, se podría lograr esto usando paralelismo basado en tareas, por ejemplo:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Este método inicia cada análisis en una tarea independiente, que se puede ejecutar en segundo plano mientras se siguen capturando fotogramas nuevos. Esto evita bloquear el subproceso principal mientras se espera que se devuelva una llamada API; sin embargo, se han perdido algunas de las garantías que proporcionaba la versión sencilla: pueden producirse varias llamadas API en paralelo y los resultados podrían devolverse en el orden equivocado. Además, este método podría provocar que varios subprocesos entraran en la función ConsumeResult() al mismo tiempo, lo que podría ser peligroso si la función no es segura para subprocesos. Por último, este código simple no realiza un seguimiento de las tareas que se crean, por lo que las excepciones desaparecerán de manera silenciosa. Por lo tanto, el ingrediente final que se debe agregar es un subproceso de "consumidor" que realice un seguimiento de las tareas de análisis, produzca excepciones, elimine tareas de larga duración y asegure que los resultados se consuman en el orden correcto, uno a la vez.

### <a name="a-producer-consumer-design"></a>Un diseño de productor-consumidor

En el sistema final de "productor-consumidor", se cuenta con un subproceso productor que es similar al bucle infinito anterior. No obstante, en lugar de consumir los resultados del análisis en cuanto están disponibles, el productor simplemente coloca las tareas en una cola para realizarles un seguimiento.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

También se tiene un subproceso consumidor, que toma tareas de la cola, esperando a finalicen, y muestra el resultado, o bien genera la excepción que se produjo. Mediante el uso de la cola, es posible garantizar que resultados se consuman uno a la vez, en el orden correcto, sin limitar la velocidad de fotogramas máxima del sistema.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementación de la solución

### <a name="getting-started"></a>Introducción

Para que la aplicación esté en funcionamiento lo más rápido posible, se ha implementado el sistema descrito anteriormente, con la intención de que sea lo suficientemente flexible como para implementar muchos escenarios, a la vez de que sea fácil de usar. Para obtener acceso al código, vaya a [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

La biblioteca contiene la clase FrameGrabber, que implementa el sistema productor-consumidor descrito anteriormente para procesar fotogramas de vídeo desde una cámara web. El usuario puede especificar la forma exacta de la llamada API, y la clase utiliza eventos para comunicar al código de llamada cuándo se adquiere un nuevo fotograma, o cuándo hay disponible un resultado del análisis.

Para ilustrar algunas de las posibilidades, hay dos aplicaciones de ejemplo que utilizan la biblioteca. La primera es una aplicación de consola simple; una versión simplificada de esta se reproduce a continuación. Toma fotogramas de la cámara web predeterminada y los envía a Face API para la detección de caras.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

La segunda aplicación de ejemplo es un poco más interesante y le permite elegir a qué API llamar en los fotogramas de vídeo. En el lado izquierdo, la aplicación muestra una vista previa del vídeo en directo; en el lado derecho, muestra el resultado más reciente de la API superpuesto con el fotograma correspondiente.

En la mayoría de los modos, habrá un retraso visible entre el vídeo en directo a la izquierda y el análisis visualizado a la derecha. Este retraso es el tiempo necesario para realizar la llamada API. La excepción a esto es el modo "EmotionsWithClientFaceDetect", que realiza la detección de caras localmente en el equipo cliente utilizando OpenCV, antes de enviar las imágenes a Cognitive Services. Al hacerlo, se posible visualizar la cara detectada inmediatamente y, a continuación, actualizar las emociones posteriormente una vez que se devuelve la llamada API. Esto demuestra la posibilidad de un enfoque "híbrido", donde se puede realizar un procesamiento simple en el cliente y, a continuación, puede usarse Cognitive Services APIs para intensificar esto con un análisis más avanzado, cuando sea necesario.

![Captura de pantalla de la aplicación LiveCameraSample que muestra una imagen con etiquetas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integración en el código base

Para empezar a trabajar con este ejemplo, siga estos pasos:

1. Obtenga las claves de la API de Vision API de [Suscripciones](https://azure.microsoft.com/try/cognitive-services/). Para el análisis de fotogramas de vídeo, las API correspondientes son:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clone el repositorio [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) de GitHub.

3. Abra el ejemplo en Visual Studio 2015, compile y ejecute las aplicaciones de ejemplo:
    - Para BasicConsoleSample, la clave de Face API está codificada de forma rígida directamente en  [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, las claves se deben escribir en el panel de configuración de la aplicación. Se conservarán de una sesión a otra como datos de usuario.

Cuando esté listo para la integración, **simplemente haga referencia a la biblioteca VideoFrameAnalyzer desde sus propios proyectos**.

Las funcionalidades de comprensión de imágenes, voces, vídeos o texto de VideoFrameAnalyzer emplean Microsoft Cognitive Services. Microsoft recibirá las imágenes, el audio, el vídeo y otros datos que usted cargue (a través de esta aplicación) y podrá usarlos para fines de mejora del servicio. Pedimos su ayuda para proteger a las personas cuyos datos envíe la aplicación a Azure Cognitive Services.

## <a name="summary"></a>Resumen

En esta guía, ha aprendido a ejecutar análisis casi en tiempo real en las secuencias de vídeo en directo Face API, Computer Vision API y Emotion API, y a utilizar el código de ejemplo para empezar a trabajar. Puede empezar a compilar su aplicación con las claves de API gratuitas disponibles en la [página de registro de Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

No dude en enviar sus comentarios y sugerencias al [repositorio de GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) o, para enviar comentarios más amplios sobre la API, a nuestro  [sitio de UserVoice](https://cognitive.uservoice.com/).

