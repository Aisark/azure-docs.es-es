---
title: Refuerce su seguridad con Azure Security Center | Microsoft Docs
description: Este artículo ayuda a reforzar la seguridad mediante la supervisión de los recursos en Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603772"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Refuerce su seguridad con Azure Security Center
Este artículo le ayuda a reforzar la seguridad. Use las funcionalidades de supervisión de Azure Security Center para asegurarse de que la seguridad de los recursos es tan estricta como sea posible y para supervisar que cumple con las directivas establecidas.

## <a name="how-do-you-strengthen-your-security-posture"></a>¿Cómo puede reforzar la seguridad?
Con frecuencia se piensa que supervisar es observar y esperar que se produzca un evento, para así poder reaccionar ante la situación. Reforzar la seguridad se refiere a contar con una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización.

Después de habilitar las [directivas de seguridad](tutorial-security-policy.md) para los recursos de una suscripción, Security Center analizará la seguridad de los recursos para identificar vulnerabilidades potenciales. La información acerca de la configuración de la red está disponible de inmediato. Dependiendo del número de máquinas virtuales y de los equipos que tenga con el agente instalado, la recopilación de información sobre máquinas virtuales y la configuración del equipo, como el estado de actualización de seguridad y la configuración del sistema operativo, puede tardar una hora o más en estar disponible. Puede ver una lista completa de los problemas y formas de proteger su red y corregir riesgos en el icono de **recomendaciones**.

Puede ver el estado de seguridad de sus recursos, además de cualquier problema, en función del tipo de recurso:

- Para supervisar el estado de los recursos del equipo y las aplicaciones, y recibir recomendaciones para mejorar su seguridad, consulte [Protección de las aplicaciones y las máquinas en Azure Security Center](security-center-virtual-machine-protection.md).
- Para supervisar los recursos de red, como las máquinas virtuales, los grupos de seguridad de red y los puntos de conexión, y recibir recomendaciones para mejorar su seguridad, consulte [Protección de las redes en Azure Security Center](security-center-network-recommendations.md) para obtener más información. 
- Para supervisar los recursos de datos y almacenamiento, como los servidores de SQL y las cuentas de almacenamiento, y recibir recomendaciones para mejorar su seguridad, consulte [Protecting Azure SQL service and data in Azure Security Center](security-center-sql-service-recommendations.md) (Protección del servicio y los datos de SQL de Azure en Azure Security Center) para obtener más información. 
- Para supervisar los recursos de identidad y acceso, incluidos los permisos de cuenta y MFA, y recibir recomendaciones para mejorar su seguridad, consulte [Supervisión de la identidad y el acceso en Azure Security Center](security-center-identity-access.md) para obtener más información. 
- Para supervisar el acceso Just-in-Time a los recursos, consulte [Administrar el acceso a máquina virtual mediante Just-In-Time](security-center-just-in-time.md) para obtener más información. 


Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md).



![Icono de estado de seguridad de los recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Consulte también
En este artículo, aprendió a usar las funcionalidades de supervisión en Azure Security Center. Para obtener más información sobre Azure Security Center, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](tutorial-security-policy.md): Aprenda a configurar opciones de seguridad en Azure Security Center.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): Aprenda a administrar y responder a las alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.