---
title: 'CLI de Azure Service Fabric: sfctl mesh deployment | Microsoft Docs'
description: Se describen los comandos de sfctl mesh deployment de CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668473"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Crea recursos de Service Fabric Mesh.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
| create | Crea una implementación de recursos de Service Fabric Mesh. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Crea una implementación de recursos de Service Fabric Mesh.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --input-yaml-files [obligatorio] | Rutas de acceso de archivos relativas o absolutas separadas por comas de todos los archivos yaml o ruta de acceso relativa o absoluta del directorio (recursivo) que contiene archivos yaml. |
| --parameters | Ruta de acceso relativa o absoluta al archivo yaml o un objeto json que contiene los parámetros que deben invalidarse. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestra este mensaje de ayuda y sale. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

### <a name="examples"></a>Ejemplos

Consolida e implementa todos los recursos en el clúster mediante la invalidación de los parámetros mencionados en el archivo yaml.

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Consolida e implementa todos los recursos en un directorio mediante la invalidación de los parámetros mencionados en el archivo yaml.

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e implementa todos los recursos de un directorio en el clúster mediante la invalidación de los parámetros, que se pasan directamente como objeto json.

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).