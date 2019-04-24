---
title: Comprender el límite de gasto de Azure | Microsoft Docs
description: Describe cómo funciona el límite de gasto de Azure y cómo quitarlo
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: c234f75ec752ec79e9f2a051f79e91bdde81de15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371025"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprender el límite de gasto de Azure y cómo quitarlo

El límite de gasto de Azure existe para evitar gastos por encima de la cantidad de crédito. Todos los clientes nuevos que se registren para las ofertas de evaluación o las que incluyan créditos para varios meses, tienen activado el límite de gasto de forma predeterminada. El límite de gasto es $0. No se puede cambiar. El límite de gasto no está disponible para tipos de suscripción como suscripciones de pago por uso y planes con compromiso. Vea la [lista completa de ofertas de Azure y la disponibilidad del límite de gasto](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>¿Qué ocurre cuando alcanzo el límite de gasto?

Cuando el uso dé lugar a cobros que superen las cantidades mensuales de la oferta incluidas con su suscripción, los servicios que implementó se deshabilitan durante el resto de ese período de facturación. 

Por ejemplo, cuando gasta todo el crédito incluido con su suscripción, los servicios Cloud Services que implementó se quitan de la producción y las máquinas virtuales de Azure se detienen y se desasignan. Los datos de sus cuentas de almacenamiento están accesibles únicamente en modo de solo lectura.

Al principio del siguiente período de facturación, si su oferta de suscripción incluyera créditos durante varios meses, se volvería a habilitar su suscripción de forma automática. Después, puede volver a implementar los servicios Cloud Services y tener acceso total a las cuentas de almacenamiento y bases de datos.

Le enviaremos notificaciones por correo electrónico cuando alcance el límite de gasto de su suscripción. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/Subscriptions) y verá notificaciones sobre las suscripciones que han alcanzado el límite de gasto.

Si tiene una evaluación gratuita y alcanza el límite de gasto, puede [actualizar a pago por uso](billing-upgrade-azure-subscription.md) para quitar el límite de gasto y hacer que la suscripción se vuelva a habilitar automáticamente.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Eliminación del límite de gasto en el Centro de cuentas

Puede quitar el límite de gasto en cualquier momento siempre que haya un método de pago válido asociado a la suscripción. Para las ofertas que tienen crédito para varios meses, también puede volver a habilitar el límite de gasto al principio del siguiente período de facturación.

Para quitar el límite de gasto, siga estos pasos:

1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/Subscriptions).
1. Seleccione una suscripción. Si la suscripción se ha deshabilitado porque se alcanzó el límite de gasto, haga clic en esta notificación: "La suscripción alcanzó el límite de gasto y se deshabilitó para evitar que se realicen más cargos". De lo contrario, haga clic en **Quitar límite de gasto** en el área **ESTADO DE LA SUSCRIPCIÓN**.
1. Seleccione una opción que sea apropiada para usted.

![Selección de una opción para quitar el límite de gasto](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opción|Efecto|
|-------|-----|
|Quitar el límite de gasto indefinidamente|Quita el límite de gasto sin activarlo automáticamente al principio del siguiente período de facturación.|
|Quitar el límite de gasto para el período de facturación actual|Quita el límite de gasto de manera que se active automáticamente al principio del siguiente período de facturación.|

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>¿Por qué desearía quitar el límite de gasto?

El límite de gasto podría impedirle implementar o usar ciertos servicios de Microsoft o de terceros. A continuación se indican los escenarios donde debería quitar el límite de gasto en su suscripción.

* Planea implementar imágenes de primera entidad como Oracle y servicios como Azure DevOps Services. Este escenario hace que se supere el límite de gasto prácticamente de forma inmediata y que la suscripción se deshabilite.
* Tiene servicios que no se pueden interrumpir.
* Tiene servicios y recursos con configuraciones como direcciones IP virtuales que no quiere perder. Estas configuraciones se pierden cuando se desasignan los servicios y recursos.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>¿Cómo se activa el límite de gasto después de quitarlo?

Esta característica solo está disponible cuando se ha quitado el límite de gasto indefinidamente. Cámbielo para activarlo automáticamente al principio del siguiente período de facturación.

1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/Subscriptions).
1. Haga clic aquí en el banner amarillo para cambiar la opción de límite de gasto.
1. Elija **Activar el límite de gasto para el siguiente período de facturación \<fecha inicial del período de facturación\>**.

### <a name="how-do-i-set-a-custom-spending-limit"></a>¿Cómo se puede establecer un límite de gasto personalizado?

Los límites de gasto personalizados no están disponibles.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>¿Impide el límite de gasto todos los cobros de Azure?

[Algunos servicios externos publicados en Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) no se pueden usar con los créditos de una suscripción y pueden generar cargos independientes incluso si se ha establecido un límite de gasto. Como ejemplos se pueden citar las licencias de Visual Studio, Azure Active Directory Premium, los planes de soporte técnico y la mayoría de los servicios de marca de terceros. Al aprovisionar un nuevo servicio externo, se muestra una advertencia en la que se le informa de que los servicios se facturan por separado:

![Advertencia de compra de Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [crear una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).
