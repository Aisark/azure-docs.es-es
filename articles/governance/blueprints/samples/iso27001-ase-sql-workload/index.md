---
title: 'Ejemplo: Introducción al plano técnico para cargas de trabajo de ASE o SQL compatibles con ISO 27001'
description: Introducción y arquitectura del ejemplo de plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b17b07124cf4491a6c58e9be2b10f958e29217ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876087"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Introducción a un ejemplo de plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001

El ejemplo de plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001 proporciona una infraestructura adicional a la del ejemplo de plano técnico de los [servicios compartidos de la norma ISO 27001](../iso27001-shared/index.md).
Este plano técnico ayuda a los clientes a implementar arquitecturas basadas en la nube que ofrecen soluciones para escenarios que tienen requisitos de acreditación o de cumplimiento.

Hay dos ejemplos de planos técnicos para la norma ISO 27001: este y el ejemplo de plano técnico de los [servicios compartidos de la norma ISO 27001](../iso27001-shared/index.md).

> [!IMPORTANT]
> Este ejemplo depende de la infraestructura que se implementa con el ejemplo de plano técnico de los [servicios compartidos de la norma ISO 27001](../iso27001-shared/index.md). Debe implementarse en primer lugar.

## <a name="architecture"></a>Arquitectura

El ejemplo de plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001 implementa un entorno web basado en una plataforma como servicio. El entorno se puede emplear para hospedar varias aplicaciones web, API web e instancias de SQL Database conforme a los estándares de la norma ISO 27001. Este ejemplo de plano técnico se basa en el de los [servicios compartidos de la norma ISO 27001](../iso27001-shared/index.md).

![Diseño de ejemplo del plano técnico para cargas de trabajo de ASE y SQL compatibles con ISO 27001](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

Este entorno se compone de varios servicios de Azure que se usan para proporcionar una infraestructura de cargas de trabajo segura, totalmente supervisada y preparada para la empresa, que se basa en los estándares de la norma ISO 27001. Este entorno se compone de:

- Un rol de [control de acceso basado en rol](../../../../role-based-access-control/overview.md) (RBAC) que se llama DevOps que tiene permisos para implementar y administrar recursos en una instancia de [Azure App Service Environment](../../../../app-service/environment/intro.md) que implementa el ejemplo de plano técnico.
- [Directivas de Azure](../../../policy/overview.md) para bloquear los servicios que se pueden implementar en el entorno y denegar la creación de cualquier recurso de dirección IP pública (PIP)
- Una red virtual que contiene una única subred y que está emparejada a un entorno de [servicios compartidos](../iso27001-shared/index.md) que existe con anterioridad y que obliga a que todo el tráfico pase a través del firewall de los [servicios compartidos](../iso27001-shared/index.md). La red virtual hospeda los recursos siguientes:
  - Una instancia de [Azure App Service Environment](../../../../app-service/environment/intro.md) que se puede utilizar para hospedar una o varias aplicaciones web, API web o funciones.
  - Una instancia de [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) que usa un punto de conexión de servicio de red virtual para almacenar los secretos que usan las aplicaciones que se ejecutan en el entorno de la carga de trabajo.
  - Una instancia de servidor de [Azure SQL Database](../../../../sql-database/sql-database-technical-overview.md) que usa un punto de conexión de servicio de red virtual para hospedar las bases de datos que usan las aplicaciones en el entorno de la carga de trabajo.

## <a name="next-steps"></a>Pasos siguientes

Ya ha revisado la introducción y arquitectura del ejemplo de plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001. Ahora, visite los siguientes artículos para más información sobre la asignación de control y la implementación de este ejemplo:

> [!div class="nextstepaction"]
> [Plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con la norma ISO 27001: Asignación de controles](./control-mapping.md)
> [Plano técnico para cargas de trabajo de App Service Environment y SQL Database compatibles con la norma ISO 27001: Pasos de implementación](./deploy.md)

Artículos adicionales sobre planos técnicos y cómo utilizarlos:

- Más información sobre el [ciclo de vida del plano técnico](../../concepts/lifecycle.md)
- Descubra cómo utilizar [parámetros estáticos y dinámicos](../../concepts/parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](../../concepts/sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](../../concepts/resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../../how-to/update-existing-assignments.md).