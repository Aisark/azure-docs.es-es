---
title: Autoservicio de cambios y reembolsos de reservas de Azure
description: Obtenga información sobre cómo puede obtener cambios o reembolsos para las reservas de Azure.
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 87f8a90b2213c5e78b456477e8be61d2f013fd71
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224117"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Autoservicio de cambios y reembolsos de reservas de Azure

Las reservas de Azure proporcionan flexibilidad para ayudar a satisfacer sus necesidades en constante cambio. Puede intercambiar una reserva por otra del mismo tipo. También puede devolver una reserva, hasta 50 000 USD al año, si ya no la necesita.

El autoservicio de intercambio y la funcionalidad de cancelación no están disponible para los clientes de Contrato Enterprise de US Government. Otros tipos de suscripción de US Government, como Pago por uso y CSP, sí se admiten.

Debe tener acceso de propietario en el pedido de reserva para realizar un cambio o reembolso en relación con una reserva existente.

## <a name="exchange-an-existing-reserved-instance"></a>Cambio de una instancia reservada existente

Puede cambiar la reserva con tres pasos rápidos en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Seleccione las reservas que quiere devolver y haga clic en **Intercambiar**.  
    ![Imagen de ejemplo que muestra las reservas para devolver](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Seleccione el producto de máquina virtual que quiere comprar y escriba la cantidad. Asegúrese de que el nuevo total de compra es mayor que el total de devolución. [Determine el tamaño adecuado antes de la compra](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Imagen de ejemplo que muestra el producto de la máquina virtual para comprar con un intercambio](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Revise y finalice la transacción.  
    ![Imagen de ejemplo que muestra el producto de la máquina virtual para comprar con un intercambio, completando la devolución](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Para devolver una reserva, vaya a **Detalles de la reserva** y haga clic en **Devolver**.

## <a name="how-transactions-are-processed"></a>Procesamiento de las transacciones

En primer lugar, Microsoft cancela la reserva existente y reembolsa la cantidad proporcional para esa reserva. Si hay un cambio, se procesa la compra nueva. Microsoft procesa los reembolsos mediante uno de los métodos siguientes, según el tipo de cuenta y el método de pago:

### <a name="enterprise-agreement-customers"></a>Clientes con contrato Enterprise

Se agrega el dinero al compromiso monetario para los cambios y devoluciones en caso de que se utilizara alguno en la compra original. Todas las facturas por encima del límite en relación con las compras originales se reabren y se vuelven a evaluar para asegurarse de que se utiliza el compromiso monetario. Si el término de compromiso monetario que se compró con la reserva ya no está activo, se agregará crédito al término de compromiso monetario de su contrato Entreprise actual. El crédito es válido durante 90 días a partir de la fecha de reembolso. El crédito no utilizado expira una vez transcurridos los 90 días.

Si la compra original se realizó como un uso por encima del límite, Microsoft emite una nota de crédito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagos mediante factura de pago por uso y programa CSP

La factura de compra de la reserva original se cancela y luego se crea una nueva factura para el reembolso. En el caso de los cambios, la nueva factura muestra el reembolso y la nueva compra. El importe del reembolso se ajusta con la compra. Si solo reembolsó una reserva, Microsoft se queda con la cantidad prorrateada y se ajusta a una futura compra de reserva.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes con tarjeta de crédito de pago por uso

La factura original se cancela y se crea una nueva factura. El dinero se devuelve a la tarjeta de crédito que se utilizó para la compra original. Si ha cambiado la tarjeta, [póngase en contacto con el soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Directivas de cancelación, intercambio y reembolso

Azure tiene estas directivas en caso de cancelaciones, intercambios y reembolsos.

**Directivas de intercambio**

- Puede devolver varias de las reservas existentes para comprar una reserva nueva del mismo tipo. No puede intercambiar las reservas de un tipo por otro. Por ejemplo, no puede devolver una reserva de máquina virtual para comprar una reserva de SQL.
- Solo los propietarios de una reserva pueden procesar un intercambio. [Obtenga información sobre cómo agregar o cambiar los usuarios que pueden administrar una reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Un intercambio se procesa como un reembolso y una recompra: se crean diferentes transacciones para la cancelación y la nueva compra. El importe de la reserva prorrateada se reembolsa por las reservas que canjee. Se le cobrará por completo la nueva compra. El importe de la reserva prorrateada es el valor residual prorrateado diario de la reserva que se devuelve.
- Puede intercambiar o reembolsar reservas incluso si el contrato Enterprise utilizado para comprar la reserva ha expirado y se ha renovado como un nuevo contrato.
- Puede cambiar cualquier propiedad de reserva, como el tamaño, la región, la cantidad y el plazo, con un intercambio.
- El nuevo total de compra debe igual o mayor que la cantidad devuelta.
- La nueva reserva comprada en el marco del intercambio tiene un nuevo plazo a partir del momento del intercambio.
- No hay ninguna penalización ni límites anuales para los intercambios.

**Directivas de reembolso**
- Si cancela una reserva, podría aplicarse una tarifa por terminación anticipada del 12 %.
- El reembolso que recibe por una cancelación es el saldo prorrateado restante menos la tarifa por terminación anticipada del 12 %. Para cancelar, vaya a la reserva en Azure Portal y seleccione **Reembolso**.
- La cantidad total de reembolso no puede superar los 50 000 dólares estadounidenses en una ventana gradual de 12 meses.
- Los reembolsos se calculan según el precio más bajo de su precio de compra o en el precio actual de la reserva.
- Solo los propietarios de una reserva pueden procesar un reembolso. [Obtenga información sobre cómo agregar o cambiar los usuarios que pueden administrar una reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft se reserva el derecho a cobrar una penalización del 12 % por cualquier devolución. La penalización no se cobra actualmente, pero se hará en el futuro.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Intercambio de almacenamiento no premium por almacenamiento premium

Puede intercambiar una reserva comprada para un tamaño de máquina virtual que no es compatible con el almacenamiento premium por un tamaño de máquina virtual que sí lo es. Por ejemplo, un _F1_ por un _F1s_. Para hacer el intercambio, vaya a los detalles de la reserva y haga clic en **Intercambiar**. El intercambio no restablece el plazo de la instancia reservada ni crea una nueva transacción.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo administrar una reserva, consulte [Administración de Azure Reservations](billing-manage-reserved-vm-instance.md).
- Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:
    - [¿Qué es Azure Reservations?](billing-save-compute-costs-reservations.md)
    - [Administración de reservas en Azure](billing-manage-reserved-vm-instance.md)
    - [Información sobre cómo se aplica el descuento por la reserva](billing-understand-vm-reservation-charges.md)
    - [Información sobre el uso de reservas para suscripciones de pago por uso](billing-understand-reserved-instance-usage.md)
    - [Información sobre el uso de reservas para la inscripción Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costos de software de Windows no incluidos con reservas](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](/partner-center/azure-reservations)
