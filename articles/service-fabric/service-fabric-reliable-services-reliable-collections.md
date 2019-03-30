---
title: Introducción a Reliable Collections en los servicios con estado de Azure Service Fabric | Microsoft Docs
description: Los servicios con estado de Service Fabric proporcionan colecciones confiables que le permiten escribir aplicaciones en la nube altamente disponibles, escalables y de baja latencia.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 4ed76b207db4712058b5524cd1b31fd65b0e19a4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664419"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introducción a Reliable Collections en los servicios con estado de Azure Service Fabric

Reliable Collections le permite escribir aplicaciones en la nube altamente disponibles, escalables y de baja latencia como si estuviese escribiendo aplicaciones para un solo equipo. Las clases del espacio de nombres **Microsoft.ServiceFabric.Data.Collections** proporcionan un conjunto de colecciones que automáticamente hacen que el estado tenga una elevada disponibilidad. Los desarrolladores solo necesitan programar en las API de Reliable Collections y permiten que Reliable Collections administren el estado local y el replicado.

La diferencia clave entre Reliable Collections y otras tecnologías de alta disponibilidad (por ejemplo, Redis, Azure Table service y Azure Queue service) es que el estado se guarda localmente en la instancia del servicio mientras también se ofrece una elevada disponibilidad. Esto significa que:

* Todas las lecturas son locales, lo que provoca una latencia baja y lecturas de alto rendimiento.
* Todas las escrituras producen el número mínimo de operaciones de E/S de red, lo cual provoca una baja latencia y escrituras de alto rendimiento.

![Imagen de la evolución de colecciones](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Reliable Collections se puede considerar como la evolución natural de las clases **System.Collections** : un nuevo conjunto de colecciones que están diseñadas para la nube y aplicaciones de varios equipos sin aumentar la complejidad para los desarrolladores. De esta forma, Reliable Collections tienen los estados siguientes:

* Replicado: se replican los cambios de estado para obtener una alta disponibilidad.
* Conservado: los datos se conservan en el disco para obtener una mayor durabilidad frente a las interrupciones a gran escala (por ejemplo, un corte de alimentación del centro de datos).
* Dado que la escritura persiste y se replica, no se puede crear una colección de confianza ReliableDictionary, ReliableQueue o de otro tipo volátiles que solo guarden datos en la memoria.
* Asincrónico: las API son asincrónicas para asegurarse de que los subprocesos no se bloquean durante las E/S.
* Transaccional: las API usan la abstracción de transacciones para que pueda administrar fácilmente varias instancias de Reliable Collections dentro de un servicio.

Reliable Collections proporciona garantías de homogeneidad sólidas de fábrica con el fin de facilitar el razonamiento sobre el estado de la aplicación.
La coherencia fuerte se consigue asegurándose de que las confirmaciones de las transacciones finalizan únicamente tras registrar toda la transacción en un cuórum mayoritario de réplicas, incluida la principal.
Para conseguir una coherencia más débil, las aplicaciones pueden efectuar la confirmación al cliente o al solicitante antes de devolver la confirmación asincrónica.

Las API de Reliable Collections son una evolución de las API de colecciones simultáneas (que se encuentran en el espacio de nombres **System.Collections.Concurrent** ):

* Asincrónico: devuelve una tarea debido a que, a diferencia de las colecciones simultáneas, las operaciones se replican y se guardan.
* Ningún parámetro de salida: usa `ConditionalValue<T>` para devolver un valor y un `bool` en lugar de parámetros de salida. `ConditionalValue<T>` es similar a `Nullable<T>`, pero no requiere que T sea una estructura.
* Transacciones: utiliza un objeto de transacción para permitir que el usuario agrupe acciones en varias colecciones confiables en una transacción.

Por el momento, el espacio de nombres **Microsoft.ServiceFabric.Data.Collections** contiene tres colecciones:

* [Diccionario de confianza](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa una colección replicada, transaccional y asincrónica de pares clave/valor. De forma similar a **ConcurrentDictionary**, la clave y el valor pueden ser de cualquier tipo.
* [Cola de confianza](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa una cola estricta replicada, transaccional y asincrónica de tipo primero en entrar primero en salir (FIFO). De forma similar a **ConcurrentQueue**, el valor puede ser de cualquier tipo.
* [Cola simultánea de confianza](service-fabric-reliable-services-reliable-concurrent-queue.md): representa una mejor cola de ordenación replicada, transaccional y asincrónica para obtener un alto rendimiento. De forma similar a **ConcurrentQueue**, el valor puede ser de cualquier tipo.

## <a name="next-steps"></a>Pasos siguientes

* [Recomendaciones y directrices de Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabajo con Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transacciones y bloqueos](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Administración de datos
  * [Copia de seguridad y restauración](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Serialización de colección confiable](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialización y actualización](service-fabric-application-upgrade-data-serialization.md)
  * [Configuración del administrador de estado confiable](service-fabric-reliable-services-configuration.md)
* Otros
  * [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
  * [Referencia para desarrolladores de colecciones confiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
