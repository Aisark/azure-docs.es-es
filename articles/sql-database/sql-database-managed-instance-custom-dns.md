---
title: DNS personalizado de Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este tema se describen las opciones de configuración para un DNS personalizado con Instancia administrada de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567550"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configuración de un DNS personalizado para Instancia administrada de Azure SQL Database

Se debe implementar una instancia administrada de Azure SQL Database dentro de una [red virtual](../virtual-network/virtual-networks-overview.md) de Azure. Hay algunos escenarios (por ejemplo, correo electrónico de base de datos o servidores vinculados a otras instancias de SQL en el entorno híbrido o en la nube) que requieren que los nombres de host privados se resuelvan en Instancia administrada. En este caso, debe configurar un DNS personalizado dentro de Azure. 

Puesto que Instancia administrada utiliza el mismo DNS para su funcionamiento interno, la configuración del servidor DNS personalizado debe poder resolver los nombres de dominio público.

   > [!IMPORTANT]
   > Utilice siempre nombres de dominio completos (FQDN) para los servidores de correo electrónico, servidores SQL Server y otros servicios, incluso si están dentro de la zona DNS privada. Por ejemplo, use `smtp.contoso.com` para el servidor de correo electrónico porque simplemente `smtp` no se resolverá correctamente.

   > [!IMPORTANT]
   > La actualización de los servidores DNS de una red virtual no afectarán de inmediato a Instancia administrada. La configuración de DNS de Instancia administrada se actualizará una vez que la concesión de DHCP expire o una vez que la plataforma se actualice, lo que sea que ocurra primero. **Se recomienda que los usuarios establezcan la configuración de DNS de una red virtual antes de crear su primera Instancia administrada.**

## <a name="next-steps"></a>Pasos siguientes

- Para información general, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md)
- Para ver un tutorial que muestra cómo crear una instancia administrada, consulte el artículo de [creación de instancias administradas](sql-database-managed-instance-get-started.md).
- Para información sobre cómo configurar una red virtual para una instancia administrada, consulte el artículo sobre la [configuración de la red virtual para las instancias administradas](sql-database-managed-instance-connectivity-architecture.md).
