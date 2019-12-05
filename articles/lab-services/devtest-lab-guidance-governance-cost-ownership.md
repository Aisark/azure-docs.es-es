---
title: Administración del costo y la propiedad en Azure DevTest Labs
description: En este artículo se proporciona información que ayudará a optimizar el costo y alinear la propiedad en su entorno.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561660"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Gobernanza de la infraestructura de Azure DevTest Labs: administrar costo y propiedad
El costo y la propiedad son las principales preocupaciones cuando considere la posibilidad de crear los entornos de desarrollo y pruebas. En esta sección, encontrará información que le ayudará a optimizar el costo y alinear la propiedad en su entorno.

## <a name="optimize-for-cost"></a>Optimizar el costo

### <a name="question"></a>Pregunta
¿Cómo puedo optimizar el costo dentro de mi entorno de DevTest Labs?

### <a name="answer"></a>Respuesta
Hay una serie de características integradas de DevTest Labs que le ayudarán a optimizar el costo. Vea los artículos sobre [administración de costos, umbrales](devtest-lab-configure-cost-management.md) [y directivas](devtest-lab-set-lab-policy.md) para limitar las actividades de los usuarios. 

Cuando utilice DevTest Labs para cargas de trabajo de desarrollo y pruebas, puede considerar la posibilidad de utilizar el [Beneficio de suscripción Desarrollo/pruebas de Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) como parte de su contrato Enterprise. Como alternativa, si es un cliente de pago por uso, es posible que le interese la [oferta de pago por uso de DevTest](https://azure.microsoft.com/offers/ms-azr-0023p/).

Este enfoque le proporciona numerosas ventajas:

- Tarifas especiales de Desarrollo/pruebas más económicas en instancias de máquinas virtuales con Windows, Cloud Services, HDInsight, App Service y Logic Apps
- Tarifas geniales del Contrato Enterprise (EA) en otros servicios de Azure
- Acceso a imágenes exclusivas de Desarrollo y pruebas de la galería, que incluye Windows 8.1 y Windows 10
 
Solo los suscriptores activos de Visual Studio (suscripciones estándar, suscripciones de nube anuales y mensuales) pueden usar los recursos de Azure que se ejecutan en una suscripción Desarrollo/pruebas - Enterprise. No obstante, los usuarios finales pueden acceder también a la aplicación para aportar comentarios o hacer pruebas de aceptación. El uso de recursos en esta suscripción se restringe al desarrollo y la realización de pruebas de aplicaciones, y no se ofrece ninguna garantía sobre el tiempo de actividad.

Si decide usar la oferta de DevTest, tenga en cuenta que este beneficio es únicamente para desarrollo y pruebas de sus aplicaciones. El uso en el marco de la suscripción no incluye un Acuerdo de Nivel de Servicio con respaldo financiero, salvo para la utilización de Azure DevOps y HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definir un acceso basado en roles en toda la organización
### <a name="question"></a>Pregunta
¿Cómo puedo definir el control de acceso basado en roles para mis entornos DevTest Labs para asegurarme de que el departamento de TI puede regir al mismo tiempo que los desarrolladores y probadores pueden hacer su trabajo? 

### <a name="answer"></a>Respuesta
Hay un amplio modelo, sin embargo, habrá detalles específicos que dependerán de su organización.

El departamento de TI central debe poseer solo lo que es necesario y permitir que los equipos del proyecto y la aplicación tengan el nivel necesario de control. Normalmente, esto significa que esa departamento de TI central posee la suscripción y las controla las funciones de TI básicas, como las configuraciones de redes. El conjunto de **propietarios** para una suscripción debe ser pequeño. Estos propietarios pueden designar a propietarios adicionales cuando sea necesario, o aplicar directivas de nivel de suscripción, por ejemplo “sin dirección IP pública”.

Puede haber un subconjunto de usuarios que requieren acceso a través de una suscripción, como la compatibilidad con Tier1 o Tier2. En este caso, se recomienda dar a dichos usuarios acceso de **colaborador** para que puedan administrar los recursos, pero no puedan proporcionar acceso de usuario o ajustar las directivas.

Los propietarios del recurso de DevTest Labs deben estar cerca del equipo de proyecto o aplicación. De esta forma entenderán sus requisitos en cuanto a las máquinas y el software necesario. En la mayoría de las organizaciones, el propietario de este recurso de DevTest Labs suele ser el responsable de desarrollo o del proyecto. Este propietario puede administrar los usuarios y las directivas en el entorno de laboratorio y puede administrar todas las máquinas virtuales en el entorno de DevTest Labs.

Los miembros del equipo de proyecto o aplicación se deben agregar al rol de usuarios de DevTest Labs. Estos usuarios pueden crear máquinas virtuales (en línea con las directivas de nivel de suscripción y de laboratorio). También pueden administrar sus propias máquinas virtuales. No pueden administrar las máquinas virtuales que pertenecen a otros usuarios.

Para obtener más información vea la documentación de [Scaffold empresarial de Azure: gobernanza de suscripción prescriptiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Pasos siguientes
Vea [Corporate policy and compliance](devtest-lab-guidance-governance-policy-compliance.md) (Cumplimiento y directiva corporativa).
