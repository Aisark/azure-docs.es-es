---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160204"
---
La tabla siguiente enumera la posibles combinaciones de carga y captura de imágenes del sistema operativo de Windows generalizadas (gen.) y especializadas (espec.). Las combinaciones que se procesarán sin errores se indican mediante una letra Y y las que generarán errores se indican mediante una letra N. En la tabla siguiente, se proporcionan las causas y soluciones para los diferentes errores que se va a encontrar.

| SO | Cargar espec. | Cargar gen. | Capturar espec. | Capturar gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Windows espec. |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

