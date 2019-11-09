---
title: Estados de la suscripción de Azure
description: Describe los diferentes estados de una suscripción de Azure.
keywords: estado de la suscripción de Azure
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 27664fef04e9b52294f0689eba49f16f84433c16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888467"
---
# <a name="azure-subscription-states"></a>Estados de la suscripción de Azure
En este artículo se describen los distintos estados que puede tener una suscripción de Azure. Estos estados aparecerán como "estado" en las hojas de suscripción.

| Estado de la suscripción | DESCRIPCIÓN |
|-------------| ----------------|
| **Activo** | La suscripción de Azure está activa. Puede usar esta suscripción para implementar recursos nuevos y administrar los existentes.|
| **Vencida** | La suscripción de Azure tiene un pago pendiente. La suscripción todavía está activa, pero si se produce un error al pagar los gastos, es posible que se deshabilite la suscripción. [Resuelva el saldo de la suscripción de Azure vencido.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Deshabilitada** | La suscripción de Azure está deshabilitada y ya no se puede usar para crear o administrar recursos de Azure. Mientras se está en este estado, las máquinas virtuales se desasignan, las direcciones IP temporales se liberan, el almacenamiento es de solo lectura y los demás servicios se deshabilitan. La suscripción se puede deshabilitar porque puede haber expirado el crédito, es posible que se haya alcanzado el límite de gasto, que tiene una factura vencida, que se haya superado el límite de la tarjeta de crédito o que se haya deshabilitado o cancelado explícitamente. Según el tipo de suscripción y el motivo de la deshabilitación, una suscripción puede permanecer deshabilitada entre uno y 90 días después de lo cual se eliminará de forma permanente. [Reactivación de una suscripción de Azure deshabilitada.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Eliminado** | La suscripción de Azure se ha eliminado junto con todos los recursos o datos subyacentes. |
