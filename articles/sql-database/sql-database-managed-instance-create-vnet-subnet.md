---
title: Creación de una red virtual para Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este artículo se describe cómo crear una red virtual en la que puede implementar una Instancia administrada de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 2fd4055f1a2ec7b5b29d1a303f1c9ac3260f4cca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958463"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Creación de una red virtual para Instancia administrada de Azure SQL Database

En este artículo se explica cómo crear una red virtual y una subred válidas en las que puede implementar una Instancia administrada de Azure SQL Database.

La Instancia administrada de Azure SQL Database debe implementarse dentro de una [red virtual](../virtual-network/virtual-networks-overview.md) de Azure. Esta implementación permite los siguientes escenarios:

- Protección de una dirección IP privada
- Conexión a Instancia administrada directamente desde una red local
- Conexión de Instancia administrada con un servidor vinculado o con otro almacén de datos local
- Conexión de Instancia administrada a los recursos de Azure  

> [!Note]
> Debería [determinar el tamaño de la subred para la Instancia administrada](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implementar la primera instancia. No se puede cambiar el tamaño de la subred después de colocar los recursos en su interior.
>
> Si piensa usar una red virtual existente, tiene que modificar la configuración de red para dar cabida a Instancia administrada. Para más información, consulte [Modificación de una red virtual existente para Instancia administrada](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Después de crear una instancia administrada, no se admite el traslado de la instancia administrada o la red virtual a otro grupo de recursos o suscripción.


## <a name="create-a-virtual-network"></a>Creación de una red virtual

La manera más fácil de crear y configurar una red virtual es usar una plantilla de implementación de Azure Resource Manager.

1. Inicie sesión en el Portal de Azure.

2. Seleccione el botón **Implementar en Azure**:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botón abre un formulario que puede usar para configurar el entorno de red, donde puede implementar Instancia administrada.

   > [!Note]
   > Esta plantilla de Azure Resource Manager implementará una red virtual con dos subredes. Una subred, llamada **ManagedInstances**, está reservada para Instancia administrada y tiene una tabla de rutas configuradas previamente. La otra subred, denominada **Default**, se usa para otros recursos que deben acceder a Instancia administrada (por ejemplo, Azure Virtual Machines).

3. Configure el entorno de red. En el formulario siguiente, puede configurar parámetros de su entorno de red:

   ![Plantilla de Resource Manager para configurar la red de Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Puede cambiar los nombres de la red virtual y las subredes, y ajustar los intervalos IP asociados a sus recursos de red. Una vez que presione el botón **Comprar**, se creará el formulario y configurará el entorno. Si no necesita dos subredes, puede eliminar la predeterminada.

## <a name="next-steps"></a>Pasos siguientes

- Para información general, consulte [¿Qué es Instancia administrada?](sql-database-managed-instance.md)
- Obtenga información sobre la [arquitectura de conectividad en Instancia administrada](sql-database-managed-instance-connectivity-architecture.md).
- Aprenda a [modificar una red virtual existente para Instancia administrada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para obtener un tutorial que muestre cómo crear una red virtual, crear una instancia administrada y restaurar una base de datos desde una copia de seguridad de base de datos, consulte [Creación de una Instancia administrada de Azure SQL Database](sql-database-managed-instance-get-started.md).
- Para ver información relativa a los distintos problemas de DNS, consulte [Configuración de un DNS personalizado](sql-database-managed-instance-custom-dns.md).
