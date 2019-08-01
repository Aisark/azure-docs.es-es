---
title: Conectar Excel a una base de datos única en Azure SQL Database | Microsoft Docs
description: Obtenga más información acerca de cómo conectar Microsoft Excel a una base de datos única en Azure SQL Database. Importación de datos en Excel para la generación de informes y la exploración de datos.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: 8711d8f7ebc00b2d0fa51ff9f420293e96766d1c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569364"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Conectar Excel a una base de datos única de Azure SQL Database y crear un informe

Conecte Excel a una base de datos única de Azure SQL Database e importe datos y cree tablas y gráficos basados en los valores de la base de datos. En este tutorial va a configurar la conexión entre Excel y una tabla de base de datos, guardar el archivo que almacena los datos y la información de conexión de Excel y, finalmente, crear un gráfico dinámico a partir de los valores de la base de datos.

Antes de comenzar, necesitará una base de datos única. Si no tiene una, consulte [Creación de una base de datos única](sql-database-single-database-get-started.md) y [Create server-level IP firewall](sql-database-server-level-firewall-rule.md) (Creación de un firewall de IP de nivel de servidor) para tener en funcionamiento en unos minutos una base de datos única con datos de ejemplo.

En este artículo se importarán los datos de ejemplo en Excel de dicho artículo, pero puede seguir los pasos con sus propios datos.

También necesitará una copia de Excel. Este artículo usa [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Conectar Excel a SQL Database y cargar datos

1. Para conectar Excel a Base de datos SQL, abra Excel y cree un libro nuevo o abra uno existente.
2. En la barra de menús de la parte superior de la página, seleccione la pestaña **Datos**, **Obtener datos**, De Azure y luego seleccione **De Azure SQL Database**. 

   ![Selección de origen de datos: Conexión de Excel a SQL Database.](./media/sql-database-connect-excel/excel_data_source.png)

   Se abre el Asistente para la conexión de datos.
3. En el cuadro de diálogo **Conectar con el servidor de la base de datos**, escriba el **nombre del servidor** de SQL Database al que quiere conectarse con el formato <*nombreDeServidor*> **.database.windows.net**. Por ejemplo, **msftestserver.database.windows.net**. También puede escribir el nombre de la base de datos. Seleccione **Aceptar** para abrir la ventana de credenciales. 

   ![Cuadro de diálogo Conectar con el servidor de la base de datos](media/sql-database-connect-excel/server-name.png)

4. En el cuadro de diálogo **Base de datos de SQL Server**, seleccione **Base de datos** en el lado izquierdo y luego escriba el **Nombre de usuario** y la **Contraseña** del servidor de SQL Database al que se quiere conectar. Seleccione **Conectar** para abrir el **navegador**. 

   ![Especificación de las credenciales del nombre del servidor y de inicio de sesión](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Dependiendo de su entorno de red, es posible que no pueda conectarse o que pierda la conexión si el servidor de SQL Database no permite el tráfico de la dirección IP del cliente. Vaya al [Portal de Azure](https://portal.azure.com/), haga clic en Servidores SQL Server, haga clic en su servidor, haga clic en Firewall en Configuración y agregue la dirección IP de cliente. Consulte [Configuración del firewall](sql-database-configure-firewall-settings.md) para obtener más detalles.

5. En el **navegador**, seleccione en la lista la base de datos con la que quiere trabajar, seleccione las tablas o vistas con las que quiere trabajar (se elige **vGetAllCategories**) y luego seleccione **Cargar** para mover los datos de la base de datos a la hoja de cálculo de Excel.

    ![Selección de una base de datos y una tabla.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importación de los datos a Excel y creación de un gráfico dinámico

Ahora que ha establecido la conexión, tiene varias opciones para cargar los datos. Por ejemplo, con los pasos siguientes se crea un gráfico dinámico basado en los datos de SQL Database. 

1. Siga los pasos de la sección anterior, pero esta vez, en lugar de seleccionar **Cargar**, seleccione **Cargar en** en la lista desplegable **Cargar**.
2. Luego seleccione cómo quiere ver estos datos en el libro. Elegimos **Gráfico dinámico**. También puede optar por crear una **nueva hoja de cálculo** o **Agregar estos datos al Modelo de datos**. Para más información sobre los modelos de datos, consulte [Crear un modelo de datos en Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Elección del formato de datos en Excel](./media/sql-database-connect-excel/import-data.png)

    La hoja de cálculo ahora tiene una tabla y un gráfico dinámicos vacíos.
3. En **Campos de tabla dinámica**, seleccione todas las casillas de los campos que desea ver.

    ![Configuración de informe de base de datos.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Si quiere conectar otros libros y hojas de cálculo de Excel a la base de datos, seleccione la pestaña **Datos** y luego **Orígenes recientes** para iniciar el cuadro de diálogo **Orígenes recientes**. Desde allí, elija la conexión que ha creado en la lista y luego haga clic en **Abrir**.
> ![Cuadro de diálogo Orígenes recientes](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Crear una conexión permanente con el archivo .odc

Para guardar los detalles de conexión de forma permanente, puede crear un archivo .odc y convertir esta conexión en una opción seleccionable del cuadro de diálogo **Conexiones existentes**. 

1. En la barra de menús de la parte superior de la página, seleccione la pestaña **Datos** y luego **Conexiones existentes** para iniciar el cuadro de diálogo **Conexiones existentes**. 
   1. Seleccione **Buscar más** para abrir el cuadro de diálogo **Seleccionar origen de datos**.   
   2. Seleccione el archivo **+NewSqlServerConnection.odc** y luego **Abrir** para abrir el **Asistente para la conexión de datos**.

      ![Cuadro de diálogo Nueva conexión](media/sql-database-connect-excel/new-connection.png)

2. En el **Asistente para la conexión de datos**, escriba el nombre del servidor y las credenciales de SQL Database. Seleccione **Next** (Siguiente). 
   1. Seleccione la base de datos que contiene los datos en la lista desplegable. 
   2. Seleccione la tabla o vista que le interesa. Se elige vGetAllCategories en este caso.
   3. Seleccione **Next** (Siguiente). 

      ![Asistente para la conexión de datos](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Seleccione la ubicación del archivo, el **Nombre de archivo** y el **Nombre descriptivo** en la siguiente pantalla del Asistente para la conexión de datos. También puede optar por guardar la contraseña en el archivo, aunque esto puede exponer los datos a accesos no deseados. Seleccione **Finalizar** cuando esté listo. 

    ![Guardar la conexión de datos](media/sql-database-connect-excel/save-data-connection.png)

4. Seleccione cómo quiere importar los datos. En este caso se ha optado por una tabla dinámica. También puede modificar las propiedades de la conexión si selecciona **Propiedades**. Seleccione **Aceptar** cuando esté listo. Si no ha optado por guardar la contraseña con el archivo, se le pide que especifique las credenciales. 

    ![Importar datos](media/sql-database-connect-excel/import-data2.png)

5. Compruebe que la nueva conexión se ha guardado al expandir la pestaña **Datos** y seleccionar **Conexiones existentes**. 

    ![Conexión existente](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Conexión a la SQL Database con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md) para obtener más información sobre consultas y análisis avanzados.
* Más información sobre las ventajas de los [grupos elásticos](sql-database-elastic-pool.md).
* Más información en [Crear una aplicación ASP.NET MVC con la autenticación y SQL Database e implementar al Servicio de aplicaciones de Azure](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
