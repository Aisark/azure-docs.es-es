---
title: Lista de todos los trabajos de Azure Import/Export | Microsoft Docs
description: Descubra cómo enumerar todos los trabajos del servicio Azure Import/Export de una suscripción.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478698"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumeración de los trabajos del servicio Azure Import/Export
Para enumerar todos los trabajos de una suscripción, llame a la operación [List Jobs](/rest/api/storageimportexport/jobs) (Enumerar trabajos). `List Jobs` devuelve una lista de trabajos, así como los siguientes atributos:

-   El tipo de trabajo (importación o exportación)

-   El estado actual del trabajo

-   La cuenta de almacenamiento asociada al trabajo

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
