---
title: Compra de reservas de Azure con pagos por adelantado o mensuales
description: Aprenda a comprar reservas de Azure con pagos por adelantado o mensuales.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: 9bab6521d95579aa5b8fd1bd679e19537d88b706
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223718"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Compra de reservas con pagos mensuales

Hasta ahora, las reservas de Azure requerían el pago por adelantado. Ahora puede pagar las reservas mensualmente. A diferencia de la compra por adelantado (importe total), la opción de pago mensual divide el costo de la reserva en partes iguales cada mes del período. El costo total de las reservas por adelantado y mensuales es el mismo y no se pagan cargos adicionales por elegir el pago mensual.

El importe del pago mensual puede variar en función del tipo de cambio de su divisa en el mes en curso.

Los pagos mensuales están disponibles para:

- Máquinas virtuales
- Azure Storage
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- Impuesto sobre el timbre de App Service

Realice reservas en [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Ejemplo que muestra una reserva](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Al realizar una reserva puede ver el calendario de pagos. Haga clic en **Ver el calendario de pagos completo**.

![Ejemplo que muestra el calendario de pagos de las reservas](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Para ver el calendario de pagos después de la reserva, selecciónela, y haga clic en el **id. de pedido de reserva** y en la pestaña **Pagos**.

## <a name="view-payments-made"></a>Visualización de los pagos realizados

Puede ver los pagos realizados mediante las API, los datos de uso y el análisis de costos. En el caso de las reservas de pago mensual, el valor de la frecuencia aparece como **periódico** en los datos de uso y en Reservation Charges API. En el caso de las reservas pagadas por adelantado, el valor se muestra como **OneTime**.

El análisis de costos muestra las compras mensuales en la vista predeterminada. Aplique el filtro **compra** en **Charge type** (Tipo de cargo) y **periódico** en **Frecuencia** para ver todas las compras. Para ver solo las reservas, aplique un filtro de **Reserva**.

![Ejemplo que muestra los costos de las reservas en el análisis de costos](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Cambio al pago mensual en la renovación

Al renovar una reserva, puede cambiar la frecuencia de facturación a mensualmente.

## <a name="exchange-and-refunds"></a>Cambios y reembolsos

Al igual que otras reservas, con la facturación mensual se pueden reembolsar o cambiar. Actualmente puede enviar una solicitud de soporte técnico para iniciar un cambio o reembolso de una reserva adquirida con facturación mensual.

Al cambiar una reserva pagada mensualmente, el costo total de la nueva compra debe ser mayor que los pagos restantes que se cancelan de la reserva devuelta. No hay ningún otro límite ni tarifa por los cambios. Puede cambiar una reserva pagada por adelantado para comprar una nueva que se facture mensualmente. Sin embargo, el valor de la duración de la nueva reserva debe ser mayor que el prorrateado de la reserva que se va a devolver.

Si se cancela una reserva de pago mensual, es posible que Microsoft aplique un precio de cancelación a los pagos futuros que se hayan cancelado. Los pagos restantes confirmados se acumulan en el límite de reembolso de 50 000 USD.

Para más información sobre los cambios y los reembolsos, consulte [Autoservicio de cambios y reembolsos de reservas de Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las reservas, consulte [¿Qué es Azure Reservations?](billing-save-compute-costs-reservations.md)
- Para información sobre las tareas que debe realizar antes de adquirir una reserva, consulte [Determinación del tamaño adecuado de una máquina virtual antes de su adquisición](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).
