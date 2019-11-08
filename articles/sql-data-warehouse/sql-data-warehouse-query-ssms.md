---
title: Conectarse con SSMS
description: Use SQL Server Management Studio (SSMS) para conectarse a y realizar consultas en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d5c903a24ea47cb152555330688dd0bc515c625b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692586"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Conexión a SQL Data Warehouse con SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use SQL Server Management Studio (SSMS) para conectarse a y realizar consultas en Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Requisitos previos
Para utilizar este tutorial, necesitará:

* Una cuenta de SQL Data Warehouse existente. Para crear una, consulte [Creación de una instancia de Azure SQL Data Warehouse][Create a SQL Data Warehouse].
* SQL Server Management Studio (SSMS) instalado. [Instale SSMS][Install SSMS] de forma gratuita si aún no lo tiene.
* El nombre del servidor SQL completo. Para encontrarlo, consulte [Connect to Azure SQL Data Warehouse][Connect to SQL Data Warehouse](Conexión a Almacenamiento de datos SQL).

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conexión a la instancia de SQL Data Warehouse
1. Abra SSMS.
2. Abra el Explorador de objetos. Para ello, seleccione **Archivo** > **Conectar Explorador de objetos**.
   
    ![Explorador de objetos de SQL Server][1]
3. Rellene los campos en la ventana Conectar al servidor.
   
    ![Conectar al servidor][2]
   
   * **Nombre del servidor**. Escriba el **nombre del servidor** definido anteriormente.
   * **Autenticación**. Seleccione **Autenticación de SQL Server** o **Autenticación integrada de Active Directory**.
   * **Nombre de usuario** y **Contraseña**. Escriba el nombre de usuario y la contraseña si la autenticación de SQL Server se seleccionó anteriormente.
   * Haga clic en **Conectar**.
4. Para explorar, expanda su Azure SQL Server. Puede ver las bases de datos asociadas al servidor. Expanda AdventureWorksDW para ver las tablas de la base de datos de ejemplo.
   
    ![Explorar AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Ejecución de una consulta de ejemplo
Ahora que se ha establecido una conexión a la base de datos, pasemos a escribir una consulta.

1. Haga clic con el botón derecho en la base de datos en el Explorador de objetos de SQL Server.
2. Seleccione **Nueva consulta**. Se abre una nueva ventana de consulta.
   
    ![Nueva consulta][4]
3. Copie esta consulta TSQL en la ventana de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Ejecute la consulta. Para hacerlo, haga clic en `Execute` o use la combinación de teclas `F5`.
   
    ![Ejecutar consulta][5]
5. Consulte los resultados de la consulta. En este ejemplo, la tabla FactInternetSales tiene 60398 filas.
   
    ![Resultados de la consulta][6]

## <a name="next-steps"></a>Pasos siguientes
Ahora que puede conectarse y realizar consultas, pruebe a [visualizar los datos con PowerBI][visualizing the data with PowerBI].

Para configurar el entorno para la autenticación de Azure Active Directory, consulte [Authentication to Azure SQL Data Warehouse][Authenticate to SQL Data Warehouse](Autenticación en Almacenamiento de datos SQL de Azure).

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
