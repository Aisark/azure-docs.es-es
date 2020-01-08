---
title: 'Inicio rápido: Reconocimiento de la voz a través de un micrófono en Python: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Use esta guía para crear una aplicación de consola de conversión de voz a texto que use el SDK de Voz para Python. Cuando termine, puede usar el micrófono del equipo para realizar la conversión de voz en texto en tiempo real.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 086a77a9d7096870b8d381d301bfc0f31ab390f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467615"
---
## <a name="prerequisites"></a>Prerequisites

Antes de comenzar:

> [!div class="checklist"]
> * [Ha creado un recurso de Voz de Azure](../../../../get-started.md)
> * [Ha configurado el entorno de desarrollo](../../../../quickstarts/setup-platform.md)
> * [Ha creado un proyecto de ejemplo vacío](../../../../quickstarts/create-project.md)
> * Asegúrese de que tiene acceso a un micrófono para capturar el audio.

## <a name="support-and-updates"></a>Soporte técnico y actualizaciones

Las actualizaciones del paquete de Python del SDK de Voz se distribuirán mediante PyPI y se anunciarán en la página [Notas de la versión](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Si hay disponible una nueva versión, puede actualizarse a ella con el comando `pip install --upgrade azure-cognitiveservices-speech`.
Para comprobar qué versión está instalada actualmente, inspeccione la variable `azure.cognitiveservices.speech.__version__`.

Si tiene un problema o falta una característica, consulte las [opciones de ayuda y soporte técnico](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Creación de una aplicación de Python mediante el SDK de Voz

### <a name="run-the-sample"></a>Ejecución del ejemplo

Puede copiar el [código de ejemplo](#sample-code) de este inicio rápido en un archivo de código fuente `quickstart.py` y ejecutarlo en el IDE o en la consola:

```sh
python quickstart.py
```

También, puede descargar este tutorial de inicio rápido como un cuaderno de [Jupyter](https://jupyter.org) del [repositorio de ejemplos del SDK de Voz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) y ejecutarlo como un cuaderno.

### <a name="sample-code"></a>Código de ejemplo

> [!NOTE]
> El SDK de Voz se usará de forma predeterminada para reconocer el uso de en-us como idioma. Para más información sobre cómo elegir el idioma de origen, consulte [Especificación del idioma de origen para la conversión de voz a texto](../../../../how-to-specify-source-language.md).

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalación y uso del SDK de Voz con Visual Studio Code

1. Descargue e instale una versión de 64 bits (3.5 o posterior) de [Python](https://www.python.org/downloads/) en el equipo.
1. Descargue e instale [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra Visual Studio Code e instale la extensión de Python. Seleccione **File** > **Preferences** > **Extensions** (Archivo > Preferencias > Extensiones) en el menú. Busque **Python**.

   ![Instalación de la extensión de Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Cree una carpeta en la que almacenar el proyecto. Por ejemplo, puede usar para ello el Explorador de Windows.
1. En Visual Studio Code, seleccione el icono de **File** (Archivo). A continuación, abra la carpeta que creó.

   ![Abrir una carpeta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Cree un archivo de código fuente de Python `speechsdk.py` mediante la selección del icono de nuevo archivo.

   ![Creación de un archivo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, pegue y guarde el [código de Python](#sample-code) en el archivo recién creado.
1. Inserte la información de la suscripción del servicio Voz.
1. Si se selecciona, se muestra un intérprete de Python en el lado izquierdo de la barra de estado en la parte inferior de la ventana.
   En caso contrario, aparecerá una lista de los intérpretes de Python disponibles. Abra la paleta de comandos (Ctrl+Mayús+P) y escriba **Python: Select Interpreter** (Seleccionar intérprete). Elija un valor apropiado.
1. Puede instalar el paquete de Python del SDK de Voz desde dentro de Visual Studio Code. Hágalo si no está instalado aún para el intérprete de Python seleccionado.
   Para instalar el paquete del SDK de Voz, abra un terminal. Abra de nuevo la paleta de comandos (Ctrl+Mayús+P) y escriba **Terminal: Create New Integrated Terminal** (Crear terminal integrado).
   En el terminal que se abre, escriba el comando `python -m pip install azure-cognitiveservices-speech` o el que sea apropiado para su sistema.
1. Para ejecutar el código de ejemplo, haga clic con el botón derecho en algún lugar dentro del editor. Seleccione **Run Python File in Terminal** (Ejecutar archivo de Python en terminal).
   Diga algunas palabras cuando se le pida. El texto transcrito se muestra poco después.

   ![Ejecución de un ejemplo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Si tiene problemas para seguir estas instrucciones, consulte el [tutorial de Python para Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) con información más amplia.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [footer](./footer.md)]
