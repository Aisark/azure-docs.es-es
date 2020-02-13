---
title: 'Inicio rápido: Creación de una clave de LUIS'
titleSuffix: Azure Cognitive Services
description: En este inicio rápido aprenderá a crear una aplicación de LUIS y a obtener una clave.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119602"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Inicio rápido: Obtención de una clave de punto de conexión de LUIS.

## <a name="prerequisites"></a>Prerrequisitos

Asegúrese de disponer de los siguientes elementos antes de empezar este tutorial:

* Una cuenta de LUIS. Puede obtener una gratis mediante el [portal de LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS y voz

LUIS se integra con el servicio Voz para reconocer las intenciones a partir de contenido de voz. No necesita una suscripción al servicio Voz, solo LUIS.

LUIS usa tres tipos de claves:

|Tipo de clave|Propósito|
|--------|-------|
|Creación|Le permite crear y modificar aplicaciones de LUIS mediante programación|
|Inicio|Permite probar la aplicación de LUIS mediante el uso solo de texto.|
|Punto de conexión |Autoriza el acceso a una aplicación de LUIS concreta|

Para este tutorial, necesitará el tipo de clave de punto de conexión. Este tutorial utiliza la aplicación de LUIS Home Automation de ejemplo, que se puede crear siguiendo el inicio rápido [Uso de automatización del hogar compilada previamente](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Si ha creado su propia aplicación de LUIS, puede usarla si lo prefiere.

Al crear una aplicación LUIS, el propio LUIS genera automáticamente una clave de inicio para que pueda probarla aplicación mediante consultas de texto. Esta clave no permite la integración del servicio de voz y no funcionará con este tutorial. Cree un recurso de LUIS en el panel de Azure y asígnelo a la aplicación de LUIS. Puede usar el nivel de suscripción gratis para este tutorial.

Después de crear el recurso de LUIS en el panel de Azure, inicie sesión en el [portal de LUIS](https://www.luis.ai/home), elija la aplicación en la página **My Apps** (Mis aplicaciones) y, después, cambie a la página **Manage** (Administrar) de la aplicación. Por último, seleccione **Keys and Endpoints** (Claves y puntos de conexión) en la barra lateral.

![Configuración de los puntos de conexión y claves del portal de LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

En la página **Keys and Endpoint settings** (Configuración de claves y puntos de conexión):

1. Desplácese hacia abajo hasta la sección **Resources and Keys** (Recursos y claves) y seleccione **Assign resource** (Asignar recurso).
1. En el cuadro de diálogo **Assign a key to your app** (Asignar una clave a la aplicación), realices los siguientes cambios:

   * En **Tenant** (Inquilino), elija **Microsoft**.
   * En **Subscription Name** (Nombre de suscripción), elija la suscripción de Azure que contiene el recurso de LUIS que desea usar.
   * En **Key** (Clave), elija el recurso de LUIS que desea usar con la aplicación.

   En unos instantes, la nueva suscripción aparecerá en la tabla en la parte inferior de la página.

1. Seleccione el icono situado junto a una clave para copiarla al Portapapeles. (Puede usar cualquiera de las claves).

![Claves de suscripción de la aplicación LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Reconocimiento de intenciones](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
