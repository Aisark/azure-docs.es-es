---
title: 'Ejemplo de directiva de Azure API Management: generación de una firma de acceso compartido | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: muestra cómo generar una firma de acceso compartido mediante expresiones y cómo reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 45e29673e264f64c976cce664eaf749636400c7f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067731"
---
# <a name="generate-shared-access-signature"></a>Generación de una firma de acceso compartido

Este artículo muestra un ejemplo de directiva de Azure API Management: que muestra cómo generar una [firma de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) mediante expresiones y reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

