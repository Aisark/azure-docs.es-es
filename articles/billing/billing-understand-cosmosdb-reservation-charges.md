---
title: Aplicación del descuento por reserva a Azure Cosmos DB | Microsoft Docs
description: Conozca cómo se aplica el descuento por reserva al rendimiento aprovisionado (RU/s) en Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d5a13e4466234d73bafe8dbe76cae92955cf64bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370754"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Aplicación del descuento por reserva a Azure Cosmos DB

Después de comprar capacidad reservada en Azure Cosmos DB, el descuento por la reserva se aplica automáticamente a los recursos que coincidan con los atributos y la cantidad de la reserva. Una reserva abarca el rendimiento aprovisionado para los recursos de Azure Cosmos DB. No abarca el software, redes, almacenamiento ni los cargos predefinidos de contenedor.

## <a name="how-reservation-discount-is-applied"></a>Cómo se aplica el descuento de reserva

Un descuento de reserva es "*-it-o-perder-usarla*". Por lo tanto, si no tiene recursos coincidentes para cualquier hora, perder una cantidad de reserva para esa hora. No se puede llevar a cabo reenviar horas reservadas no utilizadas.

Cuando se apaga un recurso, el descuento de reserva se aplica automáticamente a otro recurso coincidente en el ámbito especificado. Si se encuentra ningún recurso coincidente en el ámbito especificado, que son las horas reservadas *pierde*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>El descuento por reserva se aplica a las cuentas de Azure Cosmos DB

Se aplica un descuento por reserva al [rendimiento aprovisionado](../cosmos-db/request-units.md) en términos de unidades de solicitud por segundo (RU/s) por cada hora. Para aquellos recursos de Azure Cosmos DB que no se ejecutan durante una hora completa, el descuento por reserva se aplica automáticamente a otros recursos de Cosmos DB que coinciden con los atributos de la reserva. El descuento se puede aplicar a recursos de Azure Cosmos DB que se están ejecutando simultáneamente. Si no tiene recursos de Cosmos DB que se ejecuten durante toda una hora y que coincidan con los atributos de reserva, no obtendrá todas las ventajas del descuento por reserva para esa hora.

Los descuentos están organizados en niveles. Las reservas con unidades de solicitud mayores proporcionan mayores descuentos.

La compra de reserva aplicará descuentos a todas las regiones en la proporción equivalente a los precios regionales por demanda. Para más información sobre la proporción de descuento por reserva en cada región, consulte la sección [descuentos por reserva para cada región](#reservation-discount-per-region) que aparece en este artículo.

## <a name="reservation-discount-per-region"></a>Descuentos por reserva para cada región
El descuento por reserva se aplica a los costos de rendimiento de Azure Cosmos DB por cada hora. Se aplica en el ámbito de una suscripción única o de una inscripción o cuenta. El descuento por reserva se aplica según la métrica de uso de las diferentes regiones en la siguiente proporción:

|Descripción de la métrica  |Region |Relación  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/Hora - Sudeste de Asia Pacífico  |  Sudeste de Asia Pacífico    |   1      |
|Azure Cosmos DB - 100 RU/s/Hora - Este de Asia Pacífico |   Este de Asia Pacífico   |    1     |
|Azure Cosmos DB - 100 RU/s/Hora - Norte de Europa|  Norte de Europa       |    1     |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Corea del Sur|    Sur de Corea del Sur     |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de Europa|    Oeste de Europa     |      1   |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Corea del Sur|   Centro de Corea del Sur    |       1  |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Reino Unido|   Sur de Reino Unido 2      |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de Reino Unido|   Oeste de Reino Unido      |    1     |
|Azure Cosmos DB - 100 RU/s/Hora - Norte de Reino Unido |   Norte del Reino Unido    |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Reino Unido 2|   Sur del Reino Unido 2      |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Este de EE. UU. 2|  Este de EE. UU. - 2     |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Centro y norte de EE. UU.|   Centro y norte de EE. UU.      |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de EE. UU.|   Oeste de EE. UU.      |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de EE. UU.| Centro de EE. UU.        |     1    |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de EE. UU. 2|   Oeste de EE. UU. 2      |      1   |
|Azure Cosmos DB - 100 RU/s/Hora - Centro-oeste de EE. UU.|   Centro-oeste de EE. UU.      |       1  |
|Azure Cosmos DB - 100 RU/s/Hora - Este de EE. UU.|   Este de EE. UU.      |  1       |
|Azure Cosmos DB - 100 RU/s/Hora - Norte de Sudáfrica|     Norte de Sudáfrica    |   1      |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de Sudáfrica |    Oeste de Sudáfrica      |    1     |
|Azure Cosmos DB - 100 RU/s/Hora - India meridional|    India meridional     |    1,0375    |
|Azure Cosmos DB - 100 RU/s/Hora - Este de Canadá|   Este de Canadá      |    1.1      |
|Azure Cosmos DB - 100 RU/s/Hora - Este de Japón|   Este de Japón      |    1,125     |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de Japón|     Oeste de Japón    |   1,125       |
|Azure Cosmos DB - 100 RU/s/Hora - India occidental|     India occidental    |    1,1375     |
|Azure Cosmos DB - 100 RU/s/Hora - India central|    Centro de la India     |  1,1375       |
|Azure Cosmos DB - 100 RU/s/Hora - Este de Australia|     Este de Australia    |   1,15       |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Canadá|  Centro de Canadá       |   1.2       |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Francia|   Centro de Francia      |    1,25      |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Brasil|  Sur de Brasil       |   1.5      |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Australia|   Centro de Australia      |   1.5      |
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Australia 2| Centro de Australia 2        |    1.5     |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Francia|    Sur de Francia     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Escenarios de aplicación de descuento por reserva

Tenga en cuenta los siguientes requisitos para una reserva:

* Capacidad de proceso necesaria: 50 000 RU/s  
* Regiones que usa: 2

En este caso, sus gastos totales por demanda serían de 500 unidades de la métrica de 100 RU/s en estas dos regiones. El consumo total es de 100 000 RU/s por hora.

**Escenario 1**

Por ejemplo, suponga que necesita implementaciones de Azure Cosmos DB en las regiones "Centro y norte de EE. UU." y "Oeste de EE. UU.". Cada región tiene un consumo de rendimiento de 50 000 RU/s. Una compra de reserva de 100 000 RU/s. equilibrará por completo los gastos por demanda.

El descuento que cubre una reserva se calcula como: consumo de rendimiento x proporción_descuento_por_reservas_para_región. Para las regiones "Centro y norte de EE. UU." y "Oeste de EE. UU.", la proporción de descuento por reserva es 1. Por lo tanto, se descuentan en total 100 000 RU/s. Este valor se calcula de la siguiente forma: 50 000 x 1 + 50 000 x 1 = 100 000 RU/s. No deberá pagar cargos adicionales en las tarifas de pago por uso normales.

|Descripción de la métrica | Region |Consumo de rendimiento (RU/s) |Descuento por reserva aplicado a las RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/Hora - Centro y norte de EE. UU.  |   Centro y norte de EE. UU.  | 50.000  | 50.000  |
|Azure Cosmos DB - 100 RU/s/Hora - Oeste de EE. UU.  |  Oeste de EE. UU.   |  50.000  |  50.000 |

**Escenario 2**

Por ejemplo, suponga que necesita implementaciones de Azure Cosmos DB en las regiones "Centro de Australia 2" y "Sur de Francia". Cada región tiene un consumo de rendimiento de 50 000 RU/s. Se podría aplicar una compra de reserva de 100 000 RU/s de la siguiente manera (supongamos que el uso en la región Centro de Australia 2 se descontó en primer lugar):

|Descripción de la métrica | Region |Consumo de rendimiento (RU/s) |Descuento por reserva aplicado a las RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/Hora - Centro de Australia 2  |  Centro de Australia 2   |  50.000  |  50.000   |
|Azure Cosmos DB - 100 RU/s/Hora - Sur de Francia  |  Sur de Francia   |  50.000 |  15 384  |

Usar 50 000 unidades en la región "Centro de Australia 2" se corresponde a 75 000 RU/s de uso facturables (o uso normalizado). Dicho valor se calcula de la siguiente forma: consumo de rendimiento x proporción_descuento_por_reservas_para_región. El cálculo da como resultado 75 000 RU/s de uso facturable o normalizado. Este valor se calcula de la siguiente forma: 50 000 x 1,5 = 75 000 RU/s.

Las 100 000 RU/s de la compra de reserva cubrirían las 75 000 RU/s en "Centro de Australia 2". Así, quedarían 25 000 RU/s para la región "Sur de Francia". De las restantes 25 000 RU/s, se aplica un descuento por reserva de 15 384 RU/s a la región "Sur de Francia". Este valor se calcula de la siguiente forma: 25 000/1,625 = 15 384 RU/s. Las restantes 34 616 RU/s de la región "Sur de Francia" se cobran según las tarifas de pago por uso normales.

El sistema de facturación de Azure asignará el beneficio de la facturación por reserva a la primera instancia que se procese y coincida con la configuración de reserva. Por ejemplo, "Centro de Australia 2", en este caso.

Para información sobre la aplicación de las reservas en informes de uso de facturación de Azure y ver cómo hacerla, consulte el artículo de [información sobre el uso de reservas de Azure](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las reservas de Azure, consulte los siguientes artículos:

* [Qué son las reservas de Azure](../billing/billing-save-compute-costs-reservations.md)  
* [Pago por adelantado para recursos de Azure Cosmos DB con capacidad reservada de Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Pago por adelantado por recursos de proceso de SQL Database con capacidad reservada de Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)  
* [Administrar reservas para Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Información sobre el uso de reservas para suscripciones de pago por uso](../billing/billing-understand-reserved-instance-usage.md)  
* [Información sobre el uso de reservas para la inscripción Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Información sobre el uso de reservas para suscripciones de CSP](https://docs.microsoft.com/partner-center/azure-reservations)
