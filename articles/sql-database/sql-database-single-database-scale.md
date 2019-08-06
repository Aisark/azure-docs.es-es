---
title: 'Escalar recursos de base de datos única: Azure SQL Database | Microsoft Docs'
description: En este artículo se describe cómo escalar los recursos de proceso y almacenamiento disponibles para una base de datos única en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: e03c68854d9150c25019fe198fe855a011750844
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566543"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Escalar recursos de base de datos única en Azure SQL Database

En este artículo se describe cómo escalar los recursos de proceso y almacenamiento disponibles para una base de datos única en el nivel de proceso aprovisionado. Como alternativa, el [nivel de proceso sin servidor (versión preliminar)](sql-database-serverless.md) proporciona escalado automático de proceso y factura por segundo el proceso que se usa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos.

## <a name="change-compute-size-vcores-or-dtus"></a>Cambio del tamaño de proceso (núcleos virtuales o DTU)

Después de elegir inicialmente el número de núcleos virtuales o DTU, puede escalar o reducir una base de datos verticalmente de manera dinámica en función de la experiencia real mediante [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az-sql-db-update) o la [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

El vídeo siguiente muestra cómo cambiar de manera dinámica el nivel de servicio y el tamaño de proceso para aumentar las DTU disponibles para una base de datos única.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Administración del espacio de archivo en Azure SQL Database](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto de cambiar el nivel de servicio o la escala del tamaño de proceso

El cambio en el nivel de servicio o el tamaño de proceso de una base de datos única principalmente implica que el servicio realice los pasos siguientes:

1. Creación de una instancia de proceso nueva para la base de datos  

    Se crea una instancia de proceso nueva para la base de datos con el tamaño de proceso y el nivel de servicio solicitados. Para algunas combinaciones de cambios en el nivel de servicio y el tamaño de proceso, se debe crear una réplica de la base de datos en la nueva instancia de proceso que implique copiar los datos y pueda influir en gran medida en la latencia general. En cualquier caso, la base de datos permanece en línea durante este paso y las conexiones se continúan dirigiendo a la base de datos en la instancia de proceso original.

2. Cambio en el enrutamiento de conexiones a la nueva instancia de proceso

    Se colocan las conexiones existentes en la base de datos en la instancia de proceso original. En la nueva instancia de proceso las nuevas conexiones se establecen en la base de datos. Para algunas combinaciones de cambios de nivel de servicio y de tamaño de proceso, los archivos de base de datos se desasocian y se vuelven a asociar durante la modificación.  No obstante, el modificador puede provocar una breve interrupción del servicio cuando la base de datos no esté disponible de forma general durante menos de 30 segundos y, a menudo, durante solo unos segundos. Si hay transacciones de larga duración que se ejecutan cuando se colocan las conexiones, la duración de este paso puede ser mayor con el fin de recuperar las transacciones anuladas. La [Recuperación de base de datos acelerada](sql-database-accelerated-database-recovery.md) puede reducir el impacto de la anulación de transacciones de larga duración.

> [!IMPORTANT]
> Durante los pasos del flujo de trabajo no se pierden datos.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latencia de cambiar el nivel de servicio o la escala del tamaño de proceso

La latencia estimada de cambiar el nivel de servicio o la escala del tamaño de proceso de una base de datos única o un grupo elástico se parametriza como sigue:

|Nivel de servicio|Base de datos única básica,</br>estándar (S0-S1)|Grupo elástico básico,</br>estándar (S2-S12), </br>hiperescala, </br>base de datos única o grupo elástico de uso general|Base de datos única o grupo elástico Premium o Crítico para la empresa|
|:---|:---|:---|:---|
|**Base de datos único básica,</br> estándar (S0-S1)**|&bull; &nbsp;Latencia de tiempo constante independientemente del espacio usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|
|**Grupo elástico básico, </br>estándar (S2-S12) </br>hiperescala, </br>base de datos única o grupo elástico de uso general**|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|&bull; &nbsp;Latencia de tiempo constante independientemente del espacio usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|
|**Base de datos única o grupo elástico Premium o Crítico para la empresa**|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|&bull; &nbsp;Latencia proporcional al espacio usado en la base de datos debido a la copia de datos</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espacio usado|

> [!TIP]
> Para supervisar las operaciones en curso, consulte: [Administración de operaciones mediante la API REST de SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Administración de operaciones mediante la CLI](/cli/azure/sql/db/op), [Supervisión de operaciones mediante T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) y estos dos comandos de PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) y [Stop AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Cancelación de cambios del nivel de servicio o de la escala del proceso

Un cambio del nivel de servicio o de la escala del proceso se puede cancelar.

#### <a name="azure-portal"></a>Portal de Azure

En la hoja de información general de la base de datos, vaya a **Notificaciones** y haga clic en el icono que indica que hay una operación en curso:

![Operación en curso](media/sql-database-single-database-scale/ongoing-operations.png)

A continuación, haga clic en el botón **Cancelar esta operación**.

![Cancelación de la operación en curso](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Desde un símbolo del sistema de PowerShell, defina los valores `$ResourceGroupName`, `$ServerName` y `$DatabaseName` y, después, ejecute el comando siguiente:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Consideraciones adicionales cuando se cambia el nivel de servicio o la escala de tamaño de proceso

- Si va a actualizar a un nivel de servicio o tamaño de proceso más elevado, el tamaño máximo de la base de datos no aumenta a no ser que especifique un tamaño mayor (maxsize).
- Para cambiar una base de datos a una versión anterior, su espacio usado no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino y del tamaño de proceso.
- Al pasar del nivel **Premium** al nivel **Estándar**, se aplica un costo de almacenamiento adicional si (1) el tamaño máximo de la base de datos es compatible con el tamaño de proceso de destino y (2) el tamaño máximo supera la cantidad de almacenamiento incluido del tamaño de proceso de destino. Por ejemplo, si una base de datos P1 con un tamaño máximo de 500 GB se reduce a S3, se aplica un costo de almacenamiento adicional porque S3 admite un tamaño máximo de 1 TB y su cantidad de almacenamiento incluido es solo de 250 GB. Por lo tanto, la cantidad de almacenamiento adicional es 500 GB – 250 GB = 250 GB. Para conocer el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Si la cantidad de espacio real utilizada es menor que la cantidad de almacenamiento incluido, este costo adicional puede evitarse si se reduce el tamaño máximo de la base de datos a la cantidad incluida.
- Al actualizar una base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, actualice sus bases de datos secundarias al nivel de servicio y al tamaño de proceso deseados antes de actualizar la base de datos principal (instrucciones generales para mejorar el rendimiento). Al actualizar a una diferente, antes hay que actualizar la base de datos secundaria.
- Al cambiar a una versión anterior la base de datos con la [replicación geográfica](sql-database-geo-replication-portal.md) habilitada, se cambian también sus bases de datos principales al nivel de servicio y tamaño de proceso deseados antes de cambiar a una versión anterior la base de datos secundaria (regla general para un mejor rendimiento). Al cambiar a una versión anterior a una edición diferente, antes hay que cambiar la principal a una versión anterior.
- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si va a cambiar al nivel **Básico**, hay un período de retención de copia de seguridad más bajo. Consulte el artículo sobre [Copias de seguridad de Azure SQL Database](sql-database-automated-backups.md).
- Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

### <a name="billing-during-compute-rescaling"></a>Facturación durante el cambio de escala de proceso

Se le cobrará por cada hora que una base de datos exista con el mayor nivel de servicio + tamaño de proceso aplicable durante esa hora, independientemente del uso o de si la base de datos estuvo activa durante menos tiempo. Por ejemplo, si crea una base de datos única y la elimina a los cinco minutos, se le efectuará un cargo de una hora por usar la base de datos.

## <a name="change-storage-size"></a>Modificar el tamaño de almacenamiento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra basado en núcleo virtual

- Se puede aprovisionar el almacenamiento hasta el límite máximo de tamaño con incrementos de 1 GB. El almacenamiento de datos mínimo configurable es 5 GB
- Se puede aprovisionar almacenamiento para una base de datos única mediante el aumento o disminución de su tamaño máximo con [Azure Portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az-sql-db-update) o la [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database asigna automáticamente 30 % de almacenamiento adicional para los archivos de registro y 32 GB por núcleo virtual para TempDB, sin exceder los 384 GB. TempDB se encuentra en una SSD asociada en todos los niveles de servicio.
- El precio de almacenamiento para una única base de datos es la suma de las cantidades de almacenamiento de datos y de registros, multiplicada por el precio de la unidad de almacenamiento del nivel de servicio. El costo de TempDB se incluye en el precio del núcleo virtual. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Administración del espacio de archivo en Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra basado en DTU

- El precio de la DTU para una base de datos única incluye una cierta cantidad de almacenamiento sin costo adicional. El almacenamiento adicional que supere la cantidad incluida se puede aprovisionar por un costo extra hasta el límite de tamaño máximo en incrementos de 250 GB hasta 1 TB, y luego en incrementos de 256 GB superando 1 TB. Para más información sobre los límites de tamaño máximo y las cantidades de almacenamiento incluidas, consulte [Base de datos única: tamaños de almacenamiento y tamaños de proceso](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Se puede aprovisionar almacenamiento adicional para una base de datos única aumentando su tamaño máximo con Azure Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), la [CLI de Azure](/cli/azure/sql/db#az-sql-db-update) o la [API de REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- El precio del almacenamiento adicional para una base de datos única es la cantidad de almacenamiento adicional multiplicado por el precio de la unidad de almacenamiento adicional del nivel de servicio. Para más información sobre el precio del almacenamiento adicional, consulte los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Administración del espacio de archivo en Azure SQL Database](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 y P15 se restringen cuando el tamaño máximo es mayor de 1 TB

Existe más de 1 TB de almacenamiento en el nivel Premium actualmente disponible en todas las regiones excepto: Este de China, Norte de China, Centro de Alemania, Nordeste de Alemania, Centro-oeste de EE. UU., US regiones de US DoD y Centro de Gobierno de EE. UU. En estas regiones, el almacenamiento máximo en el nivel Prémium está limitado a 1 TB. Las siguientes consideraciones y limitaciones se aplican a las bases de datos P11 y P15 con un tamaño máximo mayor de 1 TB:

- Si el tamaño máximo de una base de datos P11 o P15 nunca se estableció en un valor mayor que 1 TB, entonces solo puede restablecerse o copiarse a una base de datos P11 o P15.  Posteriormente, la base de datos puede escalarse de nuevo a un tamaño de proceso diferente siempre que la cantidad de espacio asignado en el momento de la operación de cambio de escala no supere los límites de tamaño máximo con el nuevo tamaño de proceso.
- En escenarios de replicación geográfica activa:
  - Configuración de una relación de replicación geográfica: si la base de datos principal es P11 o P15, la secundaria (una o varias) también debe ser P11 o P15; aquellas con un tamaño de proceso inferior se rechazarán como secundarias, puesto que no tienen capacidad para admitir más de 1 TB.
  - Actualización de la base de datos principal en una relación de replicación geográfica: al cambiar el tamaño máximo a 1 TB en una base de datos principal, se desencadenará el mismo cambio en la base de datos secundaria. Ambas actualizaciones deben realizarse correctamente para que el cambio en la principal surta efecto. Se aplican limitaciones por región para la opción de más de 1 TB. Si la base de datos secundaria está en una región que no admite más de 1 TB, no se actualizará la principal.
- No se admite el uso del servicio Import/Export para cargar bases de datos P11/P15 con más de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) y [exportar](sql-database-export.md) datos.

## <a name="next-steps"></a>Pasos siguientes

Para conocer los límites de recursos globales, consulte [Límites de recursos basados en núcleos virtuales de Azure SQL Database para bases de datos únicas](sql-database-vcore-resource-limits-single-databases.md) y [SQL Database DTU-based resource limits - elastic pools](sql-database-dtu-resource-limits-single-databases.md) (Límites de recursos basados en DTU de SQL Database para grupos elásticos).
