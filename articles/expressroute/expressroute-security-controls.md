---
title: 'Azure ExpressRoute: Controles de seguridad'
description: Lista de comprobación de controles de seguridad para evaluar Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079928"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de seguridad para los servicios de Azure ExpressRoute

En este artículo, se explican los controles de seguridad integrados en Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Red

| Control de seguridad | Sí/No | Notas |
|---|---|--|
| Compatibilidad con punto de conexión de servicio| N/D |  |
| Compatibilidad con inserción de red virtual| N/D | |
| Compatibilidad con el aislamiento de red y los firewalls| Sí | Cada cliente está contenido en su propio dominio de enrutamiento y tunelizado a su propia red virtual |
| Compatibilidad con la tunelización forzada| N/D | Mediante el Protocolo de puerta de enlace de borde (BGP). |

## <a name="monitoring--logging"></a>Supervisión y registro

| Control de seguridad | Sí/No | Notas|
|---|---|--|
| Compatibilidad con la supervisión de Azure (Log Analytics, Application Insights, etc.)| Sí | Consulte [Supervisión, métricas y alertas de ExpressRoute ](expressroute-monitoring-metrics-alerts.md).|
| Registro y auditoría del plano de administración y de control| Sí |  |
| Registro y auditoría del plano de datos| Sin |   |

## <a name="identity"></a>Identidad

| Control de seguridad | Sí/No | Notas|
|---|---|--|
| Authentication| Sí | Cuenta de servicio de Gateway for Microsoft (GWM) (controlador); acceso Just-in-Time (JIT) para desarrollo y operaciones. |
| Authorization|  Sí |Cuenta de servicio de Gateway for Microsoft (GWM) (controlador); acceso Just-in-Time (JIT) para desarrollo y operaciones. |

## <a name="data-protection"></a>Protección de datos

| Control de seguridad | Sí/No | Notas |
|---|---|--|
| Cifrado del lado servidor en reposo: Claves administradas por Microsoft |  N/D | ExpressRoute no almacena datos de clientes. |
| Cifrado del lado servidor en reposo: claves administradas por el cliente (BYOK) | N/D |  |
| Cifrado de nivel de columna (Azure Data Services)| N/D | |
| Cifrado en tránsito (por ejemplo, cifrado de ExpressRoute, cifrado en la red virtual y cifrado de red virtual a red virtual)| Sin | |
| Llamadas API cifradas| Sí | Mediante [Azure Resource Manager](../azure-resource-manager/index.yml) y HTTPS. |


## <a name="configuration-management"></a>Administración de configuración

| Control de seguridad | Sí/No | Notas|
|---|---|--|
| Compatibilidad con la administración de configuración (control de versiones de configuración, etc.)| Sí | Mediante el proveedor de recursos de red (NRP). |

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [controles de seguridad integrados en los servicios de Azure](../security/fundamentals/security-controls.md).