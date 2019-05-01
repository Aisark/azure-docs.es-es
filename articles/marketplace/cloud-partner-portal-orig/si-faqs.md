---
title: Preguntas más frecuentes sobre Seller Insights
description: Preguntas más frecuentes sobre la característica Seller Insights de Cloud Partner Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943074"
---
<a name="seller-insights-faq"></a>Preguntas más frecuentes sobre Seller Insights
===================

Este artículo proporciona una guía sobre los procedimientos de usuario más habituales y preguntas sobre Seller Insights.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Búsqueda de definiciones para los valores del archivo de transacción descargado
------------------------------------------------------------------

Las definiciones de los valores de métrica del archivo de transacciones se encuentran en el artículo [Seller Insights Definitions](./si-insights-definitions-v4.md) (Definiciones de Seller Insights).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consulta de los detalles de cliente de las transacciones por las que me han pagado
-------------------------------------------------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **Payout Status** (Estado del pago) y aplique el filtro para que aparezcan solo aquellas con el valor "Paid Out" (Pagado). Aparecerán las columnas siguientes que contiene los detalles del cliente: **Nombre de la empresa**, **correo electrónico de cliente**, **país cliente**, **estado de cliente**, y **código Postal del cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Cálculo de las cuentas por cobrar abiertas
-------------------------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **Payout Status** (Estado del pago) y aplique el filtro para que aparezcan solo aquellas con los valores "Upcoming Payout" (Próximo pago) y "Not Ready for Payout" (No listas para el pago). A continuación, sume los valores de la columna con la etiqueta **Payout Amount (PC)** (Importe del pago).


<a name="calculate-revenue-by-customer-usage-period"></a>Cálculo de los ingresos por período de uso del cliente
------------------------------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **estado de la transacción**y filtrar el valor de "Pagado".   Para cada transacción en la lista, la columna con la etiqueta **importe de pago (PC)** representa la cantidad que se ha pagado.  Para calcular el período de uso asociado con la transacción, use la columna **fecha del cargo**, que es una buena aproximación del último día de uso para el período al que se aplica la transacción.


<a name="calculate-your-bad-debt"></a>Cálculo de la deuda incobrable
---------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **Final Collection Status** (Estado del cobro final) y aplique el filtro para que aparezcan solo aquellas con el valor "Write Off" (Cancelado). A continuación, sume los valores de la columna con la etiqueta **Payout Amount (PC)** (Importe del pago).


<a name="view-payout-or-customer-contact-information"></a>Ver pago o información de contacto del cliente
-------------------------------------------

Inicie sesión como usuario con el rol "Propietario" y no con el rol "Colaborador". Solo el rol de propietario puede ver los pagos y la información del cliente Puede encontrar más información acerca de los roles de usuario en el artículo [Administración de usuarios](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Cálculo de mi pagos por adelantado
----------------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **Transaction Type** (Tipo de transacción) y aplique el filtro para que aparezcan solo aquellas con el valor "Gasto" (Cargo). A continuación, busque la columna con la etiqueta **Final Collection Status** (Estado del cobro final) y aplique el filtro para que aparezcan solo aquellas transacciones con el estado "In Progress" (En curso). Por último, sume los valores de la columna **Payout Amount (PC)** (Importe del pago) para calcular todos los pagos por adelantado que se le han realizado antes del cobro al cliente.


<a name="calculate-customer-refunds"></a>Cálculo de los reembolsos a clientes
--------------------------

Después de descargar las transacciones desde el módulo de pago, busque la columna con la etiqueta **Final Collection Status** (Estado del cobro final) y aplique el filtro para que aparezcan solo aquellas con el valor "Refund" (Reembolso). Sume los valores de la columna **Charge Amount (PC)** (Importe del cobro) para calcular todos los reembolsos procesados para sus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifique qué transacciones implican el uso de Microsoft Channel Partner
----------------------------------------------------------------

Todas las transacciones de la columna **Azure License Type** (Tipo de licencia de Azure) que aparecen después de filtrar por los valores "Enterprise through Reseller" (Empresa mediante revendedor) y "Cloud Solution Provider" (Proveedor de soluciones en la nube) implican el uso de Microsoft Channel Partner. Para más información sobre el asociado, puede encontrar el **nombre del revendedor** y el **correo electrónico del revendedor** en el módulo de pago descargado y en el de cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificación del uso de versiones de evaluación y conversión de estas en ventas
------------------------------------------

Las descargas de los módulos de pedidos, uso y pago contienen ahora el valor **Trial End Date** (Fecha de fin de la versión de evaluación) para ayudarle a averiguar cuándo terminó el período de evaluación para ese pedido en concreto en los casos en los que sea aplicable. Para ver los pedidos y el uso de las versiones de evaluación, busque la columna **SKU Billing Type** (Tipo de facturación de SKU) en los módulos descargados y aplique el filtro para que solo aparezcan las transacciones con el valor "Trial" (Evaluación). Para ver las conversiones de las versiones de evaluación, busque la columna **Trial End Date** (Fecha de fin de la versión de evaluación) en los módulos descargados y aplique el filtro para que solo aparezcan los pedidos en los que el valor **Trial End Date** (Fecha de fin de la versión de evaluación) es posterior a la fecha actual y en los que la columna **Cancel Date** (Fecha de cancelación) está vacía o es posterior a la fecha de **Trial End Date** (Fecha de fin de la versión de evaluación).


<a name="when-is-my-monthly-payout-calculated"></a>Cuándo se calcula mi pago mensual
------------------------------------

Los pagos se emiten aproximadamente el 15 de cada mes por todos los importes listos para el pago el último día natural del mes anterior. El tercer día del mes, Microsoft calculará el importe del pago del mes anterior y actualizará todas las transacciones de cobro aplicables del módulo descargado con el estado "Upcoming Payout" (Próximo pago) en la columna **Payout Status** (Estado del pago). Esas transacciones permanecerán en ese estado hasta que se envíe la solicitud de pago a su cuenta bancaria, momento en el cual el valor de **Payout Status** (Estado del pago) se actualizará a "Paid Out" (Pagado) y el valor de "Payout Date" (Fecha de pago) se actualizará para que aparezca la fecha en la que se envió la solicitud de pago a su banco.


<a name="calculate-customer-acquisition-and-loss"></a>Cálculo de adquisición y pérdida de clientes
---------------------------------------

Puede ver la fecha en la que el cliente compró por primera vez una de sus ofertas buscando en la columna **Date Acquired** (Fecha de adquisición) del módulo de cliente descargado. De forma parecida, puede ver la fecha después de la cual ya no tenían ninguna oferta publicada por el usuario buscando la columna **Date Lost** (Fecha de pérdida) en el módulo de cliente descargado.


<a name="finding-more-help"></a>Obtener más ayuda
-----------------

- [Seller Insights Definitions](./si-insights-definitions-v4.md) (Definiciones de Seller Insights): busque definiciones de métricas y datos

- [Introducción a Seller Insights](./si-getting-started.md): introducción a la característica Seller Insights.

