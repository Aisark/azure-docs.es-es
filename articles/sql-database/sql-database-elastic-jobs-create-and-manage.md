---
title: Administración de grupos de bases de datos de Azure SQL | Microsoft Docs
description: Tutorial sobre la creación y administración de un trabajo elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: a95b62ab8f639ad38ee3ac9ace4f30b62bd852bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476066"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Creación y administración de bases de datos de Azure SQL escaladas horizontalmente con trabajos elásticos (versión preliminar)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


Los **trabajos de Elastic Database** simplifican la administración de grupos de bases de datos al ejecutar operaciones administrativas, como cambios de esquemas, administración de credenciales, actualizaciones de datos de referencias, recopilación de datos de rendimiento o recopilación de telemetría del inquilino (cliente). Trabajos de Elastic Database está actualmente disponible a través de Azure Portal y los cmdlets de PowerShell. En cambio, la funcionalidad reducida de Azure Portal se limita a la ejecución transversal en todas las bases de datos de un [grupo elástico](sql-database-elastic-pool.md). Para tener acceso a otras características y a la ejecución transversal de scripts en un grupo de bases de datos, que puede incluir una colección de bases de datos personalizada definida por el usuario o un conjunto de particiones (creado con la [biblioteca de cliente de Elastic Database](sql-database-elastic-scale-introduction.md)), consulte [Creación y administración de trabajos mediante PowerShell](sql-database-elastic-jobs-powershell.md). Para obtener más información, vea [Información general sobre trabajos de Elastic Database](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Para obtener una versión de evaluación gratuita, consulte [Versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un grupo elástico. Consulte [Acerca de los grupos elásticos](sql-database-elastic-pool.md).
* Instalación de componentes del servicio de trabajo de bases de datos elásticas. Vea [Instalación del servicio de trabajo de bases de datos elásticas](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Creación de trabajos
1. Mediante el [Portal de Azure](https://portal.azure.com), en un grupo de trabajos de base de datos elástica existente, haga clic en **Crear trabajo**.
2. Escriba el nombre de usuario y la contraseña del administrador de base de datos (creados al instalar los trabajos) para la base de datos de control de trabajos (almacenamiento de metadatos de los trabajos).
   
    ![Asigne un nombre al trabajo, escríbalo o péguelo en el código y haga clic en Ejecutar.][1]
3. En la hoja **Crear trabajo** , escriba un nombre para el trabajo.
4. Escriba el nombre de usuario y la contraseña para conectarse a las bases de datos de destino con los permisos necesarios para que la ejecución de script sea correcta.
5. Péguelo o escríbalo en la secuencia de comandos T-SQL.
6. Haga clic en **Guardar** y, a continuación, en **Ejecutar**.
   
    ![Creación y ejecución de trabajos][5]

## <a name="run-idempotent-jobs"></a>Ejecución de trabajos idempotentes
Al ejecutar una secuencia de comandos en un conjunto de bases de datos, debe asegurarse de que la secuencia de comandos sea idempotente. Es decir, la secuencia de comandos debe poder ejecutarse varias veces, incluso si se produce un error antes en un estado incompleto. Por ejemplo, cuando se produce un error en una secuencia de comandos, el trabajo se reintentará de forma automática hasta que se procese correctamente (dentro de los límites, ya que la lógica de reintentos finalmente dejará de reintentar la operación). La manera de hacerlo es usar la una cláusula "IF EXISTS" y eliminar cualquier instancia encontrada antes de crear un nuevo objeto. A continuación se muestra un ejemplo:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

De manera alternativa, puede usar una cláusula "IF NOT EXISTS" antes de crear una nueva instancia:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Esta secuencia de comandos, a continuación, actualizará la tabla creada anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Comprobación del estado del trabajo
Tras iniciar un trabajo, puede comprobar su progreso.

1. En la página del grupo elástico, haga clic en **Administrar trabajos**.
   
    ![Haga clic en "Administrar trabajos".][2]
2. Haga clic en el nombre (a) de un trabajo. El **ESTADO** puede ser "Completado" o "Error". Los detalles del trabajo aparecerán (b) con su fecha y hora de creación y ejecución. La lista (c) que se muestra debajo indica el progreso de la secuencia de comandos en cada base de datos del grupo y proporciona información de fecha y hora.
   
    ![Comprobación de un trabajo finalizado][3]

## <a name="checking-failed-jobs"></a>Comprobación de trabajos con errores
Si se produce un error en un trabajo, puede encontrar un registro de su ejecución. Haga clic en el nombre del trabajo con error para ver sus detalles.

![Comprobación de un trabajo con error][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


