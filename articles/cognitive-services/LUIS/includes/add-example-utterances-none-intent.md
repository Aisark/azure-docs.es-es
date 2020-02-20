---
title: archivo de inclusión
description: archivo de inclusión
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279455"
---
La aplicación cliente debe saber si una expresión no es significativa o adecuada para la aplicación. La intención **None** se agrega a cada aplicación como parte del proceso de creación para determinar si la aplicación cliente no debe responder a una expresión.

Si LUIS devuelve la intención **None** para una expresión, la aplicación cliente puede preguntar al usuario si quiere finalizar la conversación o dar más instrucciones para continuarla.

Si deja la intención **None** vacía, una expresión que debería predecirse fuera del dominio del asunto se predecirá en una de las intenciones del dominio de sujeto existente. El resultado es que la aplicación cliente, como un bot de chat, realizará operaciones incorrectas en función de una predicción incorrecta.

1. Seleccione **Intents** (Intenciones) en el panel izquierdo.

1. Seleccione la intención **None**. Agregue tres expresiones que el usuario podría solicitar pero que no son pertinentes para la aplicación de pedidos de pizza.

    |Expresiones de ejemplo de `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Estos ejemplos no deben usar palabras que se esperan en el dominio de sujeto, como `pizza`, `cheese`, `crust`, `pickup` `deliver`.