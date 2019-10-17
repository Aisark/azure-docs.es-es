---
title: Qué es una zona privada de Azure DNS
description: Información general de una zona DNS privada
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963736"
---
# <a name="what-is-a-private-azure-dns-zone"></a>¿Qué es una zona de Azure DNS privada?

DNS privado de Azure proporciona un servicio DNS confiable y seguro para administrar y resolver los nombres de dominio en una red virtual sin necesidad de agregar una solución DNS personalizada. Al utilizar zonas DNS privadas, puede usar sus propios nombres de dominio personalizados en lugar de los nombres proporcionados por Azure que están disponibles actualmente. 

Los registros contenidos en una zona DNS privada no se pueden resolver desde Internet. La resolución de DNS en una zona DNS privada solo funciona desde redes virtuales vinculadas a ella.

Puede vincular una zona DNS privada a una o varias redes virtuales mediante la creación de [vínculos de red virtual](./private-dns-virtual-network-links.md).
También puede habilitar la característica de [registro automático](./private-dns-autoregistration.md) para administrar automáticamente el ciclo de vida de los registros DNS de las máquinas virtuales implementadas en una red virtual.

## <a name="limits"></a>límites

Para saber el número de zonas DNS privadas que puede crear en una suscripción y el número de conjuntos de registros que se admiten en una zona DNS privada, consulte [Límites de Azure DNS](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Restricciones

* No se admiten las zonas DNS privadas con etiqueta única. La zona DNS privada debe tener dos o más etiquetas. Por ejemplo, contoso.com tiene dos etiquetas separadas por un punto. Una zona DNS privada puede tener un máximo de 34 etiquetas.
* No se pueden crear delegaciones de zona (registros NS) en una zona DNS privada. Si piensa usar un dominio secundario, puede crearlo directamente como una zona DNS privada y vincularlo a la red virtual sin configurar una delegación de servidor de nombres desde la zona primaria.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo crear una zona privada en Azure DNS mediante [Azure PowerShell](./private-dns-getstarted-powershell.md) o la [CLI de Azure](./private-dns-getstarted-cli.md).

* Obtenga información sobre algunos [escenarios de zona privada](./private-dns-scenarios.md) habituales que pueden realizarse con zonas privadas en Azure DNS.

* Para ver algunas preguntas y respuestas comunes sobre las zonas privadas en Azure DNS, como el comportamiento específico que se puede esperar con determinadas clases de operaciones, consulte [Preguntas frecuentes de DNS privado](./dns-faq-private.md).
