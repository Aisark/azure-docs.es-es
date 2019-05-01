---
title: Transacciones y modos de bloqueo en Reliable Collections de Azure Service Fabric | Microsoft Docs
description: Transacciones y registros de Reliable State Manager y Reliable Collections de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774070"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transacciones y modos de bloqueo en Reliable Collections de Azure Service Fabric

## <a name="transaction"></a>Transacción
Una transacción es una secuencia de operaciones realizadas como una única unidad lógica de trabajo.
Una transacción debe presentar las siguientes propiedades ACID. (vea: https://technet.microsoft.com/library/ms190612)
* **Atomicidad**: una transacción debe ser una unidad atómica de trabajo. Es decir, o se modifican todos sus datos o ninguno de ellos.
* **Coherencia**: cuando ha finalizado, una transacción debe dejar todos los datos en un estado coherente. Todas las estructuras de datos internos deben ser correctas al final de la transacción.
* **Aislamiento**: las modificaciones realizadas por transacciones simultáneas se deben aislar de las modificaciones realizadas por otras transacciones simultáneas. El nivel de aislamiento usado para una operación dentro de ITransaction viene determinado por el elemento IReliableState que realiza la operación.
* **Durabilidad**: una vez concluida una transacción, sus efectos son permanentes en el sistema. Las modificaciones persisten incluso si se produce un error del sistema.

### <a name="isolation-levels"></a>Niveles de aislamiento
Un nivel de aislamiento define el grado en el que debe aislarse la transacción de las modificaciones que realicen otras transacciones.
Hay dos niveles de aislamiento que se admiten en Colecciones confiables:

* **Lectura repetible**: especifica que las instrucciones no puedan leer datos que se hayan modificado, pero que otras transacciones aún no han confirmado y que ninguna otra puede modificar los datos leídos por la actual hasta que esta finalice. Para obtener información, vea [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantánea**: especifica que los datos que ha leído cualquier instrucción de una transacción sean la versión coherente, desde el punto de vista transaccional, de los datos existentes al comienzo de la transacción.
  La transacción solo puede reconocer las modificaciones de datos que se confirmaron antes del inicio de la transacción.
  Las modificaciones de datos realizadas por otras transacciones después del inicio de la transacción actual no son visibles para las instrucciones que se ejecutan en la transacción actual.
  El efecto es como si las instrucciones de una transacción obtienen una instantánea de los datos confirmados tal como se encontraban al inicio de la transacción.
  Las instantáneas son coherentes entre colecciones confiables.
  Para obtener información, vea [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Colecciones confiables elige automáticamente el nivel de aislamiento que se usará para una operación de lectura determinada dependiendo del funcionamiento y el rol de la réplica.
A continuación, se encuentra la tabla que muestra los valores predeterminados de nivel de aislamiento para las operaciones Diccionario confiable y Cola confiable.

| Operación\rol | Principal | Secundario |
| --- |:--- |:--- |
| Lectura de una sola entidad |Repeatable Read |Instantánea |
| Enumeración, recuento |Instantánea |Instantánea |

> [!NOTE]
> Algunos ejemplos comunes de las operaciones de entidad única son `IReliableDictionary.TryGetValueAsync` y `IReliableQueue.TryPeekAsync`.
> 

El diccionario fiable y la cola fiable admiten la lectura de las escrituras.
En otras palabras, cualquier escritura dentro de una transacción será visible en una lectura posterior que pertenezca a la misma transacción.

## <a name="locks"></a>Bloqueos
En Reliable Collections, todas las transacciones implementan un riguroso bloqueo en dos fases: una transacción no libera los bloqueos que ha adquirido hasta que la transacción finaliza con una anulación o confirmación.

Diccionario confiable utiliza el bloqueo de nivel de fila en todas las operaciones de entidad única.
Cola confiable equilibra la simultaneidad para la propiedad FIFO transaccional estricta.
Cola confiable utiliza bloqueos de nivel de operación que permite una transacción con `TryPeekAsync` o `TryDequeueAsync` y una transacción con `EnqueueAsync` a la vez.
Tenga en cuenta que para conservar FIFO, si `TryPeekAsync` o `TryDequeueAsync` nunca observa que la Cola confiable está vacía, bloqueará también `EnqueueAsync`.

Las operaciones de escritura siempre tiene bloqueos exclusivos.
En las operaciones de lectura, el bloqueo depende de dos factores.
Cualquier operación de lectura realizada mediante aislamiento Snapshot no presenta bloqueo.
Cualquier operación Repeatable Read usa bloqueos compartidos de manera predeterminada.
Sin embargo, para cualquier operación de lectura que admita Repeatable Read, el usuario puede solicitar un bloqueo de actualización en lugar del bloqueo compartido.
El bloqueo de actualización es un bloqueo asimétrico que se isa para evitar una forma común de interbloqueo que se produce cuando varias transacciones bloquean recursos para una posible actualización en un momento posterior.

La matriz de compatibilidad de bloqueo se puede encontrar en la tabla siguiente:

| Solicitar \ concedido | None | Compartido | Actualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Compartido |Ningún conflicto |Ningún conflicto |Conflicto |Conflicto |
| Actualizar |Ningún conflicto |Ningún conflicto |Conflicto |Conflicto |
| Exclusivo |Ningún conflicto |Conflicto |Conflicto |Conflicto |

El argumento de tiempo de espera de las API de Reliable Collections se usa como una detección de interbloqueo.
Por ejemplo, dos transacciones (T1 y T2) están intentando leer y actualizar K1.
Es posible que se interbloqueen, porque ambas acaban disponiendo de bloqueo compartido.
En este caso, una o ambas operaciones superará el tiempo de espera.

Esta situación de interbloqueo es un buen ejemplo de cómo puede el bloqueo de actualización impedir interbloqueos.

## <a name="next-steps"></a>Pasos siguientes
* [Trabajo con Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificaciones de Reliable Services](service-fabric-reliable-services-notifications.md)
* [Copia de seguridad y restauración de Reliable Services (recuperación ante desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuración del administrador de estado confiable](service-fabric-reliable-services-configuration.md)
* [Referencia para desarrolladores de colecciones confiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

