---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 01d84914682d40b97c3d480a753c8b966cf61acc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553972"
---
En la tabla siguiente se describen los límites predeterminados para Azure Storage. El límite de *entrada* hace referencia a todos los datos de las solicitudes que se envían a una cuenta de almacenamiento. El límite de *salida* hace referencia a todos los datos de las respuestas que se reciben de una cuenta de almacenamiento.

| Recurso | Límite predeterminado |
| --- | --- |
| Número de cuentas de almacenamiento por región y suscripción, que incluye las cuentas estándar y Premium | 250 |
| Capacidad de la cuenta de almacenamiento máximo | 2 PB de Estados Unidos y Europa, 500 TB para todas las demás regiones, lo que incluye el Reino Unido |
| Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento | Sin límite |
| Tasa de solicitud total<sup>1</sup> por cuenta de almacenamiento | 20 000 solicitudes por segundo |
| Entrada máxima<sup>1</sup> por cuenta de almacenamiento (regiones de Estados Unidos) | 10 Gbps si RA-GRS/GRS está habilitado, 20 Gbps para LRS/ZRS<sup>2</sup> |
| Entrada máxima<sup>1</sup> por cuenta de almacenamiento (regiones de Estados Unidos) | 5 Gbps si RA-GRS/GRS está habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Salida máxima para uso general v2 y las cuentas de almacenamiento de blobs (todas las regiones) | 50 Gbps |
| Salida máxima para las cuentas de almacenamiento de uso general v1 (regiones de Estados Unidos) | 20 Gbps si RA-GRS/GRS está habilitado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Salida máxima para las cuentas de almacenamiento de uso general v1 (regiones de Estados Unidos) | 10 Gbps si RA-GRS/GRS está habilitado, 15 Gbps en el caso de LRS/ZRS<sup>2</sup> |

<sup>1</sup>cuentas de azure Standard Storage son compatibles con los límites más altos para la entrada de solicitud. Para solicitar un aumento en los límites de cuenta para entrada, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [Las opciones de replicación de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluyen:
* **RA-GRS**: almacenamiento con redundancia geográfica con acceso de lectura. Si RA-GRS está habilitada, los destinos de salida para la ubicación secundaria son idénticos a los de la ubicación principal.
* **GRS**: almacenamiento con redundancia geográfica. 
* **ZRS**: almacenamiento con redundancia de zona.
* **LRS**: Almacenamiento con redundancia local. 

> [!NOTE]
> Se recomienda que use una cuenta de almacenamiento de uso general v2 para la mayoría de los escenarios. Puede actualizar fácilmente un uso general v1 o una cuenta de Azure Blob storage a una cuenta de uso general v2 sin tiempo de inactividad y sin necesidad de copiar los datos.
>
> Para obtener más información sobre las cuentas de almacenamiento de Azure, consulte [Introducción a la cuenta de almacenamiento](../articles/storage/common/storage-account-overview.md). 

Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, puede compilar la aplicación de forma que use varias cuentas de almacenamiento. A continuación, puede dividir los objetos de datos en esas cuentas de almacenamiento. Para obtener información sobre los precios por volumen, consulte [precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Todas las cuentas de almacenamiento se ejecutan en la topología de red plana y admiten los objetivos de escalabilidad y rendimiento descritos en este artículo, independientemente de su frecha de creación. Para obtener más información acerca de la arquitectura de red plana de Azure Storage y de la escalabilidad, vea [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Microsoft Azure Storage: Un servicio de almacenamiento en nube altamente disponible de gran coherencia).

