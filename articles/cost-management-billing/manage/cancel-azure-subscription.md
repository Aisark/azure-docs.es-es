---
title: Cancelación de su suscripción de Azure | Microsoft Docs
description: Describe cómo cancelar la suscripción de Azure, como la suscripción a la evaluación gratuita
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e4ce97b69250d8af4c94e8eed8df9c9497e0bb46
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985992"
---
# <a name="cancel-your-azure-subscription"></a>Cancelación de su suscripción de Azure

Puede cancelar la suscripción de Azure en Azure Portal si ya no necesita la suscripción.

Antes de cancelar la suscripción:
* Realice una copia de seguridad de los datos. Por ejemplo, si está almacenando datos en Azure Storage o SQL, descargue una copia. Si tiene una máquina virtual, guarde una imagen de la misma localmente.
* Cierre los servicios. Vaya a la [página de recursos en el portal de administración](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) y **detenga** todas las máquinas virtuales, aplicaciones u otros servicios en ejecución.
* Considere la posibilidad de migrar los datos. Consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Elimine todos los recursos y todos los grupos de recursos. Es necesario eliminarlos para poder cancelar una suscripción. Los grupos de recursos deben eliminarse uno por uno. Durante la eliminación del grupo de recursos, debe confirmar la eliminación; para ello, escriba el nombre de este.
* Si tiene algún rol personalizado que haga referencia a esta suscripción en `AssignableScopes`, debe actualizar este rol personalizado para quitar la suscripción. Si intenta actualizar un rol personalizado después de cancelar una suscripción, es posible que obtenga un error. Para más información, consulte [Problemas con roles personalizados](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) y [Roles personalizados en los recursos de Azure](../../role-based-access-control/custom-roles.md).

Si cancela un plan de soporte técnico de Azure de pago, se le facturará el resto del período de la suscripción. Para más información, vea [Soporte técnico de Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelación de la suscripción en Azure Portal

1. Seleccione su suscripción en la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Seleccione la suscripción que quiere cancelar.
3. Seleccione **Introducción** y, luego, seleccione **Cancelar suscripción**.
    ![Captura de pantalla que muestra el botón Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Siga las indicaciones y finalice la cancelación.


## <a name="who-can-cancel-a-subscription"></a>¿Quién puede cancelar una suscripción?

En la tabla siguiente se describe el permiso necesario para cancelar una suscripción.

|Tipo de suscripción     |Quién puede cancelar  |
|---------|---------|
|Suscripciones creadas al registrarse en Azure a través del sitio web de Azure. Por ejemplo, cuando se registra para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), como una [cuenta con tarifas de pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) o como un [suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador de cuenta, propietarios y colaboradores de la suscripción  |
|[Contrato Enterprise de Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) y [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Propietario de la cuenta, propietarios y colaboradores de la suscripción       |
|[Plan de Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) y [Azure Plan for DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Propietarios y colaboradores de la suscripción      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>¿Qué ocurre después de cancelar la suscripción?

Una vez que se cancela, la facturación se detiene inmediatamente. Sin embargo, pueden transcurrir hasta 10 minutos hasta que la cancelación se muestre en el portal. Si cancela en medio de un período de facturación, enviamos la factura final en la fecha de factura típica una vez finalizado el período.

Después de cancelar, los servicios se deshabilitan. Esto significa que las máquinas virtuales se desasignan, las direcciones IP temporales se liberan y el almacenamiento es de solo lectura.

Esperamos 90 días antes de eliminar permanentemente los datos, por si necesita acceder a ellos o cambia de opinión. No se le cobrará por conservar los datos. Para más información, consulte el [Microsoft Trust Center: How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centro de confianza de Microsoft: Administración de los datos).

## <a name="reactivate-subscription"></a>Reactivación de la suscripción

Si cancela su suscripción de pago por uso accidentalmente, puede [volver a activarla en el Centro de cuentas](subscription-disabled.md).

Si su suscripción no es de pago por uso, para reactivar la suscripción, póngase en contacto con el soporte técnico en los 90 días posteriores a la cancelación.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).
