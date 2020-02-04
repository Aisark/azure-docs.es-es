---
title: Aplicación del descuento por reserva a Azure Storage | Microsoft Docs
description: Obtenga información sobre cómo se aplica el descuento por la capacidad reservada de Azure Storage para los recursos de Azure Data Lake Storage Gen2 y blobs en bloques.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 40fba61b173979fb9362de73a87d6192aac9941d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2020
ms.locfileid: "75986680"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Aplicación del descuento por reserva a Azure Storage

Después de adquirir la capacidad reservada de Azure Storage, el descuento por reserva se aplica automáticamente a los recursos de Azure Data Lake Storage Gen2 y blob en bloques que coincidan con los términos de la reserva. El descuento por reserva se aplica solo a la capacidad de almacenamiento. El ancho de banda y la tasa de solicitudes se cobran según las tarifas de pago por uso.

Para más información acerca de la capacidad reservada de Azure Storage, consulte [Optimización de los costos del almacenamiento de blobs con capacidad reservada](../../storage/blobs/storage-blob-reserved-capacity.md).

Para información sobre los precios de reserva de Azure Storage, consulte [Precios de los blobs en bloques](https://azure.microsoft.com/pricing/details/storage/blobs/) y [Precios de Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Aplicación del descuento por reserva

El descuento por la capacidad reservada de Azure Storage se aplica a los recursos de blob en bloques y Azure Data Lake Storage Gen2 según la hora.

El descuento por la capacidad reservada de Azure Storage es un descuento para "usarlo o perderlo". Si no tiene ningún recurso de blob en bloques ni de Azure Data Lake Storage Gen2 que cumpla los términos de la reserva para una hora determinada, perderá una cantidad de reserva durante esa hora. No se pueden arrastrar las horas reservadas no utilizadas.

Al eliminar un recurso, el descuento por reserva se aplica automáticamente a otro que coincida con el ámbito especificado. Si no se encuentran recursos coincidentes en el ámbito especificado, las horas reservadas se pierden.

## <a name="discount-examples"></a>Ejemplos de descuento

En los ejemplos siguientes se muestra cómo se aplica el descuento por la capacidad reservada de Azure Storage en función de las implementaciones.

Supongamos que ha adquirido 100 TB de capacidad reservada en la región Oeste de EE. UU. 2 durante un período de un año. La reserva es para el almacenamiento con redundancia local (LRS) en el nivel de acceso frecuente.

Supongamos que el costo de esta reserva de ejemplo es de 18 540 $. Puede optar por pagar la cantidad completa por adelantado o pagar las instalaciones mensuales fijas de 1.545 $ al mes durante los próximos 12 meses.

En estos ejemplos, se supone que se ha registrado para un plan de pago de reserva mensual. En los siguientes escenarios se describe lo que sucede si el uso excede o no llega a la capacidad reservada.

### <a name="underusing-your-capacity"></a>Uso por debajo de la capacidad

Supongamos que en una hora determinada dentro del período de reserva, solo usó 80 TB de la capacidad reservada de 100 TB. Los 20 TB restantes no se aplican durante esa hora y no se usan.

### <a name="overusing-your-capacity"></a>Uso por encima de la capacidad

Supongamos que en una hora determinada dentro del período de reserva, usó 101 TB de la capacidad de almacenamiento. El descuento por reserva se aplica a los 100 TB de datos y el TB restante se cobra según las tarifas de pago por uso durante esa hora. Si, en la siguiente hora, el uso cambia a 100 TB, todo el uso está incluido en la reserva.

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Ponerse en contacto con nosotros

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- [Optimizar los costos de almacenamiento de blobs con capacidad reservada](../../storage/blobs/storage-blob-reserved-capacity.md)
- [¿Qué es Azure Reservations?](save-compute-costs-reservations.md)
