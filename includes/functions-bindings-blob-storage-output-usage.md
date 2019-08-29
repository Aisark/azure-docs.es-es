---
title: archivo de inclusión
description: archivo de inclusión
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641864"
---
Puede enlazar con los tipos siguientes para la escritura de blobs:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requiere un enlace "in" `direction` en *function.json* o `FileAccess.Read` en una biblioteca de clases de C#. Sin embargo, puede usar el objeto de contenedor que proporciona el tiempo de ejecución para escribir operaciones, como cargar blobs en el contenedor.

<sup>2</sup> requiere un enlace "inout" `direction` en *function.json* o `FileAccess.ReadWrite` en una biblioteca de clases de C#.

Si intenta enlazar a uno de los tipos de SDK de Storage y recibe un mensaje de error, asegúrese de que hace referencia a [la versión de SDK de Storage correcta](#azure-storage-sdk-version-in-functions-1x).

En las funciones asincrónicas, use el valor devuelto o `IAsyncCollector` en lugar de un parámetro `out`.

El enlace a `string` o `Byte[]` solo se recomienda si el tamaño de blob es pequeño, ya que todo el contenido del blob se carga en memoria. Por lo general, es preferible usar un tipo `Stream` o `CloudBlockBlob`. Para más información, consulte [Uso de simultaneidad y memoria](#trigger---concurrency-and-memory-usage) que apareció anteriormente en este artículo.