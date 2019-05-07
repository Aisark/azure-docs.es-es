---
title: Uso del análisis de Azure Storage para recopilar datos de métricas y registros | Microsoft Docs
description: Storage Analytics permite realizar un seguimiento de los datos de métricas para todos los servicios de almacenamiento y recopilar registros de Blob Storage, Queue Storage y Table Storage.
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b588ebe577e61014c6c2bbeaae751b2783dd6f80
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153920"
---
# <a name="storage-analytics"></a>Storage Analytics

El análisis de Azure Storage realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Para utilizar Storage Analytics, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde la [portal Azure](https://portal.azure.com). Para obtener más información, consulte [supervisar una cuenta de almacenamiento en Azure portal](storage-monitor-storage-account.md). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use la [Set Blob Service Properties](/rest/api/storageservices/set-blob-service-properties), [establecer propiedades del servicio cola](/rest/api/storageservices/set-queue-service-properties), [establecer propiedades de Table Service](/rest/api/storageservices/set-table-service-properties), y [establecer propiedades del servicio archivo](/rest/api/storageservices/Get-File-Service-Properties)operaciones para habilitar Storage Analytics para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API de Blob service y de Table service.

Storage Analytics tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Para obtener más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md).

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con Azure Storage, consulte [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Facturación para Storage Analytics

Todos los datos de métricas los escriben los servicios de una cuenta de almacenamiento. Como resultado, cada una de las operaciones de escritura realizadas por Storage Analytics es facturable. También lo es la cantidad de almacenamiento utilizado por los datos de métricas.

Son facturables las acciones siguientes realizadas por Storage Analytics:

* Solicitudes para crear blobs para el registro
* Solicitudes para crear entidades de tabla para las métricas

Si ha configurado una directiva de retención de datos, no se le cobrarán las transacciones de eliminación cuando Storage Analytics elimine los antiguos datos de métricas y de registro. Sin embargo, las transacciones de eliminación desde un cliente sí son facturables. Para obtener más información acerca de las directivas de retención, consulte [Establecer una directiva de retención de datos de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Descripción de las solicitudes facturables

Las solicitudes realizadas al servicio de almacenamiento de una cuenta son facturables o no facturables. Storage Analytics registra cada solicitud realizada a un servicio, incluyendo un mensaje de estado que indica cómo se administró la solicitud. De igual forma, Storage Analytics guarda las métricas para un servicio y para las operaciones de la API de dicho servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Todas estas características pueden ayudarle a analizar las solicitudes facturables, a llevar a cabo mejoras en la aplicación y a diagnosticar problemas en las solicitudes a los servicios. Para obtener más información sobre la facturación, consulte [Descripción de la facturación de Azure Storage: ancho de banda, transacciones y capacidad](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Al examinar los datos de Storage Analytics, puede usar las tablas del tema [Operaciones y mensajes de estado registrados por Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) para determinar qué solicitudes son facturables. De esta manera, podrá comparar los datos de métricas y de registro con los mensajes de estado para ver si se le cobró por una solicitud determinada. También puede usar las tablas del tema anterior para investigar la disponibilidad de un servicio de almacenamiento o de una operación de API determinada.

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de una cuenta de almacenamiento en Azure Portal](storage-monitor-storage-account.md)
* [Métricas de Storage Analytics](storage-analytics-metrics.md)
* [Registro de Storage Analytics](storage-analytics-logging.md)
