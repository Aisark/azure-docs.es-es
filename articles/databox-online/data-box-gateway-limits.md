---
title: Límites de Azure Data Box Gateway | Microsoft Docs
description: Se describen los límites del sistema y los tamaños recomendados de Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60755227"
---
# <a name="azure-data-box-gateway-limits"></a>Límites de Azure Data Box Gateway

Tenga en cuenta estos límites cuando implemente y use su solución de Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Límites de servicio de Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Límites de dispositivos de Data Box Gateway

En la tabla siguiente se describen los límites del dispositivo de Data Box Gateway.

| DESCRIPCIÓN | Valor |
|---|---|
|No. de archivos por dispositivo |100 millones <br> El límite es ~ 25 millones de archivos para cada 2 TB de espacio en disco con un límite máximo de 100 millones |
|No. de recursos compartidos por dispositivo |24 |
|No. de recursos compartidos por contenedor de Azure Storage |1 |
|Tamaño máximo de archivo escrito en un recurso compartido|Para un dispositivo virtual de 2 TB, el tamaño máximo de archivo es de 500 GB. <br> El tamaño máximo de archivo aumenta con el tamaño del disco de datos en la relación anterior hasta que alcanza un máximo de 5 TB. |

## <a name="azure-storage-limits"></a>Límites de almacenamiento de Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Advertencias al cargar los datos

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Límites de tamaño objeto y de tamaño de cuenta de almacenamiento en Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Límites de tamaño de objeto de Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Preparación para implementar Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
