---
title: 'Notas de la versión: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Consulte un registro de características, mejoras, correcciones de errores y problemas conocidos para el servicio de voz.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: 73f1739b09111052abd985920efe3ef944a89ca9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380361"
---
# <a name="release-notes"></a>Notas de la versión

## <a name="speech-sdk-180-2019-november-release"></a>SDK de Voz 1.8.0: Versión de noviembre de 2019

**Nuevas características**

- Se ha agregado una API `FromHost()` para facilitar su uso con contenedores locales y nubes soberanas.
- Se ha agregado la detección automática de idioma de origen para el reconocimiento de voz (en Java y C++)
- Se ha agregado el objeto `SourceLanguageConfig` para el reconocimiento de voz, que se usa para especificar los idiomas de origen esperados (en Java y C++).
- Se ha agregado compatibilidad con `KeywordRecognizer` en Windows (UWP), Android e iOS mediante los paquetes de Nuget y Unity.
- Se ha agregado la API de Java de conversación remota para realizar la transcripción de conversaciones en lotes asincrónicos.

**Cambios importantes**

- Las funcionalidades de transcripción de conversaciones se han movido al espacio de nombres `Microsoft.CognitiveServices.Speech.Transcription`.
- Parte de los métodos de transcripción de conversaciones se han movido a la nueva clase `Conversation`.
- Compatibilidad eliminada para iOS de 32 bits (ARMv7 y x86)

**Correcciones de errores**

- Se ha corregido un bloqueo si se usa `KeywordRecognizer` local sin una clave de suscripción válida al servicio de voz.

**Muestras**

- Ejemplo de Xamarin para `KeywordRecognizer`
- Ejemplo de Unity para `KeywordRecognizer`
- Ejemplos de C++ y Java de detección automática de idioma de origen

## <a name="speech-sdk-170-2019-september-release"></a>SDK de voz 1.7.0: versión de septiembre de 2019

**Nuevas características**

- Compatibilidad con la versión beta agregada para Xamarin en la Plataforma universal de Windows (UWP), Android e iOS
- Compatibilidad con iOS agregada para Unity
- Se ha agregado compatibilidad con entradas `Compressed` para ALaw, Mulaw, FLAC en Android, iOS y Linux.
- Se ha agregado `SendMessageAsync` en la clase `Connection` para enviar un mensaje al servicio.
- Se ha agregado `SetMessageProperty` en la clase `Connection` para establecer la propiedad de un mensaje.
- TTS agregó compatibilidad para Java (JRE y Android), Python, Swift y Objective-C
- TTS agregó compatibilidad de reproducción para macOS, iOS y Android
- Se ha agregado información de "límite de palabras" para TTS

**Correcciones de errores**

- Se ha corregido un problema de compilación de IL2CPP en Unity 2019 para Android
- Se ha corregido un problema con los encabezados con formato incorrecto en la entrada de archivo WAV que se procesa de forma incorrecta
- Se ha corregido un problema con UUID que no es único en algunas propiedades de conexión
- Se han corregido algunas advertencias sobre los especificadores de nulabilidad en los enlaces SWIFT (puede que se requieran pequeños cambios en el código)
- Se ha corregido un error que provocaba que las conexiones de WebSocket se cerraran de manera incorrecta en la carga de red
- Se ha corregido un problema en Android que a veces provoca que `DialogServiceConnector` use identificadores de impresión duplicados.
- Se han introducido mejoras en la estabilidad de las conexiones entre interacciones multiproceso y la generación de informes de errores (a través de eventos `Canceled`) cuando se producen con `DialogServiceConnector`.
- Los inicios de sesión de `DialogServiceConnector` ahora proporcionarán eventos correctamente, incluso si se llama a `ListenOnceAsync()` durante una operación `StartKeywordRecognitionAsync()` activa.
- Se ha resuelto un bloqueo asociado a la recepción de actividades `DialogServiceConnector`.

**Muestras**

- Inicio rápido para Xamarin
- Se ha actualizado el inicio rápido de CPP con información de ARM64 de Linux
- Se ha actualizado el inicio rápido de Unity con información de iOS

## <a name="speech-sdk-160-2019-june-release"></a>SDK de Voz 1.6.0: versión de junio de 2019

**Muestras**

- Ejemplos de inicio rápido para Texto a voz en UWP y Unity
- Ejemplo de inicio rápido para Swift en iOS
- Ejemplos de Unity para Traducción y Reconocimiento de la intención comunicativa y Voz
- Ejemplos de inicios rápidos actualizados para `DialogServiceConnector`

**Mejoras y cambios**

- Espacio de nombres de cuadro de diálogo:
  - El nombre de `SpeechBotConnector` ha cambiado a `DialogServiceConnector`
  - El nombre de `BotConfig` ha cambiado a `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` se ha reasignado a `DialogServiceConfig::FromBotSecret()`
  - Todos los clientes de Voz de Direct Line existentes siguen siendo compatibles después del cambio de nombre
- Actualización del adaptador REST de TTS para admitir una conexión persistente de proxy
- Un mejor mensaje de error cuando se pasa una región no válida
- Swift/Objective-C:
  - Mejores informes de errores: los métodos que pueden generar un error ahora se encuentran en dos versiones: una que expone un objeto `NSError` para el control de errores y una que genera una excepción. La primera se expone a Swift. Este cambio requiere adaptaciones en el código Swift existente.
  - Mejor control de eventos

**Correcciones de errores**

- Corrección de TTS: donde el futuro de `SpeakTextAsync` se devolvió sin esperar al fin de la representación del audio
- Corrección para la serialización de las cadenas en C# para permitir la compatibilidad total con idiomas
- Corrección del problema de las aplicaciones centrales de .NET para cargar la biblioteca principal con un marco de destino net461 en ejemplos
- Corrección de problemas ocasionales para implementar bibliotecas nativas en la carpeta de salida en los ejemplos
- Corrección para cerrar el socket web de manera confiable
- Corrección de un posible bloqueo al abrir una conexión con una carga muy elevada en Linux
- Corrección de metadatos faltantes en el paquete de marcos para macOS
- Corrección de problemas con `pip install --user` en Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Se trata de una versión de corrección de errores y solo afecta al SDK nativo o administrado. No afecta a la versión de JavaScript del SDK.

**Correcciones de errores**

- Corrección de FromSubscription cuando se usa con la transcripción de la conversación.
- Corrección de errores en la detección de palabras clave para los asistentes por voz.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0 Versión de mayo de 2019

**Nuevas características:**

- La detección de palabras clave (KWS) ahora está disponible para Windows y Linux. La funcionalidad KWS podría funcionar con cualquier tipo de micrófono; no obstante, la compatibilidad oficial de KWS está limitada actualmente a las matrices de micrófonos que se encuentran en el hardware de Azure Kinect DK o el SDK de dispositivos de voz.
- La funcionalidad de sugerencia de frases está disponible a través del SDK. Para más información, consulte [esta página](how-to-phrase-lists.md).
- La funcionalidad de transcripción de conversaciones está disponible a través del SDK. Consulte [aquí](conversation-transcription-service.md).
- Compatibilidad agregada con los asistentes por voz mediante el canal Direct Line Speech.

**Muestras**

- Se han agregado ejemplos para nuevas características o nuevos servicios admitidos por el SDK.

**Mejoras y cambios**

- Se han agregado varias propiedades de reconocimiento para ajustar el comportamiento del servicio o los resultados del servicio (por ejemplo, enmascaramiento de palabras soeces etc.).
- Ahora puede configurar el reconocimiento a través de las propiedades de configuración estándar, incluso si ha creado el valor de `FromEndpoint` del reconocedor.
- Objective-C: se agregó la propiedad `OutputFormat` a `SPXSpeechConfiguration`.
- El SDK ahora admite Debian 9 como una distribución de Linux.

**Correcciones de errores**

- Se ha corregido un problema donde el recurso de altavoz se destruía demasiado pronto en la conversión de texto a voz.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Se trata de una versión de corrección de errores y solo afecta al SDK nativo o administrado. No afecta a la versión de JavaScript del SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Esta es una versión solo para JavaScript. No se agregó ninguna característica. Se realizaron las siguientes correcciones:

- Se impide que el paquete web cargue https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0 Versión de abril de 2019

**Nuevas características:**

- El SDK admite ahora el servicio de conversión de texto a voz en versión beta. Se admite en Windows y Linux Desktop desde C++ y C#. Para más información, consulte la [información general sobre la conversión de texto a voz](text-to-speech.md#get-started).
- El SDK ahora admite archivos de audio MP3 y Opus/OGG como archivos de entrada de secuencia. Esta característica solo está disponible en Linux desde C++ y C# y está actualmente en versión beta (más detalles [aquí](how-to-use-codec-compressed-audio-input-streams.md)).
- Speech SDK para Java, .NET Core, C++ y Objective-C ha conseguido compatibilidad con macOS. La compatibilidad de Objective-C con macOS está actualmente en versión beta.
- iOS: Speech SDK para iOS (Objective-C) ahora también se publica como una instancia de CocoaPod.
- JavaScript: compatibilidad con micrófono no predeterminada como dispositivo de entrada.
- JavaScript: compatibilidad con servidores proxy para Node.js.

**Muestras**

- se han agregado ejemplos para usar Speech SDK con C++ y con Objective-C en macOS.
- Se han agregado ejemplos que muestran el uso del servicio de conversión de texto a voz.

**Mejoras y cambios**

- Python: ahora se exponen propiedades adicionales de los resultados del reconocimiento mediante la propiedad `properties`.
- Para la compatibilidad adicional con el desarrollo y la depuración, puede redirigir la información de registro y diagnóstico del SDK a un archivo de registro (más información [aquí](how-to-use-logging.md)).
- JavaScript: mejora del rendimiento del procesamiento de audio.

**Correcciones de errores**

- Mac/iOS: se corrigió un error que daba lugar a una larga espera cuando no se podía establecer una conexión con el servicio de voz.
- Python: mejora del control de errores en los argumentos de las devoluciones de llamada de Python.
- JavaScript: se corrigieron los informes de estado erróneos de la voz que finalizaban en RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1 Actualización de febrero de 2019

Se trata de una versión de corrección de errores y solo afecta al SDK nativo o administrado. No afecta a la versión de JavaScript del SDK.

**Corrección de error**

- Se ha corregido una fuga de memoria cuando se usa la entrada de micrófono. No afecta a la entrada de archivos o basada en secuencias.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: versión de febrero de 2019

**Nuevas características**

- El SDK de voz admite la selección del micrófono de entrada mediante la clase `AudioConfig`. Esto permite transmitir datos de audio al servicio de voz desde un micrófono no predeterminado. Para más información, consulte la documentación en la que se describe cómo [seleccionar un dispositivo de entrada de audio](how-to-select-audio-input-devices.md). Esta característica aún no está disponible en JavaScript.
- Speech SDK ahora es compatible con Unity en una versión beta. Proporcione sus comentarios en la sección de problemas en el [repositorio de ejemplos de GitHub](https://aka.ms/csspeech/samples). Esta versión es compatible con Unity en Windows x86 y x64 (aplicaciones de escritorio o de la Plataforma universal de Windows) y Android (ARM32/64, x86). Puede encontrar más información en nuestra [guía de inicio rápido sobre Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- El archivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (incluido en versiones anteriores) ya no es necesario. La funcionalidad está ahora integrada en el SDK principal.

**Muestras**

El siguiente contenido nuevo está disponible en nuestro [repositorio de ejemplo](https://aka.ms/csspeech/samples):

- Ejemplos adicionales para `AudioConfig.FromMicrophoneInput`.
- Ejemplos adicionales de Python para traducción y reconocimiento de intenciones.
- Ejemplos adicionales para usar el objeto `Connection` en iOS.
- Ejemplos adicionales de Java para la traducción con la salida de audio.
- Nuevo ejemplo de uso de la [API de REST de transcripción de lotes](batch-transcription.md).

**Mejoras y cambios**

- Python
  - Mensajes de error y verificación de parámetros mejorada en `SpeechConfig`.
  - Adición de compatibilidad para el objeto `Connection`.
  - Compatibilidad con Python (x86) de 32 bits en Windows.
  - Speech SDK para Python ya no está disponible como beta.
- iOS
  - El SDK ahora se compila en función de la versión 12.1 del SDK de iOS.
  - El SDK ahora es compatible con las versiones 9.2 y posteriores de iOS.
  - Documentación de referencia mejorada y corrección de varios nombres de propiedad.
- JavaScript
  - Adición de compatibilidad para el objeto `Connection`.
  - Archivos de definición de tipos agregados para JavaScript agrupado.
  - Compatibilidad e implementación iniciales para sugerencias de frases.
  - Colección de propiedades devuelta con JSON del servicio para reconocimiento.
- Los archivos DLL de Windows contienen ahora un recurso de versión.
- Si crea un valor de `FromEndpoint` de reconocedor, puede agregar parámetros directamente a la dirección URL del punto de conexión. Con `FromEndpoint` no puede configurar el reconocedor mediante las propiedades de configuración estándar.

**Correcciones de errores**

- La contraseña de proxy y el nombre de usuario de proxy vacíos no se administraron correctamente. Con esta versión, si establece el nombre de usuario de proxy y la contraseña de proxy en una cadena vacía, no se enviarán al conectarse al proxy.
- El identificador de sesión creado por el SDK no siempre es realmente aleatorio para algunos lenguajes o&nbsp; entornos. Se ha agregado la inicialización del generador aleatorio para corregir este problema.
- Control mejorado del token de autorización. Si desea usar un token de autorización, especifíquelo en `SpeechConfig` y deje la clave de suscripción vacía. A continuación, cree el reconocedor como de costumbre.
- En algunos casos, el objeto `Connection` no se publicó correctamente. Ahora se ha corregido.
- Se corrigió el ejemplo de JavaScript para admitir la salida de audio para la síntesis de traducción también en Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta es una versión solo para JavaScript. No se agregó ninguna característica. Se realizaron las siguientes correcciones:

- Activar el final del flujo en turn.end, y no en speech.end.
- Corregir error de la bomba de audio por el que no se programaba el siguiente envío en caso de error del envío actual.
- Corregir el reconocimiento continuo con el token de autenticación.
- Corrección de errores de diferentes reconocedores y puntos de conexión.
- Mejoras en la documentación.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: Versión de diciembre de 2018

**Nuevas características**

- Python
  - La versión beta de la compatibilidad con Python (3.5 y versiones posteriores) está disponible con esta versión. Para más información, consulte aquí](quickstart-python.md).
- JavaScript
  - Speech SDK para JavaScript ha sido de código abierto. El código fuente está disponible en [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Ya se admite Node.js; puede encontrar más información [aquí](quickstart-js-node.md).
  - Se quitó la restricción de longitud para las sesiones de audio; la reconexión se realizará automáticamente en la portada.
- Objecto `Connection`
  - Desde el objeto `Recognizer`, puede acceder al objeto `Connection`. Este objeto le permite iniciar la conexión al servicio y suscribirse para conectar y desconectar eventos explícitamente.
    (Esta característica no está disponible aún ni en JavaScript ni en Python).
- Compatibilidad con Ubuntu 18.04.
- Android
  - Compatibilidad con ProGuard habilitada durante la generación del APK.

**Mejoras**

- Mejoras en el uso de subprocesos internos, lo que reduce el número de subprocesos, bloqueos y exclusiones mutuas.
- Se mejoraron los informes de errores y la información. En algunos casos, los mensajes de error no se propagan totalmente.
- Se actualizaron las dependencias de desarrollo en JavaScript para usar los módulos actualizados.

**Correcciones de errores**

- Se han corregido las fugas de causadas por un error de coincidencia de tipos en `RecognizeAsync`.
- En algunos casos, se perdieron excepciones.
- Corrección de las fugas de memoria en los argumentos de eventos de traducción.
- Se ha corregido un problema de bloqueo al volver a conectar en sesiones de larga ejecución.
- Se ha corregido un problema que podría dar lugar a que faltase el resultado final para las traducciones con errores.
- C#: Si no se esperaba una operación `async` en el subproceso principal, es posible que se pudiese desechar el reconocedor antes de completarse la tarea asincrónica.
- Java: Se ha corregido un problema que provocaba un bloqueo de la VM de Java.
- Objective-C: Se ha corregido la asignación de la enumeración; se devolvió RecognizedIntent en lugar de `RecognizingIntent`.
- JavaScript: Se ha establecido el formato de salida predeterminado en "simple" en `SpeechConfig`.
- JavaScript: Se ha quitado una incoherencia entre las propiedades del objeto de configuración en JavaScript y otros lenguajes.

**Muestras**

- Se han actualizado y corregido varios ejemplos, como las voces de salida para la traducción, etc.
- Se han agregado ejemplos de Node.js en el [repositorio de ejemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuevas características**

- Compatibilidad con Android x86/x64.
- Compatibilidad con proxy: En el objeto `SpeechConfig`, ahora puede llamar a una función para establecer la información del proxy (nombre de host, puerto, nombre de usuario y contraseña). Esta característica no está disponible aún en iOS.
- Mensajes y códigos de error mejorados. Si un reconocimiento devolvió un error, esto ya ha establecido `Reason` (en el evento cancelado) o `CancellationDetails` (en el resultado del reconocimiento) en `Error`. El evento cancelado ahora contiene dos miembros adicionales, `ErrorCode` y `ErrorDetails`. Si el servidor devolvió información de error adicional con el error notificado, ahora estará disponible en los nuevos miembros.

**Mejoras**

- Verificación adicional agregada en la configuración del reconocedor y mensaje de error adicional agregado.
- Control mejorado del silencio prolongado en medio de un archivo de audio.
- Paquete NuGet: para proyectos de .NET Framework, evita la compilación con la configuración de AnyCPU.

**Correcciones de errores**

- En los reconocedores se han encontrado varias excepciones corregidas. Además, las excepciones se detectan y se convierten en un evento `Canceled`.
- Corrección de una fuga de memoria en la administración de propiedades.
- Se corrigió el error en el que un archivo de entrada de audio podría bloquear el reconocedor.
- Se corrigió un error donde se podrían recibir eventos después de un evento de detención de la sesión.
- Se corrigieron algunas condiciones de subprocesos.
- Se corrigió un problema de compatibilidad de iOS que podría dar lugar a un bloqueo.
- Mejoras de estabilidad para la compatibilidad del micrófono en Android.
- Se corrigió un error donde un reconocedor en JavaScript ignoraría el lenguaje de reconocimiento.
- Se corrigió un error que impedía establecer el valor `EndpointId` (en algunos casos) en JavaScript.
- Se cambió el orden de los parámetros en AddIntent en JavaScript y se agregó la firma de JavaScript `AddIntent` que faltaba.

**Muestras**

- Se han agregado ejemplos de C++ y C# para el uso de transmisiones de inserción y extracción en el [repositorio de ejemplos](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Mejoras en la confiabilidad y correcciones de errores:

- Corrección de un potencial error grave debido a una condición de carrera al desechar un reconocedor
- Corrección de un potencial error grave en el caso de propiedades sin establecer.
- Comprobación adicional de errores y parámetros.
- Objective-C: corrección de posibles errores graves causados por la invalidación de nombres en NSString.
- Objective-C: ajuste de visibilidad en la API
- JavaScript: corrección con respecto a los eventos y sus cargas.
- Mejoras en la documentación.

Se ha agregado un nuevo ejemplo de Javascript en nuestro [repositorio de ejemplos](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de Voz 1.0.0 de Cognitive Services: Versión de septiembre de 2018

**Nuevas características:**

- Compatibilidad con Objective-C en iOS. Consulte la [Guía de inicio rápido de Objective-C para iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Se admite JavaScript en el explorador. Consulte la [Guía de inicio rápido de JavaScript](quickstart-js-browser.md).

**Cambios importantes**

- Con esta versión se presentan una serie de cambios importantes.
  Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para más información.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Voz 0.6.0 de Cognitive Services: Versión de agosto de 2018

**Nuevas características:**

- Ahora, las aplicaciones de UWP creadas con SDK de Voz superan el Kit para la certificación de aplicaciones en Windows (WACK).
  Consulte la [Guía de inicio rápido de UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Compatibilidad con .NET Standard 2.0 en Linux (Ubuntu 16.04 x64).
- Experimental: compatibilidad con Java 8 en Windows (64 bits) y Linux (Ubuntu 16.04 x 64).
  Consulte la [Guía de inicio rápido de Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Cambios funcionales**

- Se expone más información detallada sobre los errores de conexión.

**Cambios importantes**

- En Java (Android), la función `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` ya no requiere un parámetro de ruta de acceso. Ahora, la ruta de acceso se detecta automáticamente en todas las plataformas compatibles.
- En Java y C#, se ha quitado el descriptor de acceso get- de la propiedad `EndpointUrl`.

**Correcciones de errores**

- En Java, se implementa ahora el resultado de la síntesis de audio en el reconocedor de traducción.
- Se ha corregido un error que podía provocar subprocesos inactivos y un mayor número de sockets abiertos y sin usar.
- Se ha corregido un problema por el que un proceso de reconocimiento de larga ejecución podía terminar en mitad de la transmisión.
- Se ha corregido una condición de carrera en el proceso de apagado del reconocedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Voz 0.5.0 de Cognitive Services: Versión de julio de 2018

**Nuevas características:**

- Compatibilidad con la plataforma Android (API 23: Android Marshmallow 6.0 o posterior). Consulte el [inicio rápido de Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Compatibilidad con .NET Standard 2.0 en Windows. Consulte el [inicio rápido de .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: compatibilidad con UWP en Windows (versión 1709 o posterior).
  - Consulte la [Guía de inicio rápido de UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Nota: Las aplicaciones de UWP creadas con el SDK de Voz no pasan aún el Kit para la certificación de aplicaciones en Windows (WACK).
- Compatibilidad con el reconocimiento de ejecución prolongada con reconexión automática.

**Cambios funcionales**

- `StartContinuousRecognitionAsync()` admite reconocimiento de ejecución prolongada.
- El resultado del reconocimiento contiene más campos. Tienen un desplazamiento desde el principio del audio y la duración (ambos en tics) del texto reconocido y valores adicionales que representan el estado de reconocimiento, por ejemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Compatibilidad con AuthorizationToken para la creación de instancias de fábrica.

**Cambios importantes**

- Eventos de reconocimiento: el tipo de evento `NoMatch` se combina con el evento `Error`.
- SpeechOutputFormat en C# se llama ahora `OutputFormat` para concordar con C++.
- El tipo de valor devuelto de algunos métodos de la interfaz `AudioInputStream` se ha modificado ligeramente:
  - En Java, el método `read` ahora devuelve `long` en lugar de `int`.
  - En C#, el método `Read` ahora devuelve `uint` en lugar de `int`.
  - En C++, los métodos `Read` y `GetFormat` ahora devuelven `size_t` en lugar de `int`.
- C++: las instancias de secuencias de entrada de audio ahora solo se pueden pasar como un valor `shared_ptr`.

**Correcciones de errores**

- Se han corregido los valores devueltos incorrectos cuando se agota el tiempo de espera de `RecognizeAsync()`.
- Se ha eliminado la dependencia de las bibliotecas de Media Foundation en Windows. El SDK ahora usa las API de audio básicas.
- Corrección de la documentación: se ha agregado una página de [regiones](regions.md) para describir cuáles son las regiones admitidas.

**Problema conocido**

- SDK de Voz para Android no informa de los resultados de la síntesis de voz para la traducción. Este problema se solucionará en la próxima versión.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Voz 0.4.0 de Cognitive Services: Versión de junio de 2018

**Cambios funcionales**

- AudioInputStream

  Un reconocedor ahora puede consumir una secuencia como origen de audio. Para más información, consulte la [guía de procedimientos](how-to-use-audio-input-streams.md) relacionada.

- Formato de salida detallado

  Al crear un elemento `SpeechRecognizer`, puede solicitar el formato de salida `Detailed` o `Simple`. `DetailedSpeechRecognitionResult` contiene una puntuación de confianza, texto reconocido, formato léxico sin formato, formato normalizado y formato normalizado con palabras soeces enmascaradas.

**Cambio importante**

- En C# se cambia de `SpeechRecognitionResult.RecognizedText` a `SpeechRecognitionResult.Text`.

**Correcciones de errores**

- Se ha corregido un posible problema de devolución de llamada en la capa USP durante el apagado.
- Si un reconocedor usaba un archivo de entrada de audio, mantenía el identificador de archivo más tiempo del necesario.
- Se han eliminado varios interbloqueos entre el suministro de mensajes y el reconocedor.
- Se desencadena un resultado `NoMatch` cuando se agota la respuesta del servicio.
- Las bibliotecas de Media Foundation en Windows son de carga retrasada. Esta biblioteca solo es necesaria para la entrada del micrófono.
- La velocidad de carga de los datos de audio se limita al doble de la velocidad de audio original.
- En Windows, los ensamblados .NET de C# ahora son de nombre seguro.
- Corrección de la documentación: `Region` necesita información para crear un reconocedor.

Se han agregado más ejemplos y se actualizan constantemente. Para obtener el conjunto más reciente de ejemplos, consulte el [repositorio de GitHub de ejemplos de SDK de Voz](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Voz 0.2.12733 de Cognitive Services: Versión de mayo de 2018

Esta versión es la primera versión preliminar pública de SDK de Voz de Cognitive Services.
