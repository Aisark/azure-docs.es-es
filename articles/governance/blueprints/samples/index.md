---
title: Índice de ejemplos de planos técnicos
description: Índice de cumplimiento y ejemplos de entornos estándar para la implementación con Azure Blueprints.
author: DCtheGeek
manager: carmonm
ms.service: blueprints
ms.topic: sample
ms.date: 08/20/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: 884777bce761f6b16581a28038bf9a28ce6a3801
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650517"
---
# <a name="azure-blueprints-samples"></a>Ejemplos de Azure Blueprints

En la tabla siguiente se incluyen vínculos a ejemplos de Azure Blueprints. Cada ejemplo tiene calidad de producción y está listo para su implementación inmediata para ayudarle a satisfacer sus requisitos de cumplimiento normativo.

## <a name="standards-based-blueprint-samples"></a>Ejemplos de planos técnicos basados en estándares

|  |  |
|---------|---------|
| [IRS 1075](./irs-1075/index.md) | Proporciona barreras de seguridad para garantizar el cumplimiento con la norma IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Proporciona barreras de seguridad para garantizar el cumplimiento con la norma ISO 27001. |
| [ISO 27001: servicios compartidos](./iso27001-shared/index.md) | Proporciona un conjunto de patrones de infraestructura compatibles y una directiva de protección que ayuda a la obtención de la certificación ISO 27001. |
| [Cargas de trabajo de App Service Environment y SQL Database compatibles con ISO 27001](./iso27001-ase-sql-workload/index.md) | Proporciona una infraestructura adicional para el ejemplo de plano técnico de los [servicios compartidos de la norma ISO 27001](./iso27001-shared/index.md). |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Proporciona barreras de seguridad para garantizar el cumplimiento con la norma NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Proporciona un conjunto de directivas que ayuda a lograr la conformidad con PCI DSS v3.2.1. |
| [Gobernanza de UK OFFICIAL y UK NHS](./ukofficial/index.md) | Proporciona un conjunto de patrones de infraestructura compatibles y una directiva de protección que ayuda a la obtención de la certificación UK OFFICIAL y UK NHS. |
| [Fundamentos de CAF](./caf-foundation/index.md) | Proporciona un conjunto de controles para ayudarle a administrar su propia nube en consonancia con [Microsoft Cloud Adoption Framework para Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index.md). |
| [Zona de aterrizaje de migración de CAF](./caf-migrate-landing-zone/index.md) | Proporciona un conjunto de controles para ayudarle a configurar la migración de la primera carga de trabajo y para administrar su propia nube en consonancia con [Microsoft Cloud Adoption Framework para Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index.md). |

## <a name="samples-strategy"></a>Estrategia de ejemplos

![Estrategia de ejemplos de planos técnicos](../media/blueprint-samples-strategy.png)

Los planos técnicos de la zona de aterrizaje de migración de CAF y de fundamentos de CAF asumen que el cliente prepara una suscripción individual limpia existente para migrar los recursos locales o las cargas de trabajo a Azure.
(Región A y B en la figura anterior).  

Hay una oportunidad de iterar en los planos técnicos de ejemplo y buscar patrones de las personalizaciones que solicita el cliente. También existe la oportunidad de abordar de forma proactiva los planos técnicos específicos del sector, como los servicios financieros y el comercio electrónico (extremo superior de la región B). Del mismo modo, se prevé la creación de planos técnicos para consideraciones de arquitectura complejas, tales como suscripciones múltiples, alta disponibilidad, recursos entre regiones y clientes que implementan controles sobre las suscripciones y recursos existentes (regiones C y D).

Hay planos técnicos de ejemplo que abordan el escenario del cliente donde los requisitos de cumplimiento son elevados y las complejidades de la arquitectura son altas (región E en la figura anterior). La región F anterior es la que abordarán los clientes y asociados al aprovechar los planos técnicos de ejemplo y personalizarlos en función de sus necesidades únicas.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [ciclo de vida del plano técnico](../concepts/lifecycle.md)
- Descubra cómo utilizar [parámetros estáticos y dinámicos](../concepts/parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](../concepts/sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](../concepts/resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../how-to/update-existing-assignments.md).
- Puede consultar la información de [solución de problemas generales](../troubleshoot/general.md) para resolver los problemas durante la asignación de un plano técnico.