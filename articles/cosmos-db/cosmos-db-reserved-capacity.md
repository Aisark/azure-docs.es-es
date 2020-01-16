---
title: Capacidad reservada en Azure Cosmos DB para optimizar el costo
description: Aprenda a comprar capacidad reservada de Azure Cosmos DB para ahorrar en los costos de proceso.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: 83ecb0ad85a02c8c05b9d7e504e578afd98ed7b6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969352"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimización del costo con capacidad reservada en Azure Cosmos DB

La capacidad reservada de Azure Cosmos DB le ayuda a ahorrar dinero al comprometerse a una reserva de los recursos de Azure Cosmos DB por uno o tres años. Con la capacidad reservada de Azure Cosmos DB, puede obtener un descuento en el rendimiento aprovisionado para recursos de Cosmos DB. Algunos ejemplos de recursos son las bases de datos y los contenedores (tablas, colecciones y gráficos).

La capacidad reservada de Azure Cosmos DB puede reducir de forma considerable los costos de Cosmos DB: hasta un 65 % sobre los precios normales, con el acuerdo anticipado de uno o tres años.&mdash; La capacidad reservada ofrece un descuento en la facturación y no afecta el estado de tiempo de ejecución de sus recursos de Azure Cosmos DB.

La capacidad reservada de Azure Cosmos DB abarca el rendimiento aprovisionado de los recursos. No cubre los cargos de almacenamiento y redes. Tan pronto como se compra una reserva, los costos de proceso que coincidan con los atributos de la reserva dejan de pagarse según las tarifas de pago por uso. Para más información sobre las reservas, consulte el artículo [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md).

Puede comprar capacidad reservada de Azure Cosmos DB en [Azure Portal](https://portal.azure.com). Pague la reserva [por adelantado o mensualmente](../cost-management-billing/reservations/monthly-payments-reservations.md). Para adquirir capacidad reservada:

* Debe tener el rol de propietario al menos en una suscripción Enterprise o individual con tarifas de pago por uso.  
* En el caso de las suscripciones Enterprise, la opción **Agregar instancias reservadas** debe estar habilitada en el [portal de EA](https://ea.azure.com). O bien, si esa opción está deshabilitada, debe ser un administrador de EA en la suscripción.
* En el caso del programa del Proveedor de soluciones en la nube (CSP), solo los agentes de administración o de ventas pueden comprar capacidad reservada de Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinación del rendimiento necesario antes de la compra

El tamaño de la reserva debe basarse en la cantidad total de rendimiento que usarán los recursos de Azure Cosmos DB existentes o que se van a implementar. Puede determinar el rendimiento necesario de las maneras siguientes:

* Obtenga los datos históricos del rendimiento total aprovisionado a través de sus cuentas, bases de datos y colecciones de Azure Cosmos DB en todas las regiones. Por ejemplo, puede evaluar el rendimiento aprovisionado medio diario si descarga el extracto de uso diario desde `https://account.azure.com`.

* Si es un cliente de Contrato Enterprise (EA), puede descargar el archivo de uso para obtener los detalles de rendimiento de Azure Cosmos DB. Consulte el valor **Tipo de servicio** de la sección **Información adicional** en el archivo de uso.

* Puede sumar el rendimiento medio de todas las cargas de trabajo de las cuentas de Azure Cosmos DB que espera ejecutar durante el próximo período de uno o tres años. A continuación, puede usar esa cantidad para la reserva.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Compra de capacidad reservada de Azure Cosmos DB

1. Inicie sesión en [Azure Portal](https://portal.azure.com).  

2. Seleccione **Todos los servicios** > **Reservations** > **Agregar**.  

3. En el panel **Comprar reservas**, elija **Azure Cosmos DB** para comprar una nueva reserva.  

4. Rellene los campos obligatorios tal como se describe en la tabla siguiente:

   ![Cumplimentación del formulario de capacidad reservada](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Campo  |Descripción  |
   |---------|---------|
   |Ámbito   |   Opción que controla el número de suscripciones que pueden usar la ventaja de facturación asociada con la reserva. También controla cómo se aplica la reserva a suscripciones concretas. <br/><br/>  Si selecciona **Compartido**, el descuento de la reserva se aplica a las instancias de Azure Cosmos DB que se ejecutan en cualquier suscripción en el contexto de facturación. El contexto de facturación se basa en cómo se haya suscrito a Azure. Para los clientes Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones que esta contiene. Para los clientes de pago por uso, el ámbito compartido incluye todas las suscripciones con tarifas de pago por uso creadas por el administrador de la cuenta.  <br/><br/>  Si selecciona **Suscripción única**, el descuento de reserva se aplica a las instancias de Azure Cosmos DB de la suscripción seleccionada. <br/><br/> Si selecciona **Grupo de recursos único**, el descuento de reserva se aplica a las instancias de Azure Cosmos DB de la suscripción seleccionada y al grupo de recursos seleccionado dentro de esa suscripción. <br/><br/> Puede cambiar el ámbito de reserva después de comprar la capacidad reservada.  |
   |Subscription  |   Suscripción usada para pagar la capacidad reservada de Azure Cosmos DB. El método de pago en la suscripción seleccionada se usa al cargar los costos. La suscripción debe ser uno de los tipos siguientes: <br/><br/>  Contrato Enterprise (números de oferta: MS-AZR-0017P o MS-AZR-0148P): Para una suscripción Enterprise, los cargos se deducen del saldo de compromiso monetario de la inscripción o se cobran como uso por encima del límite. <br/><br/> Suscripción individual con tarifas de pago por uso (números de la oferta: MS-AZR-0003P o MS-AZR-0023P): en una suscripción individual con tarifas de pago por uso, los cargos se cobran en el método de pago de tarjeta de crédito o factura de la suscripción.    |
   | Grupo de recursos | Grupo de recursos al que se aplica el descuento de capacidad reservada. |
   |Término  |   Un año o tres años.   |
   |Tipo de rendimiento   |  El rendimiento se aprovisiona como unidades de solicitud. Puede comprar una reserva para el rendimiento aprovisionado con ambas configuraciones: escrituras de una sola región y escrituras de varias regiones. El tipo de rendimiento tiene dos valores para elegir: 100 RU/s por hora y 100 RU/s de arquitectura multimaestro por hora.|
   | Unidades de capacidad reservada| La cantidad de rendimiento que quiere reservar. Puede calcular este valor si determina el rendimiento necesario para todos los recursos de Cosmos DB (por ejemplo, las bases de datos o contenedores) por región. A continuación, multiplique esa cifra por el número de regiones que asociará a la base de datos de Cosmos. Por ejemplo: Si tiene cinco regiones con un millón de RU/s en todas las regiones, seleccione cinco millones de RU/s para la compra de capacidad de reserva. |


5. Después de rellenar el formulario, se calcula el precio necesario para comprar la capacidad reservada. La salida también muestra el porcentaje de descuento que se obtiene con las opciones seleccionadas. A continuación, haga clic en **Seleccionar**.

6. En el panel **Comprar reservas**, revise el descuento y el precio de la reserva. Este precio de reserva se aplica a los recursos de Azure Cosmos DB con rendimiento aprovisionado en todas las regiones.  

   ![Resumen de capacidad reservada](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Seleccione **Review + buy** (Revisar y comprar) y, luego, **Comprar ahora**. Cuando se realiza correctamente la compra, se muestra la siguiente página:

Después de comprar una reserva, se aplica inmediatamente a cualquier recurso de Azure Cosmos DB existente que coincida con los términos de la reserva. Si no tiene recursos de Azure Cosmos DB ya existentes, la reserva se aplica al implementar una nueva instancia de Cosmos DB que coincida con los términos de la reserva. En ambos casos, el período de la reserva empieza inmediatamente después de que una compra se ha realizado correctamente.

Cuando expira la reserva, las instancias de Azure Cosmos DB se siguen ejecutando y se facturan según las tarifas habituales de pago por uso.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelación, intercambio o reembolso de reservas

Para ayudar a identificar la capacidad reservada adecuada, consulte [Aplicación del descuento por reserva a Azure Cosmos DB](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

Puede cancelar, intercambiar o reembolsar reservas con ciertas limitaciones. Para más información, consulte [Autoservicio de intercambios y reembolsos de reservas de Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Pasos siguientes

El descuento de la reserva se aplica automáticamente a los recursos de Microsoft Azure Cosmos DB que coincidan con el ámbito y los atributos de la reserva. Puede actualizar el ámbito de la reserva mediante Azure Portal, PowerShell, la CLI de Azure o la API.

*  Para saber cómo se aplican los descuentos de capacidad reservada a Azure Cosmos DB, consulte [Aplicación del descuento por reserva a Azure Cosmos DB](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Para más información acerca de las reservas de Azure, consulte los siguientes artículos:

   * [¿Qué son las reservas de Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Administración de reservas de Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Información sobre el uso de reservas para la inscripción Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Información sobre el uso de reservas para suscripciones de pago por uso](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Reservas de Azure en el programa CSP del Centro de partners](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
