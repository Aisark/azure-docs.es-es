---
title: Introducción a las consultas entre bases de datos (particiones verticales) | Microsoft Docs
description: cómo usar la consulta de base de datos elástica con bases de datos con particiones verticales
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
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307451"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Introducción a las consultas entre bases de datos (particiones verticales) (versión preliminar)

La consulta de base de datos elástica (versión preliminar) para Azure SQL Database le permite ejecutar consultas T-SQL que distribuyen varias bases de datos con un único punto de conexión. Este artículo se aplica a [bases de datos con particiones verticales](sql-database-elastic-query-vertical-partitioning.md).  

Una vez completado, sabrá: cómo configurar y usar una base de datos de Azure SQL para realizar consultas que distribuyan múltiples bases de datos relacionadas.

Para obtener más información sobre la característica de la consulta de base de datos elástica, consulte la [información general sobre consulta de bases de datos elásticas de Azure SQL Database](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Se requiere el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE. Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.

## <a name="create-the-sample-databases"></a>Crear las base de datos de ejemplo

Para empezar, cree dos bases de datos, **Customers** (Clientes) y **Orders** (Pedidos), ya sea en el mismo servidor o en diferentes servidores de SQL Database.

Ejecute las siguientes consultas en la base de datos **Orders** para crear la tabla **OrderInformation** e introducir los datos de ejemplo.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Ahora, ejecute la siguiente consulta en la base de datos **Customers** para crear la tabla **CustomerInformation** e introducir los datos de ejemplo.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Creación de objetos de base de datos

### <a name="database-scoped-master-key-and-credentials"></a>Clave maestra y credenciales de ámbito de base de datos

1. Abra SQL Server Management Studio o SQL Server Data Tools en Visual Studio.
2. Conéctese a la base de datos Pedidos y ejecute los siguientes comandos de T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "username" y "password" deben ser el nombre de usuario y la contraseña que se utilizan para iniciar sesión en la base de datos de Clientes.
    Actualmente no se admite la autenticación usando Azure Active Directory con consultas elásticas.

### <a name="external-data-sources"></a>Orígenes de datos externos

Para crear un origen de datos externo, ejecute el siguiente comando en la base de datos Pedidos:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tablas externas

Cree una tabla externa en la base de datos Pedidos, que coincida con la definición de la tabla CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Ejecución de la consulta de T-SQL de la base de datos elástica de ejemplo

Una vez que haya definido su origen de datos externo y las tablas externas, puede usar el T-SQL para consultar las tablas externas. Ejecute esta consulta en la base de datos Pedidos:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Coste

En la actualidad, la característica de consulta de base de datos elástica se incluye en el costo de su base de datos de Azure SQL.  

Para obtener información de precios, consulte [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre las consultas elásticas, consulte [Información general sobre las consultas elásticas](sql-database-elastic-query-overview.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento vertical, consulte [Consulta de datos particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md)
* Para obtener un tutorial sobre la creación de particiones horizontales (particionamiento), consulte [Introducción a las consultas elásticas para las particiones horizontales (particionamiento)](sql-database-elastic-query-getting-started.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento horizontal, consulte [Consulta de datos particionados horizontalmente.](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.
