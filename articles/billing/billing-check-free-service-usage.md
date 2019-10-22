---
title: Supervisión y seguimiento del uso de los servicios gratuitos de Azure
description: Aprenda a comprobar el uso de los servicios gratuitos en Azure Portal.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ad9d7aa700a0c86f8153936b935f6d48e02b2fa4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375882"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Comprobación del uso de los servicios gratuitos incluidos con la cuenta gratuita de Azure

No se le cobrarán los servicios que se incluyen de forma gratuita en la cuenta gratuita de Azure, a menos que supere los límites de los mismos. Para no superarlos, puede usar Azure Portal para realizar el seguimiento del uso de los servicios gratuitos.

## <a name="check-usage-in-the-azure-portal"></a>Comprobación del uso en Azure Portal

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).

2.  Busque **Suscripciones**.

    ![Captura de pantalla que muestra la búsqueda de suscripciones en el portal](./media/billing-check-usage-of-free-services/billing-search-subscriptions.png)

3.  Seleccione la suscripción que se creó cuando se registró para obtener la cuenta gratuita de Azure.

4.  Desplácese hacia abajo para encontrar la tabla que muestra el uso de los servicios gratuitos.

    ![Captura de pantalla que muestra el uso de los servicios gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    La tabla tiene las columnas siguientes.

* **Medidor:** identifica la unidad de medida del recurso que se consume.
* **Uso y límite:** uso y límite del mes actual para el medidor.
* **Estado:** estado de uso del servicio. En función del uso, puede tener uno de los siguientes estados:
  * **No está en uso:** no ha usado el medidor o el uso de este no llega al sistema de facturación.
  * **Superado en \<fecha>:** ha superado el límite del medidor el \<Fecha>.
  * **Superación improbable:** es poco probable que supere el límite del medidor.
  * **Lo supera en \<fecha >:** es probable que supere el límite del medidor en \<Fecha >.

> [!IMPORTANT]
>
> Servicios gratuitos que solo están disponibles para la suscripción que se creó cuando se registró para obtener la cuenta gratuita de Azure. Si no puede ver la tabla de servicios gratuitos en la página de información general de la suscripción, no estarán disponibles para la suscripción.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes
- [Actualización de una cuenta gratuita de Azure](billing-upgrade-azure-subscription.md)
