---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186215"
---
## <a name="what-is-queue-storage"></a>¿Qué es Queue Storage?
El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento.

El almacenamiento en cola suele usarse para realizar las siguientes tareas:

* Creación de trabajo pendiente para el procesamiento asincrónico
* Transferencia de mensajes de un rol web de Azure a un rol de trabajo de Azure

## <a name="queue-service-concepts"></a>Conceptos del servicio Cola
El servicio Cola contiene los siguientes componentes:

![Cola1](./media/storage-queue-concepts-include/queue1.png)

* **Formato de dirección URL**: Las colas son direccionables mediante el siguiente formato de dirección URL:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    La siguiente dirección URL dirige a una cola del diagrama:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Cuenta de almacenamiento**: Todo el acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../articles/storage/common/storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento.
* **Cola**: una cola contiene un conjunto de mensajes. Todos los mensajes deben encontrarse en una cola. Tenga en cuenta que el nombre de la cola debe ir en minúsculas. Para más información, consulte [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Mensaje**: un mensaje, en cualquier formato, de hasta 64 KB. El tiempo máximo que un mensaje puede permanecer en la cola es de 7 días.

