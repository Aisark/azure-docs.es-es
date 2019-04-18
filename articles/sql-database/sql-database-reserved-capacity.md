---
title: Pago por adelantado de los núcleos virtuales de Azure SQL Database para ahorrar dinero | Microsoft Docs
description: Aprenda a comprar capacidad reservada de Azure SQL Database para ahorrar en los costos de proceso.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ec9bd3ee106571484c513c2d005a374a90c1d17e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359725"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Pago por adelantado por recursos de proceso de SQL Database con capacidad reservada de Azure SQL Database

Ahorre dinero con Azure SQL Database al pagar por adelantado los recursos de proceso en lugar de elegir el pago por uso. Con la capacidad reservada, se compromete de antemano en Azure SQL Database durante un período de uno a tres años para obtener un descuento considerable en los costos de proceso. Para comprar capacidad reservada de SQL Database, debe especificar la región de Azure, el tipo de implementación, el nivel de servicio y el período.

No es necesario asignar la reserva a instancias específicas de SQL Database (bases de datos únicas, grupos elásticos o instancias administradas). Al igualarse las instancias de SQL Database en ejecución o las recién implementadas se obtendrá la ventaja automáticamente. Al comprar una reserva, se adelanta el pago de los costos de proceso durante un período de uno a tres años. En cuando se compra una reserva, los costos de proceso de SQL Database que coincidan con los atributos de reserva dejan de pagarse según las tarifas de pago por uso. La reserva no cubre los cargos por software, redes o almacenamiento asociados a la instancia de SQL Database. Al final del plazo de reserva, la ventaja en la facturación expira y las instancias de SQL Database se facturan según los precios de pago por uso. Las reservas no se renuevan automáticamente. Para información sobre precios, consulte el artículo sobre la [oferta de capacidad reservada de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Puede comprar capacidad reservada de Azure SQL Database en [Azure Portal](https://portal.azure.com). Para comprar capacidad reservada de SQL Database:

- Debe tener rol de propietario al menos en una suscripción Enterprise o de Pago por uso.
- En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción.
- En el caso del programa del Proveedor de soluciones en la nube (CSP), los únicos que pueden comprar la capacidad reservada de SQL Database son los agentes de administración o de ventas.

Para información sobre cómo se les cobra a los clientes de empresa y a los de pago por uso las compras de reservas, consulte [Información sobre el uso de reservas de Azure para la inscripción Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) y [Información sobre el uso de reservas de Azure para suscripciones de pago por uso](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinación del tamaño adecuado de SQL antes de la compra

El tamaño de la reserva debe basarse en el proceso total de las bases de datos únicas existentes o que se van a implementar, de los grupos elásticos o de instancias administradas de una región específica y con el mismo nivel de rendimiento y la misma generación de hardware.

Por ejemplo, supongamos que está ejecutando un grupo elástico de propósito general Gen5 de 16 núcleos virtuales y dos bases de datos únicas Gen5 de 4 núcleos virtuales críticas para la empresa. Además, supongamos que planea implementar en el próximo mes un grupo elástico Gen5 de propósito general y 16 núcleos virtuales adicional, y otro de 32 núcleos virtuales crítico para la empresa. Y también supongamos que sabe que necesitará estos recursos durante al menos 1 año. En este caso debe comprar una reserva de 1 año de una instancia de Gen5 con 32 núcleos virtuales (2 × 16) para una base de datos única y un grupo elástico de uso general y otra reserva de 1 año de una instancia de Gen 5 con 40 núcleos virtuales (2 × 4 + 32) para una base de datos única y un grupo elástico críticos para la empresa.

## <a name="buy-sql-database-reserved-capacity"></a>Compra de capacidad reservada de SQL Database

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** y, en el panel Seleccione el tipo de producto, elija **SQL Database** para adquirir una nueva reserva para SQL Database.
4. Rellene todos los campos obligatorios. Las bases de datos únicas, los grupos elásticos o las instancias administradas nuevas que coincidan con los atributos seleccionados serán aptos para el descuento en la capacidad reservada. El número real de instancias de SQL Database que obtienen el descuento depende el ámbito y la cantidad seleccionados.

   ![Captura de pantalla antes del envío de la compra de capacidad reservada de SQL Database](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Campo      | DESCRIPCIÓN|
    |:------------|:--------------|
    |NOMBRE        |Nombre de esta reserva.|
    |Subscription|La usada para pagar la reserva de capacidad reservada de SQL Database. Los costos anticipados por la capacidad reservada de SQL Database se cobran mediante el método de pago de la suscripción. El tipo de suscripción debe ser Contrato Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P) o Pago por uso (números de oferta: MS-AZR-0003P o MS-AZR-0023P). Para una suscripción Enterprise, los cargos se deducen del saldo de compromiso monetario de la inscripción o se cobran como uso por encima del límite. Para la suscripción Pago por uso, los cargos se cobran en el método de pago de tarjeta de crédito o factura de la suscripción.|
    |Ámbito       |El ámbito de la reserva de núcleos virtuales puede cubrir una suscripción o varias (ámbito compartido). Si selecciona: <br/><br/>**Suscripción única**: el descuento por la reserva de núcleos virtuales se aplica a las instancias de SQL Database de esta suscripción. <br/><br/>**Suscripción compartida**: el descuento por la reserva de núcleos virtuales se aplica a las instancias de SQL Database en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.|
    |Region      |La región de Azure que abarca la capacidad reservada de SQL Database.|
    |Tipo de implementación|El tipo de recurso de SQL para el que desea adquirir la reserva.|
    |Nivel de rendimiento|El nivel de servicio de las instancias de SQL Database.
    |Término        |Un año o tres años.|
    |Cantidad    |Número de instancias que se compran dentro de la capacidad reservada de SQL Database. La cantidad es el número de instancias de SQL Database en ejecución a las que se aplica el descuento de facturación. Por ejemplo, si tiene 10 instancias de SQL Database en la región Este de EE. UU., debería especificar 10 como cantidad para maximizar la ventaja para todas las máquinas en ejecución. |
    |||

5. Revise el costo de la reserva de capacidad reservada de SQL Database en la sección **Costos**.
6. Seleccione **Comprar**.
7. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

## <a name="cancellations-and-exchanges"></a>Cancelaciones e intercambios

Si tiene que cancelar la reserva de capacidad de SQL Database, podría aplicarse una tarifa del 12 % por terminación anticipada. Los reembolsos se basan en el precio más bajo de su precio de compra o en el precio actual de la reserva. Los reembolsos están limitados a 50 000 dólares al año. El reembolso que recibe es el saldo prorrateado restante menos la tarifa de terminación anticipada del 12 %. Para solicitar una cancelación, vaya a la reserva de Azure Portal y seleccione **Reembolso** para crear una solicitud de soporte técnico.

Si necesita cambiar la reserva de capacidad de SQL Database por una región, un tipo de implementación, un nivel de rendimiento o un período diferentes, puede intercambiarla por otra reserva de valor igual o mayor. La fecha de inicio del período de la nueva reserva no se extiende desde la reserva intercambiada. Al crear la nueva reserva empieza el período de 1 o 3 años. Para solicitar un intercambio, vaya a la reserva en Azure Portal y seleccione **Cambio** para crear una solicitud de soporte técnico.

Para obtener más información acerca de cómo las reservas de direcciones de exchange o un reembolso, consulte [intercambios de reserva y los reembolsos](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>Flexibilidad de tamaño del núcleo virtual

La flexibilidad de tamaño del núcleo virtual le ayuda a escalar o reducir verticalmente dentro de un nivel de rendimiento y una región, sin perder los beneficios de la capacidad reservada. La capacidad reservada de SQL Database también le proporciona la flexibilidad para mover temporalmente las bases de datos de acceso frecuente entre grupos y bases de datos únicas como parte de las operaciones normales (dentro de la misma región y el mismo nivel de rendimiento) sin perder las ventajas de la capacidad reservada. Si se mantiene un búfer no aplicado en la reserva, puede administrar de manera eficaz los picos de rendimiento sin sobrepasar el presupuesto.

## <a name="next-steps"></a>Pasos siguientes

El descuento por la reserva de núcleos virtuales se aplica automáticamente al número de instancias de SQL Database que coincidan con el ámbito y los atributos de la capacidad reservada de SQL Database. Puede actualizar el ámbito de la capacidad reservada de SQL Database a través de [Azure Portal](https://portal.azure.com), PowerShell, la CLI o la API.

Para saber cómo administrar la capacidad reservada de SQL Database, consulte el artículo sobre la [administración de capacidad reservada de SQL Database](../billing/billing-manage-reserved-vm-instance.md).

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [¿Qué es Azure Reservations?](../billing/billing-save-compute-costs-reservations.md)
- [Administración de Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Información sobre el descuento de Azure Reservations](../billing/billing-understand-reservation-charges.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](../billing/billing-understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure Reservations en el programa del Proveedor de soluciones en la nube (CSP) del Centro de partners](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
