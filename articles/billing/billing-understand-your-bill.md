---
title: Entienda la factura de Azure
description: Aprenda a leer y entender el uso y la factura de la suscripción de Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "68610137"
---
# <a name="understand-your-microsoft-azure-bill"></a>Descripción de la factura de Microsoft Azure
Para entender la factura de Azure, compare la factura con el archivo de uso diario detallado y los informes de administración de costos en Azure Portal.

Este artículo no se aplica a los siguientes clientes:
- Clientes de Azure con un Contrato Enterprise (Clientes de EA). Si es un cliente de Contrato Enterprise, consulte [Descripción de la factura de los clientes de Azure con un Contrato Enterprise](billing-understand-your-bill-ea.md).
- Clientes de Azure con un [contrato de cliente de Microsoft](#check-access-to-a-microsoft-customer-agreement). Si tiene un contrato de cliente de Microsoft, consulte [Descripción de los cargos de Azure en la factura del contrato de cliente de Microsoft](billing-mca-understand-your-bill.md).

Para obtener una explicación sobre cómo funciona la facturación en el programa Proveedor de soluciones en la nube de Azure (CSP de Azure), incluido el uso, los precios y el ciclo de facturación, consulta [Información general acerca de la funcionalidad de facturación de CSP de Azure](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Revisión de los gastos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Si hay algún cargo en su factura sobre el que desea conocer más información, puede comparar el uso y los costos con el archivo de uso o con Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opción 1: Comparar el uso y los costos con el archivo de uso

El archivo CSV de uso detallado proporciona los cargos por período de facturación y el uso diario. Para descargar o ver el archivo, consulte [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md).

Los cargos de uso se muestran en el nivel de medidor. Los siguientes términos significan lo mismo en la factura y en el archivo de uso detallado. Por ejemplo, el ciclo de facturación en la factura es equivalente al período de facturación que se muestra en el archivo de uso detallado.

 | Factura (PDF) | Uso detallado (CSV)|
 | --- | --- |
|Ciclo de facturación | Período de facturación |
 |NOMBRE |Categoría de medidor |
 |type |Subcategoría de medidor |
 |Recurso |Nombre de medidor |
 |Region |Medidor de la región |
 |Consumida |Cantidad consumida |
 |Se incluye |Cantidad incluida |
 |Facturable |Cantidad de superávit |

La sección de **cargos de uso** de la factura muestra el valor total de cada medidor que se consumió durante el período de facturación. Por ejemplo, la imagen siguiente muestra un cargo de uso para el servicio del programador de Azure.

![Cargos de uso de la factura](./media/billing-understand-your-bill/1.png)

Se muestra el mismo cargo en la sección de **resumen de cuenta** del archivo CSV de uso detallado. Tanto la cantidad *consumida* como el *valor* coinciden con los de la factura.

![Cargos de uso del archivo CSV](./media/billing-understand-your-bill/2.png)

Para ver un desglose diario del cargo, vaya a la sección **Uso diario** del archivo .csv. Filtre por *Scheduler* en *Categoría de medición*. Puede ver qué días se usó el medidor y cómo lo usó. La información de *recursos* y de *grupo de recursos* también se muestra para poder realizar una comparación. Los valores *consumidos* deben sumarse y corresponder a los que aparecen en la factura.

![Sección de uso diario en el archivo CSV](./media/billing-understand-your-bill/3.png)

Para obtener el costo por día, multiplique las cantidades *consumidas* por el valor de la *tarifa* en la sección **Resumen de cuenta**.

Para obtener más información, consulte:

- [Descripción de la factura de Azure](billing-understand-your-invoice.md)
- [Descripción del uso detallado de Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opción 2: Comparación del uso y los costos en Azure Portal

Azure Portal también puede ayudarle a comprobar los cargos. Para obtener una información general rápida del uso y los cargos facturados, vea los gráficos de administración de costos.

1. En Azure Portal, vaya a [Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione su suscripción > **Análisis de costos**.
1. Filtre por **Timespan**.
1. Para continuar con el ejemplo anterior, puede ver un cargo por uso para el servicio Azure Scheduler.

   ![Vista de análisis de costos en Azure Portal](./media/billing-understand-your-bill/4.png)

1. Seleccione la fila que muestra la carga para ver el desglose de los costos diarios.

   ![Visualización del historial de costos en Azure Portal](./media/billing-understand-your-bill/5.png)

Para más información, vea [Prevención de costes inesperados con la administración de costes y facturación de Azure](billing-getting-started.md#costs).

## <a name="external"></a>Servicios externos que se facturan por separado

Los servicios externos, o cargos de Azure Marketplace, son para aquellos recursos creados por proveedores de software de otros fabricantes. Estos recursos están disponibles para su uso desde Azure Marketplace. Por ejemplo, un firewall Barracuda es un recurso de Azure Marketplace que ofrecen otros fabricantes. Todos los cargos por el firewall y sus medidores correspondientes aparecerán como cargos por servicios externos.

Los servicios externos se facturan por separado. Los cargos no se muestran en la factura de Azure. Para aprender más, consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Recursos que se facturan mediante medidores de uso

Azure no factura directamente en función del costo del recurso. Los gastos de un recurso se calculan mediante uno o varios medidores. Los medidores se usan para realizar un seguimiento del uso de un recurso durante su vigencia. Estos medidores se usan entonces para calcular la factura.

Por ejemplo, cuando se crea un único recurso de Azure, como una máquina virtual, se crean una o varias instancias de medidores. Los medidores se utilizan para realizar el seguimiento de la utilización del recurso con el tiempo. Cada medidor emite registros de uso que Azure utiliza para calcular la factura.

Por ejemplo, una sola máquina virtual creada en Azure puede tener los siguientes medidores creados para realizar el seguimiento de su uso:

- Horas de proceso
- Horas de dirección IP
- Transferencia de datos de entrada
- Transferencia de datos de salida
- Disco administrado estándar
- Operaciones de disco administrado estándar
- E/S estándar: Disco
- E/S estándar: Lectura de blob en bloques
- E/S estándar: Escritura de blob en bloques
- E/S estándar: Eliminación de blob en bloques

Cuando se crea la máquina virtual, cada medidor comienza a emitir registros de uso. Este uso y el precio del medidor se registra en el sistema de medición de Azure.

## <a name="payment"></a>Pagar la factura

Si ha configurado una tarjeta de crédito como forma de pago, el pago se cargará automáticamente en un plazo de diez días después de finalizar el período de facturación. En el extracto de la tarjeta de crédito, el elemento de línea diría **MSFT Azure**.

Para cambiar la tarjeta de crédito o débito en la que se efectuará el cobro, consulte [Agregar, actualizar o quitar una tarjeta de crédito o débito para Azure](billing-how-to-change-credit-card.md).

Si [paga mediante factura](billing-how-to-pay-by-invoice.md), envíe el pago a la ubicación indicada en la parte inferior de la factura.

[Cree una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para comprobar el estado de su pago.


## <a name="tips-for-cost-management"></a>Sugerencias de administración de costes

- Calcule los costos mediante el uso de la:
  - [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Calculadora del costo total de propiedad](https://aka.ms/azure-tco-calculator)
  - [Información detallada de precios de cada servicio](https://azure.microsoft.com/pricing/)
- [Revise el uso y los costos con regularidad en Azure Portal](billing-getting-started.md#costs).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Comprobación del acceso a un contrato de cliente de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Más información

- [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Descripción de los términos en su factura de Microsoft Azure](billing-understand-your-invoice.md)
- [Descripción de los términos de uso detallados de Microsoft Azure](billing-understand-your-usage.md)
- [Administración de costos en Azure Portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing-getting-started.md#costs)
