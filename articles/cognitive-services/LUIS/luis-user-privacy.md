---
title: Exportación y eliminación de datos
titleSuffix: Azure Cognitive Services
description: Elimine datos de clientes para garantizar la privacidad y el cumplimiento.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: a82452f4b41aee9c4ea6f269d92fbc91a5697d16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64916951"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportación y eliminación de datos del cliente en Language Understanding (LUIS) en Cognitive Services

Elimine datos de clientes para garantizar la privacidad y el cumplimiento. 

## <a name="summary-of-customer-data-request-features"></a>Resumen de características de solicitud de datos del cliente
Language Understanding Intelligent Service (LUIS) conserva el contenido del cliente para que el servicio funcione, pero el usuario de LUIS tiene control total sobre la visualización, la exportación y la eliminación de sus datos. Esto se puede realizar a través del [portal](luis-reference-regions.md) web de LUIS o las [API de creación de LUIS (también conocidas como API de programación)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

El contenido del cliente se almacena cifrado en el almacenamiento regional de Microsoft Azure e incluye:

- El contenido de la cuenta de usuario recopilado en el registro.
- Datos de entrenamiento necesarios para compilar los modelos
- Consultas de usuario registradas usadas por el [aprendizaje activo](luis-concept-review-endpoint-utterances.md) para ayudar a mejorar el modelo
  - Los usuarios pueden desactivar el registro de consultas mediante la anexión de `&log=false` a la solicitud. Puede obtener más información [aquí](troubleshooting.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Eliminación de datos del cliente
Los usuarios de LUIS tienen control total para eliminar cualquier contenido del usuario, ya sea a través del portal web de LUIS o de las API de creación (o programación) de LUIS. En la siguiente tabla se muestran vínculos para proporcionar asistencia con ambos:

| | **Cuenta de usuario** | **Aplicación** | **Expresiones de ejemplo** | **Consultas de usuario final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Vínculo](luis-concept-data-storage.md#delete-an-account) | [Vínculo](luis-how-to-start-new-app.md#delete-app) | [Vínculo](luis-concept-data-storage.md#utterances-in-an-intent) | [Expresiones de aprendizaje activo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Expresiones registradas](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportación de datos del cliente
Los usuarios de LUIS tienen control total para ver los datos en el portal, sin embargo, debe exportarse a través de las API de creación (o programación) de LUIS. En la siguiente tabla se muestra vínculos para proporcionar asistencia con las exportaciones de datos a través de las API de creación (o programación) de LUIS:

| | **Cuenta de usuario** | **Aplicación** | **Expresiones** | **Consultas de usuario final** |
| --- | --- | --- | --- | --- |
| **API** | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Vínculo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Ubicación del aprendizaje activo

Para habilitar el [aprendizaje activo](luis-how-to-review-endpoint-utterances.md#enable-active-learning), las expresiones registradas de los usuarios, recibidas en los puntos de conexión de LUIS publicados, se almacenan en las siguientes regiones geográficas de Azure:

* [Europa](#europe)
* [Australia](#australia)
* [Estados Unidos](#united-states)

A excepción de los datos de aprendizaje activo (que se detallan a continuación), LUIS sigue los [procedimientos de almacenamiento para los servicios regionales](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

El portal [eu.luis.ai](https://eu.luis.ai) y Europe Authoring (que también se conoce como API de programación) se hospedan en la región geográfica Europa de Azure. El portal eu.luis.ai y Europe Authoring (que también se conoce como API de programación) admiten la implementación de puntos de conexión en las siguientes regiones geográficas de Azure:

* Europa
* Francia
* Reino Unido

Al implementar en estas ubicaciones geográficas de Azure, las expresiones recibidas por el punto de conexión de los usuarios finales de la aplicación se almacenarán en la ubicación geográfica Europa de Azure para el aprendizaje activo. Para deshabilitar el aprendizaje activo, consulte [Deshabilitar el aprendizaje activo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para administrar las expresiones almacenadas, consulte [Eliminar la expresión](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Australia

El portal [au.luis.ai](https://au.luis.ai) y Australia Authoring (que también se conoce como API de programación) se hospedan en la región geográfica Australia de Azure. El portal au.luis.ai y Australia Authoring (que también se conoce como API de programación) admiten la implementación de puntos de conexión en las siguientes regiones geográficas de Azure:

* Australia

Al implementar en estas ubicaciones geográficas de Azure, las expresiones recibidas por el punto de conexión de los usuarios finales de la aplicación se almacenarán en la ubicación geográfica Australia de Azure para el aprendizaje activo. Para deshabilitar el aprendizaje activo, consulte [Deshabilitar el aprendizaje activo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para administrar las expresiones almacenadas, consulte [Eliminar la expresión](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Estados Unidos

El portal [luis.ai](https://www.luis.ai) y United States Authoring (que también se conoce como API de programación) se hospedan en la región geográfica Estados Unidos de Azure. El portal luis.ai y United States Authoring (que también se conoce como API de programación) admiten la implementación de puntos de conexión en las siguientes regiones geográficas de Azure:

* Ubicaciones geográficas de Azure no admitidas en las regiones de creación Europa o Australia

Al implementar en estas ubicaciones geográficas de Azure, las expresiones recibidas por el punto de conexión de los usuarios finales de la aplicación se almacenarán en la ubicación geográfica Estados Unidos de Azure para el aprendizaje activo. Para deshabilitar el aprendizaje activo, consulte [Deshabilitar el aprendizaje activo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para administrar las expresiones almacenadas, consulte [Eliminar la expresión](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Referencia de las regiones de LUIS](./luis-reference-regions.md)
