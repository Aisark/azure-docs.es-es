---
title: Notificaciones de Azure Active Directory Identity Protection | Microsoft Docs
description: Obtenga información sobre cómo contribuyen las notificaciones a sus actividades de investigación.
services: active-directory
keywords: azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 482b69752cc889ff99c3d9082d3bc20a7caa6d76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294534"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificaciones de Azure Active Directory Identity Protection

Azure AD Identity Protection envía dos tipos de correos electrónicos de notificación automatizados para ayudar a administrar el riesgo del usuario y los eventos de riesgo:

- Correo electrónico de los usuarios en riesgo detectados
- Correo electrónico de resumen semanal

En este artículo se proporciona una introducción de ambos correos electrónicos de notificación.


## <a name="users-at-risk-detected-email"></a>Correo electrónico de los usuarios en riesgo detectados

En respuesta a una cuenta detectada en riesgo, Azure AD Identity Protection genera una alerta de correo electrónico con el asunto **Usuarios en riesgo detectados**. El correo electrónico incluye un vínculo al informe **[Usuarios marcados en riesgo](../reports-monitoring/concept-user-at-risk.md)**. Como práctica recomendada, debería investigar inmediatamente los usuarios en peligro.

![Correo electrónico de los usuarios en riesgo detectados](./media/notifications/01.png)


### <a name="configuration"></a>Configuración

Como administrador, puede establecer:

- **El nivel de riesgo que desencadena la generación de este correo electrónico**: de forma predeterminada, el nivel de riesgo se establece en "Alto" riesgo.
- **Los destinatarios de este correo electrónico**: de forma predeterminada, los destinatarios incluyen todos los administradores globales. Los administradores globales también pueden agregar otros administradores globales, administradores de seguridad y Lectores de seguridad como destinatarios.  


Para abrir el cuadro de diálogo relacionado, haga clic en **Alertas** en la sección **Configuración** de la página **Identity Protection**.

![Correo electrónico de los usuarios en riesgo detectados](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>Correo electrónico de resumen semanal

El correo electrónico de resumen semanal contiene un sumario de nuevos eventos de riesgo.  
 Incluye:

- Usuarios en riesgo

- Actividades sospechosas

- Puntos vulnerables detectados

- Vínculos a los informes relacionados en Identity Protection

    ![Corrección](./media/notifications/400.png "Corrección")

### <a name="configuration"></a>Configuración

Como administrador, puede desactivar la opción de envío de un correo electrónico de resumen semanal.

![Riesgos de usuario](./media/notifications/62.png "Riesgos de usuario")

Para abrir el cuadro de diálogo relacionado, haga clic en **Resumen semanal** en la sección **Configuración** de la página **Identity Protection**.

![Correo electrónico de los usuarios en riesgo detectados](./media/notifications/04.png)


## <a name="see-also"></a>Vea también

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
