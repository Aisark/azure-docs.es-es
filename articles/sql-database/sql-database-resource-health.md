---
title: Uso de Azure Resource Health para supervisar el estado de SQL Database | Microsoft Docs
description: Use Azure Resource Health para supervisar el estado de SQL Database, ayudar a diagnosticar problemas y obtener soporte técnico cuando un problema de Azure afecta a los recursos de SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483066"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Uso de Resource Health para solucionar problemas de conectividad para Azure SQL Database

## <a name="overview"></a>Información general

[Resource Health](../service-health/resource-health-overview.md#getting-started) para SQL Database le ayuda a diagnosticar sin un problema de Azure afecta a los recursos de SQL y, en su caso, a obtener soporte técnico. Le informa acerca del mantenimiento actual y pasado de los recursos y le ayuda a mitigar los problemas. Resource Health le proporciona soporte técnico si necesita ayuda con los problemas de los servicios de Azure.

![Información general](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Comprobaciones de mantenimiento

Resource Health determina el estado del recurso de SQL al examinar el estado correcto o erróneo de los inicios de sesión para el recurso. Actualmente, Resource Health para el recurso de SQL DB solo examina los errores de inicio de sesión debido a errores del sistema y no a errores del usuario. El estado de Resource Health se actualiza cada 1 a 2 minutos.

## <a name="health-states"></a>Estados de mantenimiento

### <a name="available"></a>Disponible

Un estado de **Disponible** significa que Resource Health no ha detectado errores de inicios de sesión debido a errores del sistema en el recurso de SQL.

![Disponible](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

Un estado de **Degradado** significa que Resource Health ha detectado una mayoría de inicios de sesión correctos, pero también algunos errores. Probablemente estos sean errores transitorios de inicio de sesión. Para reducir el impacto de los problemas de conexión provocados por errores transitorios de inicio de sesión, implemente la [lógica de reintento](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) en el código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>No disponible

Un estado de **No disponible** significa que Resource Health ha detectado errores constantes de inicio de sesión al recurso de SQL. Si el recurso permanece en este estado durante un largo período, póngase en contacto con soporte técnico.

![No disponible](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconocido

Este estado de mantenimiento indica que Resource Health no ha recibido información sobre este recurso durante más de 10 minutos. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante en el proceso de solución de problemas. Si el recurso se ejecuta según lo esperado, el estado del recurso cambia a Disponible al cabo de unos minutos. Si tiene problemas con el recurso, el estado de mantenimiento Desconocido podría sugerir que un evento de la plataforma afecta al recurso.

![Desconocido](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Información histórica

Puede obtener acceso al historial de mantenimiento de hasta 14 días en la sección Historial de estado de Resource Health. La sección también contendrá el motivo del tiempo de inactividad (si está disponible) para los tiempos de inactividad notificados por Resource Health. Actualmente, Azure muestra el tiempo de inactividad para el recurso de SQL Database con una granularidad de dos minutos. Es probable que el tiempo de inactividad real sea de menos de un minuto: el promedio es de 8 s.

### <a name="downtime-reasons"></a>Motivos del tiempo de inactividad

Cuando SQL Database experimenta un tiempo de inactividad, se realizan análisis para determinar un motivo. Cuando esté disponible, el motivo del tiempo de inactividad se notifica en la sección Historial de estado de Resource Health. Normalmente, los motivos del tiempo de inactividad se publican 30 minutos después de un evento.

#### <a name="planned-maintenance"></a>Mantenimiento planeado

La infraestructura de Azure realiza periódicamente un mantenimiento planeado: actualización de componentes de hardware o software en el centro de datos. Mientras la base de datos se somete a mantenimiento, SQL puede terminar algunas conexiones existentes y rechazar otras nuevas. Los errores de inicio de sesión sufridos durante un mantenimiento planeado suelen ser transitorios y la [lógica de reintento](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ayuda a reducir el impacto. Si sigue experimentando errores de inicio de sesión, póngase en contacto con soporte técnico.

#### <a name="reconfiguration"></a>Reconfiguración

Las reconfiguraciones se consideran condiciones transitorias y se espera que se produzcan de vez en cuando. Estos eventos pueden desencadenarse por errores de software y hardware o de equilibrio de carga. Cualquier aplicación de producción cliente que se conecte a un servicio de base de datos en la nube debe implementar una [lógica de reintento](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) de conexión sólida, ya que podría ayudar a mitigar estas situaciones y, en general, debería hacer que los errores sean transparentes para el usuario final.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [lógica de reintento para errores transitorios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en SQL Database](./sql-database-connectivity-issues.md)
- Más información sobre [la configuración de alertas de Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Introducción a [Resource Health](../service-health/resource-health-overview.md)
- [Preguntas frecuentes de Resource Health](../service-health/resource-health-faq.md)
