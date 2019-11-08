---
title: Referencia de correo electrónico de entidades precompiladas de LUIS
titleSuffix: Azure Cognitive Services
description: Este artículo contiene información acerca de la entidad precompilada de correo electrónico en Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464983"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entidad precompilada Email para una aplicación de LUIS
La extracción de correo electrónico incluye la dirección de correo electrónico completa de un mensaje. Dado que esta entidad ya está entrenada, no se necesita agregar expresiones de ejemplo que contengan el correo electrónico en las intenciones de la aplicación. La entidad de correo electrónico se admite solamente en la referencia cultural `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Resolución del correo electrónico precompilado

La consulta devuelve los siguientes objetos de entidad:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[Respuesta de V3](#tab/V3)

El siguiente JSON es con el parámetro `verbose` establecido en `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Respuesta detallada de V3](#tab/V3-verbose)

El siguiente JSON es con el parámetro `verbose` establecido en `true`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[Respuesta de V2](#tab/V2)

En el siguiente ejemplo, se muestra la resolución de la entidad **builtin.email**.

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Pasos siguientes

Más información acerca del [punto de conexión de predicción de V3](luis-migration-api-v3.md).

Obtenga información acerca de las entidades de [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) y [porcentaje](luis-reference-prebuilt-percentage.md). 
