---
title: Consideraciones de almacenamiento de Azure Functions
description: Conozca los requisitos de almacenamiento de Azure Functions y aprenda a cifrar los datos almacenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f094996ca44ec36d46330e54eac56b28794ef22e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358183"
---
# <a name="storage-considerations-for-azure-functions"></a>Consideraciones de almacenamiento de Azure Functions

Azure Functions necesita una cuenta de Azure Storage para crear una instancia de la aplicación de funciones. Esta aplicación de funciones puede utilizar los siguientes servicios de almacenamiento:


|Servicio de Storage  | Uso de Functions  |
|---------|---------|
| [Almacenamiento de blobs de Azure](../storage/blobs/storage-blobs-introduction.md)     | Mantener el estado de los enlaces y las teclas de función.  <br/>También se utiliza en la [central de tareas de Durable Functions](durable/durable-functions-task-hubs.md). |
| [Archivos de Azure](../storage/files/storage-files-introduction.md)  | Recurso compartido de archivos que se utiliza para almacenar y ejecutar el código de la aplicación de funciones en un [plan de consumo](functions-scale.md#consumption-plan). |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Se utiliza en la [central de tareas de Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Se utiliza en la [central de tareas de Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Cuando usa el plan de hospedaje de consumo, los archivos de configuración de enlace y el código de la función se almacenan en Azure File Storage en la cuenta de almacenamiento principal. Si elimina la cuenta de almacenamiento principal, este contenido se suprimirá y no se podrá recuperar.

## <a name="storage-account-requirements"></a>Requisitos de la cuenta de almacenamiento

Al crear una aplicación de funciones, debe crear o vincular una cuenta de Azure Storage de uso general compatible con Blob, Queue y Table Storage. El motivo es que Azure Functions usa Azure Storage con ciertas operaciones; por ejemplo, para administrar los desencadenadores y ejecutar las funciones de registro. Algunas cuentas de almacenamiento no admiten el uso de colas y tablas; por ejemplo, las cuentas de almacenamiento solo para blobs, Azure Premium Storage y las cuentas de almacenamiento de uso general con replicación ZRS. Estas cuentas no compatibles no se muestran en la hoja Cuenta de almacenamiento cuando se crea una aplicación de funciones.

Para más información sobre los tipos de cuenta de almacenamiento, consulte [Introducción de los servicios Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

Aunque puede usar una cuenta de almacenamiento existente con la aplicación de funciones, debe asegurarse de que cumple estos requisitos. En las cuentas de almacenamiento que se generan durante el flujo de creación de la aplicación de funciones, el cumplimiento de los requisitos de la cuenta de almacenamiento está garantizado.  

## <a name="storage-account-guidance"></a>Guía de la cuenta de almacenamiento

Cada aplicación de función requiere una cuenta de almacenamiento para funcionar. Si se elimina la cuenta, la aplicación de funciones no se ejecutará. Para más información, consulte este artículo sobre [la solución de problemas relacionados con el almacenamiento](functions-recover-storage-account.md). Estas otras consideraciones que se indican a continuación también se aplican a la cuenta de almacenamiento que se utiliza en las aplicaciones de funciones.

### <a name="storage-account-connection-setting"></a>Configuración de la conexión de la cuenta de almacenamiento

La conexión de la cuenta de almacenamiento se mantiene en [el ajuste de la aplicación AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Cuando se generan las claves de almacenamiento, debe actualizarse la cadena de conexión de la cuenta de almacenamiento. [Más información sobre la administración de las claves de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Cuentas de almacenamiento compartidas

Una misma cuenta de almacenamiento puede estar compartida entre varias aplicaciones de funciones sin que se produzcan problemas. Por ejemplo, en Visual Studio, puede desarrollar varias aplicaciones con el Emulador de Azure Storage. En este caso, el emulador actúa como una cuenta de almacenamiento única. La cuenta de almacenamiento que se utiliza en la aplicación de funciones puede usarse también para almacenar los datos de la aplicación. Sin embargo, este enfoque no siempre resulta conveniente en los entornos de producción.

### <a name="optimize-storage-performance"></a>Optimización del rendimiento de almacenamiento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Cifrado de datos de almacenamiento

Azure Storage cifra todos los datos de las cuentas de almacenamiento en reposo. Para más información, consulte [Cifrado de Azure Storage para datos en reposo](../storage/common/storage-service-encryption.md).

De manera predeterminada, los datos se cifran con claves administradas por Microsoft. Para tener un mayor control sobre las claves de cifrado, puede proporcionar claves administradas por el cliente que puede usar para el cifrado de datos de archivos y blobs. Estas claves deben estar presentes en Azure Key Vault para que Azure Functions pueda acceder a la cuenta de almacenamiento. Para más información, consulte [Configuración de claves administradas por el cliente con Azure Key Vault mediante Azure Portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las opciones de hospedaje de Azure Functions.

> [!div class="nextstepaction"]
> [Escalado y hospedaje de Azure Functions](functions-scale.md)


