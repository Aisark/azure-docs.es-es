---
title: Aplicación de un descuento por reserva a Azure Database for MySQL
description: Aplicación de un descuento por reserva a Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 905a0c77919e448a7c3387ca8fa35a8150780534
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608159"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Aplicación de un descuento por reserva a Azure Database for MySQL

Después de comprar una capacidad reservada en Azure Database for MySQL, el descuento por la reserva se aplica automáticamente a los servidores MySQL que coincidan con los atributos y la cantidad de la reserva. Una reserva solo cubre los costos de proceso del Azure Database for MySQL. Por el almacenamiento y la administración de redes se le cobra según las tarifas normales. 

## <a name="how-reservation-discount-is-applied"></a>Aplicación del descuento por reserva

Un descuento por reserva es para ***usarlo o perderlo***. Por lo tanto, si no tiene recursos coincidentes para ninguna hora, perderá una cantidad de reserva para esa hora. No se pueden arrastrar las horas reservadas no utilizadas.</br>

Al cerrar un recurso, el descuento por reserva se aplica automáticamente a otro recurso que coincida con el ámbito especificado. Si no se encuentran recursos coincidentes en el ámbito especificado, las horas reservadas se pierden.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Descuento aplicado a Azure Database for MySQL

El descuento por la capacidad reservada de Azure Database for MySQL se aplica a la ejecución de los servidores MySQL cada hora. La reserva que compra coincide con el uso de procesos que emiten los servidores Azure Database for MySQL en ejecución. Para los servidores MySQL que no se ejecutan durante una hora entera, la reserva se aplica automáticamente a otras instancias de Azure Database for MySQL que coincidan con los atributos de reserva. El descuento se puede aplicar a servidores Azure Database for MySQL que se están ejecutando simultáneamente. Si no tiene un servidor MySQL que se ejecute durante toda la hora que coincida con los atributos de reserva, no obtendrá todas las ventajas del descuento por la reserva para esa hora.

En los ejemplos siguientes se muestra cómo se aplica el descuento por la capacidad reservada de Azure Database for MySQL en función del número de núcleos adquiridos y el momento de su ejecución.

* **Ejemplo 1**: compra una capacidad reservada de Azure Database for MySQL para ocho núcleos virtuales. Si va a ejecutar un servidor Azure Database for MySQL de 16 núcleos virtuales que coincida con el resto de los atributos de la reserva, se le cobrará el precio de pago por uso de 8 núcleos virtuales correspondiente al uso de procesos del servidor MySQL y obtendrá el descuento por reserva durante una hora del uso de proceso del servidor MySQL correspondiente a 8 núcleos virtuales.</br>

En el resto de estos ejemplos, supongamos que la capacidad reservada de Azure Database for MySQL que compra es para una instancia de Azure Database for MySQL de 16 núcleos virtuales y el resto de atributos de la reserva coinciden con los servidores MySQL en ejecución.

* **Ejemplo 2**: se ejecutan dos servidores Azure Database for MySQL con ocho núcleos virtuales cada hora. Se aplica el descuento por reserva para 16 núcleos virtuales al uso de proceso para ambos servidores Azure Database for MySQL de 8 núcleos.

* **Ejemplo 3**: se ejecuta un servidor Azure Database for MySQL de 16 núcleos virtuales desde la 1 p.m. a la 1:30 p.m. Se ejecuta un servidor Azure Database for MySQL de 16 núcleos virtuales desde la 1:30 p.m. a las 2 p.m. Ambas están cubiertas por el descuento en la reserva.

* **Ejemplo 4**: se ejecuta un servidor Azure Database for MySQL de 16 núcleos virtuales desde la 1 p.m. a la 1:45 p.m. Se ejecuta un servidor Azure Database for MySQL de 16 núcleos virtuales desde la 1:30 p.m. a las 2 p.m. Se le cobra el precio de pago por uso por el solapamiento de 15 minutos. El descuento en la reserva se aplica al uso de proceso por el resto del tiempo.

Para obtener información sobre la aplicación de Azure Reservations en informes de uso de facturación, consulte el artículo [Información sobre el uso de Azure Reservations](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros
Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).
