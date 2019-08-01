---
title: 'Inspección de la calidad de los datos de Custom Speech: Servicios de voz'
titlesuffix: Azure Cognitive Services
description: Custom Speech proporciona herramientas que le permiten inspeccionar visualmente la calidad del reconocimiento de un modelo mediante la comparación de los datos de audio con el resultado de reconocimiento correspondiente. Desde el portal de Custom Speech, puede reproducir el audio cargado y determinar si el resultado de reconocimiento proporcionada es correcto.  Esta herramienta le permite inspeccionar rápidamente la calidad del modelo de voz a texto de línea de base de Microsoft o de un modelo personalizado entrenado sin tener que transcribir los datos de audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 132e355abbdbe147a843c51efd018cc759c06836
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064042"
---
# <a name="inspect-custom-speech-data"></a>Inspección de los datos de Custom Speech

> [!NOTE]
> En esta página se supone que ha leído [Preparación de los datos de Custom Speech](how-to-custom-speech-test-data.md) y que ha cargado un conjunto de datos para su inspección.

Custom Speech proporciona herramientas que le permiten inspeccionar visualmente la calidad del reconocimiento de un modelo mediante la comparación de los datos de audio con el resultado de reconocimiento correspondiente. Desde el portal de Custom Speech, puede reproducir el audio cargado y determinar si el resultado de reconocimiento proporcionada es correcto. Esta herramienta le permite inspeccionar rápidamente la calidad del modelo de voz a texto de línea de base de Microsoft o de un modelo personalizado entrenado sin tener que transcribir los datos de audio.

En este documento, aprenderá a inspeccionar visualmente la calidad de un modelo con los datos de entrenamiento cargados previamente.

En esta página, aprenderá a inspeccionar visualmente la calidad del modelo de voz a texto de línea de base de Microsoft o de un modelo personalizado que haya entrenado. Usará los datos que cargó en la pestaña **Data** (Datos) para realizar pruebas.

## <a name="create-a-test"></a>Creación de una prueba

Para crear una prueba, siga estas instrucciones:

1. Vaya a **Speech-to-text > Custom Speech > Testing** (Conversión de voz a texto > Custom Speech > Pruebas).
2. Haga clic en **Add test** (Agregar prueba).
3. Seleccione **Inspect quality (Audio-only data)** (Inspeccionar la calidad [solo datos de audio]). Asigne un nombre y una descripción a la prueba y seleccione el conjunto de datos de audio.
4. Puede seleccionar hasta dos modelos para probar.
5. Haga clic en **Create**(Crear).

Después de que la prueba se ha creado correctamente, puede comparar los modelos en paralelo.

## <a name="side-by-side-model-comparisons"></a>Comparaciones de los modelos en paralelo

Cuando el estado de la prueba sea *Succeeded* (Correcto), haga clic en el nombre del elemento de prueba para ver los detalles de la prueba. En esta página se muestran todas las expresiones del conjunto de datos y se indican los resultados del reconocimiento de los dos modelos al lado de la transcripción del conjunto de datos enviado.

Con el fin de inspeccionar la comparación en paralelo, puede alternar los distintos tipos de error, como inserción, eliminación y sustitución. Al escuchar el audio y comparar los resultados del reconocimiento de cada columna (que muestra la transcripción con la etiqueta humana y los resultados de los dos modelos de conversión de voz a texto), puede decidir qué modelo satisface sus necesidades y dónde hacen falta mejoras.

La inspección de las pruebas de calidad es útil para validar si la calidad de un punto de conexión de reconocimiento de voz es suficiente para una aplicación.  Si necesita una medida objetiva de precisión, que necesite la transcripción del audio, siga las instrucciones que encontrará en [Evaluación de la precisión](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Pasos siguientes

* [Evaluación de los datos](how-to-custom-speech-evaluate-data.md)
* [Entrenamiento del modelo](how-to-custom-speech-train-model.md)
* [Implementación del modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Preparación de los datos de prueba para Custom Speech](how-to-custom-speech-test-data.md)
