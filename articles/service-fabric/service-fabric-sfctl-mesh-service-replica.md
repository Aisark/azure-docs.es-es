---
title: 'CLI de Azure Service Fabric: sfctl mesh service-replica'
description: Obtenga información sobre sfctl, la interfaz de la línea de comandos de Azure Service Fabric. Incluye una lista de comandos para obtener los detalles de la réplica de los recursos de una aplicación.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 219a691dd1b74ec2214c156018e6e3f62366f523
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645334"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtiene los detalles de la réplica y muestra las réplicas de un servicio determinado en un recurso de aplicación.

## <a name="commands"></a>Comandos:

|Get-Help|Descripción|
| --- | --- |
| list | Enumera todas las réplicas de un servicio. |
| show | Obtiene la réplica dada del servicio de una aplicación. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Enumera todas las réplicas de un servicio.

Obtiene la información sobre todas las réplicas de un servicio. La información incluye la descripción y otras propiedades de la réplica del servicio.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --app-name: nombre de la aplicación [obligatorio] | Nombre de la aplicación. |
| --service-name [obligatorio] | El nombre del servicio. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug | Aumente el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestre este mensaje de ayuda y salga. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Obtiene la réplica dada del servicio de una aplicación.

Obtiene la información sobre la réplica de servicio con el nombre especificado. La información incluye la descripción y otras propiedades de la réplica del servicio.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --app-name: nombre de la aplicación [obligatorio] | Nombre de la aplicación. |
| --name -n [obligatorio] | El nombre de la réplica de servicio. |
| --service-name [obligatorio] | El nombre del servicio. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug | Aumente el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestre este mensaje de ayuda y salga. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |


## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).