---
title: Provisión de detalles de contacto de seguridad en Azure Security Center | Microsoft Docs
description: En este documento se muestra cómo proporcionar detalles de contacto de seguridad en Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 1a66ea200082f60a3a763c6a4e2bdea62ec473d8
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921002"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Proporcionar detalles de contacto de seguridad en Azure Security Center
Azure Security Center recomendará que proporcione los detalles de contacto de seguridad para su suscripción de Azure si no lo ha hecho ya. Esta información la utilizará Microsoft para ponerse en contacto con usted si Microsoft Security Response Center (MSRC) detecta que un tercero no autorizado o ilegal ha accedido a los datos de clientes. MSRC lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros.

Se envía una notificación de correo electrónico en la primera repetición diaria de una alerta y solo en aquellas con un nivel de gravedad elevado. Las preferencias de correo electrónico solo pueden configurarse para las directivas de suscripción. Los grupos de recursos de una suscripción heredan esta configuración. Las alertas solo están disponibles en el nivel Estándar de Azure Security Center.

Se envían las notificaciones de alerta por correo electrónico:
- Solo para alertas de gravedad alta
- A un único destinatario de correo electrónico por cada tipo de alerta por día  
- Se envían no más de 3 mensajes de correo electrónico a un destinatario único en un solo día
- Cada mensaje de correo electrónico contiene una única alerta, no una agregación de alertas
 
Por ejemplo, si ya se ha enviado un mensaje de correo electrónico para avisarle de un ataque de RDP, no recibirá otro mensaje de correo electrónico acerca de un ataque de RDP en el mismo día, incluso si se desencadena otra alerta. 

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.

## Configuración de notificaciones de correo electrónico para alertas <a name="email"></a>

1. En el portal, seleccione **Pricing & settings** (Precios y configuración).
1. Haga clic en la suscripción.
1. Haga clic en **Notificaciones por correo electrónico**.

> [!NOTE]
> Si va a implementar una recomendación, en **Recomendaciones**, seleccione **Proporcionar detalles de contacto de seguridad** y elija la suscripción de Azure de la que quiere proporcionar información de contacto. Así se abren las **Notificaciones por correo electrónico**.

   ![Proporcionar datos de los contactos de seguridad][2]

   * Escriba la dirección de correo electrónico del contacto de seguridad o direcciones separadas por comas. No hay un límite en el número de direcciones de correo electrónico que se pueden escribir.
   * Escriba un número de teléfono internacional de seguridad.
   * Para recibir correos electrónicos de alertas de gravedad alta, active la opción **Send me emails about alert**(Enviar correos electrónicos de alertas).
   * Tiene la opción de enviar notificaciones por correo electrónico a los propietarios de la suscripción (Administrador de servicios clásico y Coadministradores, además del rol Propietario de RBAC en el ámbito de la suscripción).
   * Seleccione **Aceptar** para aplicar la información de contacto de seguridad a su suscripción.

## <a name="see-also"></a>Consulte también
Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](tutorial-security-policy.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](https://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
