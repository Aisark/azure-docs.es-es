---
title: Copia de datos locales con la herramienta Copiar datos de Azure
description: Cree una instancia de Azure Data Factory y use la herramienta Copy Data para copiar datos locales de Azure SQL Server Database en Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 04/09/2018
ms.author: abnarain
ms.openlocfilehash: 3b9c9371d947b0193f93b8084fbaaf66e3253997
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683546"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copia de datos de una base de datos de SQL Server local en Azure Blob Storage con la herramienta Copy Data
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión actual](tutorial-hybrid-copy-data-tool.md)

En este tutorial, usará Azure Portal para crear una factoría de datos. A continuación, usará la herramienta Copy Data para crear una canalización que copia datos de una instancia de base de datos de SQL Server local en una instancia de Azure Blob Storage.

> [!NOTE]
> - Si no está familiarizado con Azure Data Factory, consulte [Introducción a Data Factory](introduction.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

## <a name="prerequisites"></a>Requisitos previos
### <a name="azure-subscription"></a>Suscripción de Azure
Antes de empezar, si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, a la cuenta de usuario que use para iniciar sesión en Azure debe se le deben asignar los roles *Colaborador* o *Propietario*, o bien debe de ser de un *administrador* de la suscripción a Azure. 

Para ver los permisos que tiene en la suscripción, vaya a Azure Portal. Seleccione su nombre de usuario en la esquina superior derecha y luego seleccione **Permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente. Para instrucciones de ejemplo sobre cómo agregar un rol a un usuario, consulte [Administración del acceso mediante RBAC y Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 y 2017
En este tutorial se usa una base de datos de SQL Server local como almacén de datos de *origen*. La canalización de la factoría de datos que crea en este tutorial, copia los datos de esta base de datos de SQL Server local (origen) a Blob Storage (receptor). Luego, cree una tabla denominada **emp** en la base de datos de SQL Server e inserte un par de entradas de ejemplo en la tabla. 

1. Inicie SQL Server Management Studio. Si no está instalada en su máquina, vaya a [Descarga de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Conéctese a una instancia de SQL Server con sus credenciales. 

1. Cree una base de datos de ejemplo. En la vista de árbol, haga clic con el botón derecho en **Bases de datos** y, luego, seleccione **Nueva base de datos**. 

1. En el cuadro de diálogo **Nueva base de datos**, escriba el nombre de la base de datos y haga clic en **Aceptar**. 

1. Para crear la tabla **emp** e insertar en ella algunos datos de ejemplo, ejecute el siguiente script de consulta en la base de datos. En la vista de árbol, haga clic con el botón derecho en la base de datos que ha creado y, después, haga clic en **Nueva consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Cuenta de almacenamiento de Azure
En este tutorial, use una cuenta de almacenamiento de Azure (en concreto Blob Storage) de uso general como almacén de datos de destino o receptor. Si no dispone de una cuenta de almacenamiento de uso general, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-quickstart-create-account.md), donde se indica cómo crearla. La canalización de la factoría de datos que crea en este tutorial copia datos de la base de datos de SQL Server local (origen) en esta instancia de Blob Storage (receptor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtención de un nombre y una clave de cuenta de almacenamiento
En este tutorial, use el nombre y la clave de su cuenta de almacenamiento. Para obtener el nombre y la clave de la cuenta de almacenamiento, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con el nombre de usuario y la contraseña de Azure. 

1. Seleccione **Todos los servicios** en el panel izquierdo. Use la palabra clave **Almacenamiento** para filtrar el resultado y, luego, seleccione **Cuentas de almacenamiento**.

    ![Búsqueda de cuenta de almacenamiento](media/doc-common-process/search-storage-account.png)

1. En la lista de cuentas de almacenamiento, filtre por su cuenta de almacenamiento, si fuera necesario. Después, seleccione su cuenta de almacenamiento. 

1. En la ventana **Cuenta de almacenamiento**, seleccione **Claves de acceso**.


1. En los cuadros **Nombre de la cuenta de almacenamiento** y **key1**, copie los valores y péguelos en el Bloc de notas, u otro editor, para su uso posterior en el tutorial. 

#### <a name="create-the-adftutorial-container"></a>Creación del contenedor adftutorial 
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Blob Storage. 

1. En la ventana **Cuenta de almacenamiento**, cambie a **Información general** y, después, seleccione **Blobs**. 

1. En la ventana **Blobs**, seleccione **+ Contenedor**. 

1. En la ventana **Nuevo contenedor**, en el cuadro **Nombre**, escriba **adftutorial** y, después, seleccione **Aceptar**. 

1. En la lista de contenedores, seleccione **adftutorial**.


1. Mantenga abierta la ventana **Contenedor** de **adftutorial**. Úselo para comprobar la salida al final de este tutorial. Data Factory crea automáticamente la carpeta de salida de este contenedor, por lo que no es necesario que el usuario la cree.


## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. En el menú de la izquierda, seleccione **+ Crear un recurso** > **Analytics** > **Data Factory**. 
  
   ![Creación de nueva factoría de datos](./media/doc-common-process/new-azure-data-factory-menu.png)

1. En la página **Nueva factoría de datos**, en **Nombre**, escriba **ADFTutorialDataFactory**. 

   El nombre de la factoría de datos tiene que ser *único a nivel global*. Si ve el siguiente mensaje de error en el campo de nombre, cambie el nombre de la factoría de datos (por ejemplo, suNombreADFTutorialDataFactory). Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte [Azure Data Factory: reglas de nomenclatura](naming-rules.md).

   ![Nombre de la nueva factoría de datos](./media/doc-common-process/name-not-available-error.png)
1. Seleccione la **suscripción** de Azure en la que quiere crear la factoría de datos. 
1. Para **Grupo de recursos**, realice uno de los siguientes pasos:
  
   - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable.

   - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos. 
        
     Para más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).
1. En **Versión**, seleccione **V2**.
1. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista desplegable solo se muestran las ubicaciones que se admiten. Los almacenes de datos (por ejemplo, Azure Storage y SQL Database) y los procesos (por ejemplo, Azure HDInsight) que usa Data Factory pueden estar en otras ubicaciones o regiones.
1. Seleccione **Crear**.

1. Una vez finalizada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
  
     ![Página principal Factoría de datos](./media/doc-common-process/data-factory-home-page.png)
1. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Uso de la herramienta Copy Data para crear una canalización

1. En la página **Let's get started** (Introducción), seleccione **Copy Data** (Copiar datos) para iniciar la herramienta Copy Data. 

   ![Página de introducción](./media/doc-common-process/get-started-page.png)

1. En la página **Properties** (Propiedades) de la herramienta Copy Data, en **Task name** (Nombre de la tarea), especifique **CopyFromOnPremSqlToAzureBlobPipeline**. Luego, seleccione **Siguiente**. La herramienta Copy Data crea una canalización con el nombre que especificó para este campo. 
  ![Nombre de tarea](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. En la página **Almacén de datos de origen**, haga clic en **Crear nueva conexión**. 


1. En **New Linked Service** (Nuevo servicio vinculado), busque **SQL Server**y, a continuación, seleccione **Continuar**. 

1. Enel cuadro de diálogo **New Linked Service (SQL Server)**  (Nuevo servicio vinculado), en **Nombre**, escriba **SqlServerLinkedService**. Seleccione **+Nuevo** en la opción **Connect via integration runtime** (Conectar mediante IR). Tiene que crear un entorno de ejecución de integración, descargarlo en la máquina y registrarlo en Data Factory. El entorno de ejecución de integración autohospedado copia datos entre el entorno local y la nube.


1. En el cuadro de diálogo **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione **Autohospedado**. Luego, seleccione **Siguiente**. 

   ![Creación de tiempo de ejecución de integración](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. En el cuadro de diálogo **Integration Runtime Setup** (Configuración de Integration Runtime), en **Name** (Nombre) escriba **TutorialIntegrationRuntime**. Luego, seleccione **Siguiente**. 


1. En el cuadro de diálogo **Integration Runtime Setup** (Configuración de Integration Runtime), seleccione **Click here to launch the express setup for this computer** (Haga clic aquí para iniciar la configuración rápida en este equipo). Esta acción instala el entorno de ejecución de integración en la máquina y la registra en Data Factory. Como alternativa, puede usar la instalación manual para descargar el archivo de instalación, ejecutarlo y registrar la instancia de Integration Runtime con la clave. 

1. Ejecute la aplicación descargada. Verá el estado de la configuración rápida en la ventana. 

    ![Estado de la configuración rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. En el cuadro de diálogo **New Linked Service (SQL Server)** (Nuevo servicio vinculado), confirme que **TutorialIntegrationRuntime** está seleccionado en el campo de Integration Runtime. A continuación, siga estos pasos:

    a. En **Name** (Nombre), escriba **SqlServerLinkedService**.

    b. Escriba el nombre de la instancia de SQL Server local en **Server name** (Nombre del servidor).

    c. Escriba el nombre de la base de datos local en **Database name** (Nombre de la base de datos).

    d. Seleccione la autenticación adecuada en **Authentication type** (Tipo de autenticación).

    e. Escriba el nombre de usuario con acceso a la instancia de SQL Server local en **User name** (Nombre de usuario).

    f. Escriba la **contraseña** del usuario. 

    g. Pruebe la conexión y seleccione **Finalizar**.

      ![Integration Runtime seleccionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. En la página **Source data store** (Almacén de datos de origen), seleccione **Siguiente**.

1. En la página **Select tables from which to copy the data or use a custom query** (Seleccionar tablas de donde copiar los datos o usar una consulta personalizada), seleccione la tabla **[dbo].[emp]** de la lista y luego seleccione **Next** (Siguiente). Puede seleccionar cualquier otra tabla en función de la base de datos.

1. En la página **Almacén de datos de destino**, seleccione **Crear nueva conexión**


1. En **New Linked Service** (Nuevo servicio vinculado), busque y seleccione **Azure Blob** y, después, seleccione **Continuar**. 

   ![Selección de Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. En el cuadro de diálogo **New Linked Service (Azure Blob Storage)** [Nuevo servicio vinculado (Azure Blob Storage)], realice los siguientes pasos: 

   a. En **Name** (Nombre), escriba **AzureStorageLinkedService**.

   b. En **Connect via integration runtime** (Conectar mediante IR), seleccione **TutorialIntegrationRuntime**

   c. Seleccione la cuenta de almacenamiento en la lista desplegable **Storage account name** (Nombre de la cuenta de almacenamiento). 

   d. Seleccione **Finalizar**.

1. En el cuadro de diálogo **Almacén de datos de destino**, asegúrese de que está seleccionado **Azure Blob Storage**. Luego, seleccione **Siguiente**. 

1. En el cuadro de diálogo **Choose the output file or folder** (Elegir el archivo o la carpeta de salida), en **Folder path** (Ruta de acceso a la carpeta) escriba **adftutorial/fromonprem**. El contenedor **adftutorial** se creó como parte de los requisitos previos. Si no existe la carpeta de salida (en este caso **fromonprem**), Data Factory la crea automáticamente. También puede usar el botón **Browse** (Examinar) para examinar Blob Storage y sus contenedores o carpetas. Si no especifica ningún valor en **nombre de archivo**, de forma predeterminada se usará el nombre del origen (en este caso **dbo.emp**).
           
   ![Elección del archivo o la carpeta de salida](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. En el cuadro de diálogo **File format settings** (Configuración de formato de archivo), seleccione **Next** (Siguiente). 

1. En el cuadro de diálogo **Configuración**, seleccione **Next** (Siguiente). 

1. En el cuadro de diálogo **Summary** (Resumen), revise los valores de configuración y seleccione **Next** (Siguiente). 

1. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización o la tarea que ha creado.

   ![Página Deployment (Implementación)](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. En la pestaña **Monitor** (Supervisión), puede ver el estado de la canalización que ha creado. Puede usar los vínculos de la columna **Action** (Acción) para ver las ejecuciones de actividad asociadas a la de la canalización y volver a ejecutar la canalización. 
   
1. Seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones) para ver las ejecuciones de actividad asociadas a las de la canalización. Para ver detalles acerca de la operación de copia, seleccione el vínculo **Details** (Detalles) (icono de gafas) en la columna **Actions** (Acciones). Para volver a la vista **Pipeline Runs** (Ejecuciones de canalización) seleccione **Pipelines Runs** (Ejecuciones de canalizaciones) en la parte superior.

1. Confirme que ve un archivo de salida en la carpeta **fromonprem** del contenedor **adftutorial**. 


1. Seleccione la pestaña **Edit** (Editar) de la izquierda para cambiar al modo de edición. Con el editor puede actualizar los servicios vinculados, los conjuntos de datos y las canalizaciones creados mediante la herramienta. Seleccione **Code** (Código) para ver el código JSON asociado con la entrada abierta en el editor. Para más información sobre cómo editar estas entidades en la interfaz de usuario de Data Factory, consulte [la versión de Azure Portal de este tutorial](tutorial-copy-data-portal.md).

   ![Pestaña Edit (Editar)](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia datos de la base de datos SQL Server local en Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Uso de la herramienta Copy Data para crear una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

Para ver una lista de los almacenes de datos compatibles con Data Factory, consulte los [almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

Para informarse acerca de cómo copiar datos de forma masiva de un origen a un destino, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy-portal.md)
