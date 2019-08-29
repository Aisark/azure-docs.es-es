---
title: Instrucciones de control de errores de Azure Functions | Microsoft Docs
description: Se proporcionan instrucciones generales para controlar los errores que se producen cuando se ejecutan funciones, y vínculos a temas de errores específicos de enlace.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: fdfee3442986322f242da730bb9ceccbc9f9e250
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097482"
---
# <a name="azure-functions-error-handling"></a>Control de errores de Azure Functions

En este tema se proporcionan instrucciones generales para controlar los errores que se producen cuando se ejecutan funciones. También se proporcionan vínculos a temas que describen los errores específicos de enlace que pueden producirse. 

## <a name="handling-errors-in-functions"></a>Control de errores en funciones
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de error de enlace

Cuando se realizan integraciones con los servicios de Azure, pueden surgir errores que se originan desde las API de los servicios subyacentes. Encontrará vínculos a la documentación de códigos de error para estos servicios en la sección acerca de las **excepciones y códigos de retorno** de los siguientes temas de referencia de enlaces y desencadenadores:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Centros de notificaciones](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
