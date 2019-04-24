---
title: Expresiones condicionales del motor de reglas de Azure CDN | Microsoft Docs
description: Documentación de referencia sobre las condiciones y características de coincidencia del motor de reglas de Azure CDN.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 73c41b754c0aca5ddb1a49fcd2794aa41b2fa705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324204"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Expresiones condicionales del motor de reglas de Azure CDN
En este tema se muestran descripciones detalladas de las expresiones condicionales para el [motor de reglas](cdn-rules-engine.md)de Azure Content Delivery Network (CDN).

La primera parte de una regla es la expresión condicional.

Expresión condicional | DESCRIPCIÓN
-----------------------|-------------
IF | Una expresión IF siempre es una parte de la primera instrucción en una regla. Al igual que las demás expresiones condicionales, esta instrucción IF debe asociarse a una coincidencia. Si no se ha definido ninguna expresión condicional adicional, esta coincidencia determina los criterios que deben cumplirse antes de poder aplicar un conjunto de funciones a una solicitud.
AND IF | Una expresión AND IF solo puede agregarse después de los siguientes tipos de expresiones condicionales: IF, AND IF. Indica que hay otra condición que debe cumplirse para la instrucción IF inicial.
ELSE IF| Una expresión ELSE IF especifica una condición alternativa que debe cumplirse antes de que se realice un conjunto de funciones específicas de esta instrucción ELSE IF. La presencia de una instrucción IF ELSE indica el final de la instrucción anterior. La única expresión condicional que se puede colocar después de una instrucción ELSE IF es otra instrucción ELSE IF. Esto significa que una instrucción ELSE IF solo puede usarse para especificar una única condición adicional que debe cumplirse.

**Ejemplo**: ![Condición de coincidencia de red CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regla posterior puede invalidar las acciones especificadas por una regla anterior. Ejemplo: Una regla comodín protege todas las solicitudes mediante la autenticación basada en Token. Puede crearse otra regla directamente debajo de ella para realizar una excepción para ciertos tipos de solicitudes.

### <a name="next-steps"></a>Pasos siguientes
* [Información general de Azure CDN](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Funciones del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
