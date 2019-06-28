---
title: Actividad de validación en Azure Data Factory | Microsoft Docs
description: La actividad de validación no continúa la ejecución de la canalización hasta que valida el conjunto de datos adjunto con determinados criterios que el usuario especifica.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764329"
---
# <a name="validation-activity-in-azure-data-factory"></a>Actividad de validación en Azure Data Factory
Puede usar una validación en una canalización para asegurarse de que la canalización solo continúe la ejecución una vez que haya validado la existencia de la referencia del conjunto de datos adjunto, que cumple con los criterios especificados o que se haya alcanzado el tiempo de expiración.


## <a name="syntax"></a>Sintaxis

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propiedades de tipo

Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
Nombre | Nombre de la actividad de validación | Cadena | Sí |
Tipo | Debe establecerse en **Validación**. | Cadena | Sí |
dataset | La actividad bloqueará la ejecución hasta que haya validado la existencia de esta referencia del conjunto de datos y que cumple con los criterios especificados, o se haya alcanzado el tiempo de expiración. El conjunto de datos proporcionado debe admitir la propiedad "MinimumSize" o "ChildItems". | Referencia del conjunto de datos | Sí |
timeout | Especifica el tiempo de espera para que se ejecute la actividad. Si no se especifica ningún valor, el valor predeterminado es 7 días ("7.00:00:00"). El formato es d.hh:mm:ss | Cadena | Sin |
en reposo | Un retardo en segundos entre los intentos de validación. Si no se especifica ningún valor, el valor predeterminado es 10 segundos. | Entero | Sin |
childItems | Comprueba si la carpeta tiene elementos secundarios. Se puede establecer en -true: valida que la carpeta existe y que tiene elementos. Se bloquea hasta que al menos un elemento está presente en la carpeta o se alcanza el valor del tiempo de expiración. -false: valida que la carpeta existe y está vacía. Se bloquea hasta que la carpeta está vacía o hasta que se alcanza el valor del tiempo de expiración. Si no se especifica ningún valor, la actividad se bloqueará hasta que exista la carpeta o hasta que se alcance el tiempo de espera. | Boolean | Sin |
minimumSize | Tamaño mínimo de un archivo en bytes. Si no se especifica ningún valor, el valor predeterminado es 0 bytes. | Entero | Sin |


## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory:

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)
