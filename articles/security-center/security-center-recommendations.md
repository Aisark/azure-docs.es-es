---
title: Administración de recomendaciones de seguridad en Azure Security Center | Microsoft Docs
description: Este documento explica cómo las recomendaciones del Centro de seguridad de Azure ayudan a proteger los recursos de Azure y a cumplir con las directivas de seguridad.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109195"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Administración de recomendaciones de seguridad en el Centro de seguridad de Azure
En este documento se explica cómo usar las recomendaciones del Centro de seguridad de Azure para proteger los recursos de Azure.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  Este documento no es una guía paso a paso.
>
>

## <a name="what-are-security-recommendations"></a>¿Cuáles son las recomendaciones de seguridad?
El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios.

## <a name="implementing-security-recommendations"></a>Implementación de recomendaciones de seguridad
### <a name="set-recommendations"></a>Obtención de recomendaciones
En [Establecimiento de directivas de seguridad en Azure Security Center](tutorial-security-policy.md), aprenderá lo siguiente:

* Configurar directivas de seguridad.
* Activar la recopilación de datos.
* Elegir las recomendaciones que verá como parte de la directiva de seguridad.

Las recomendaciones de directiva actuales se centran en las actualizaciones del sistema, las reglas de línea de base, los programas antimalware, los [grupos de seguridad de red](../virtual-network/security-overview.md) en subredes y las interfaces de red, la auditoría de bases de datos de SQL, el cifrado de datos transparente de bases de datos de SQL y los firewalls de aplicaciones web.  [Establecimiento de directivas de seguridad](tutorial-security-policy.md) proporciona una descripción de cada opción de recomendación.

### <a name="monitor-recommendations"></a>Supervisión de recomendaciones
Después de establecer una directiva de seguridad, el Centro de seguridad analiza el estado de seguridad de los recursos, con el fin de identificar vulnerabilidades potenciales. El icono **Recomendaciones** de **Información general** permite conocer el número total de recomendaciones que identifica Security Center.

![Icono Recomendaciones][1]

Para ver los detalles de cada recomendación, seleccione el icono **Recomendaciones** en **Información general**. **Recomendaciones** se abre.

![Filtrar recomendaciones][2]

Puede filtrar las recomendaciones. Para ello, seleccione **Filtro** en la hoja **Recomendaciones**. Se abrirá la hoja **Filtro** , donde podrá seleccionar los valores de gravedad y de estado que quiera ver.


* **RECOMENDACIONES**: La recomendación.
* **PROTEGER EL IMPACTO DE PUNTUACIÓN**:
* **RECURSO**: enumera los recursos a los que se aplica la recomendación.
* **BARRAS DE ESTADO**:  describe la gravedad de una recomendación concreta:
   * **Alto (rojo)**: existe una vulnerabilidad en un recurso importante (como una aplicación, una máquina virtual o un grupo de seguridad de red) y requiere atención.
   * **Medio (naranja)**: existe una vulnerabilidad y se requieren pasos adicionales o no críticos para eliminarla o completar un proceso.
   * **Baja (azul)**: existe una vulnerabilidad que se debe abordar, pero no requiere una atención inmediata. (De manera predeterminada no se muestran las recomendaciones bajas, pero si desea verlas, puede filtrar por ellas). 
   * **Correcto (verde)**:
   * **No está disponible (gris)**:
 


> [!NOTE]
> Deberá comprender los [modelos de implementación clásico y de Resource Manager](../azure-classic-rm.md) para los recursos de Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Aplicación de recomendaciones
> Después de revisar todas las recomendaciones, decida cuáles son las primeras que debe aplicar. Se recomienda usar la clasificación de gravedad como parámetro principal para evaluar las recomendaciones que se deben aplicar primero.



## <a name="next-steps"></a>Pasos siguientes
En este documento, se han presentando las recomendaciones de seguridad del Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](tutorial-security-policy.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga más información sobre cómo supervisar el estado de sus recursos en Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](https://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
