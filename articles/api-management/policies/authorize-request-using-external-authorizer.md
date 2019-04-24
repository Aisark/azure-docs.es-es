---
title: 'Ejemplo de directiva de Azure API Management: autorización de la solicitud con un autorizador externo | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: muestra cómo autorizar las solicitudes con el autorizador externo mediante la encapsulación de una lógica de autenticación o autorización personalizada o heredada.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 65ea8622187d0665e4680f4162ddff0bc01e6eb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306773"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorización de solicitudes mediante el autorizador externo

En este artículo se muestra un ejemplo de directiva de Azure API Management que explica cómo proteger el acceso seguro a la API mediante un autorizador externo que encapsula la lógica de autenticación o autorización personalizada. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de restricciones de acceso](../api-management-access-restriction-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)
