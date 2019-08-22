---
title: Evento de error de tarea de Azure Batch | Microsoft Docs
description: Referencia del evento de error de tarea de Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: aa56b0ead8edc17efe74547f6374a3f8888970b5
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624195"
---
# <a name="task-fail-event"></a>Evento de error en tareas

 Este evento se emite cuando una tarea se completa con error. Actualmente todos los códigos de salida distintos de cero se consideran errores. Este evento se emitirá *además de* un evento de tarea completada y se puede usar para detectar cuando una tarea presenta un error.


 En el ejemplo siguiente se muestra el cuerpo de un evento de error en tareas.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nombre del elemento|type|Notas|
|------------------|----------|-----------|
|jobId|Cadena|Identificador del trabajo que contiene la tarea.|
|id|Cadena|Identificador de la tarea.|
|taskType|Cadena|Tipo de la tarea. Puede ser "JobManager", que indica que es una tarea del administrador de trabajos, o "User", que indica que no lo es. Este evento no se emite para tareas de preparación de trabajos, tareas de liberación de trabajo ni tareas de inicio.|
|systemTaskVersion|Int32|Se trata del contador interno de reintentos de una tarea. De manera interna, el servicio de Batch puede reintentar una tarea para tener en cuenta los problemas transitorios. Estos problemas pueden incluir errores internos de programación o intentos de recuperación a partir de nodos de proceso en estado no válido.|
|[nodeInfo](#nodeInfo)|Tipo complejo|Contiene información sobre el nodo de ejecución en que se ejecutó la tarea.|
|[multiInstanceSettings](#multiInstanceSettings)|Tipo complejo|Especifica que la tarea es una tarea de instancias múltiples que requiere varios nodos de proceso.  Consulte [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para detalles.|
|[constraints](#constraints)|Tipo complejo|Restricciones de ejecución que se aplican a esta tarea.|
|[executionInfo](#executionInfo)|Tipo complejo|Contiene información sobre la ejecución de la tarea.|

###  <a name="nodeInfo"></a> nodeInfo

|Nombre del elemento|type|Notas|
|------------------|----------|-----------|
|poolId|Cadena|Identificador del grupo en que se ejecutó la tarea.|
|nodeId|Cadena|Identificador del nodo en que se ejecutó la tarea.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nombre del elemento|type|Notas|
|------------------|----------|-----------|
|numberOfInstances|Int32|Número de nodos de proceso que requiere la tarea.|

###  <a name="constraints"></a> constraints

|Nombre del elemento|type|Notas|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Número máximo de veces que se puede reintentar la tarea. El servicio de Batch reintenta una tarea su el código de salida es distinto de cero.<br /><br /> Tenga en cuenta que este valor controla específicamente el número de reintentos. El servicio de Batch intentará una vez la tarea y podría reintentarla hasta alcanzar este límite. Por ejemplo, si el conteo de reintentos máximo es 3, Batch intenta una tarea hasta 4 veces (un intento inicial y 3 reintentos).<br /><br /> Si el conteo de intentos máximo es 0, el servicio de Batch no reintenta las tareas.<br /><br /> Si el conteo de intentos máximo es -1, el servicio de Batch reintenta las tareas sin ningún límite.<br /><br /> El valor predeterminado es 0 (sin ningún reintento).|


###  <a name="executionInfo"></a> executionInfo

|Nombre del elemento|type|Notas|
|------------------|----------|-----------|
|startTime|DateTime|Hora a la que empezó a ejecutarse la tarea. "En ejecución" se refiere al estado **running**, por lo que si la tarea especifica archivos de recursos o paquetes de aplicación, la hora inicial refleja la hora a la que la tarea empezó a descargarlos o implementarlos.  Si se reinició o reintentó la tarea, es la hora más reciente a la que comenzó a ejecutarse.|
|endTime|DateTime|Hora a la que finalizó la tarea.|
|exitCode|Int32|Código de salida de la tarea.|
|retryCount|Int32|Cantidad de veces que el servicio de Batch reintentó la tarea. La tarea se reintenta si el código de salida es distinto de cero, hasta el valor MaxTaskRetryCount especificado.|
|requeueCount|Int32|Cantidad de veces que el servicio de Batch volvió a poner en cola la tarea como resultado de una solicitud de usuario.<br /><br /> Cuando el usuario quita nodos de un grupo (ya sea debido a que cambia o disminuye el tamaño del grupo), o bien cuando se deshabilita el trabajo, el usuario puede especificar que las tareas en ejecución en los nodos se vuelvan a poner en cola para su ejecución. Este conteo hace un seguimiento de las veces en que la tarea se volvió a poner en cola por estos motivos.|
