---
title: Código de archivo de evento de XEvent para SQL Database | Microsoft Docs
description: Brinda PowerShell y Transact-SQL para un ejemplo de código de dos fases que muestra el destino de archivo de evento en un evento extendido en Azure SQL Database. Azure Storage es una parte necesaria en este escenario.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: ce559e50d5a34ebad9113f0e21dcb732adc40dd2
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233796"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino del archivo de evento para eventos extendidos en SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Desea tener un ejemplo de código completo de una manera eficaz para capturar y notificar información de un evento extendido.

En Microsoft SQL Server, el [destino de archivo de evento](https://msdn.microsoft.com/library/ff878115.aspx) se usa para almacenar las salidas de evento en un archivo del disco duro local. Sin embargo, esos archivos no están disponibles para Azure SQL Database. En su lugar, usamos el servicio Azure Storage para admitir el destino de archivo de evento.

En este tema se presenta un ejemplo de código de dos fases:

* PowerShell, para crear un contenedor de Azure Storage en la nube.
* Transact-SQL:
  
  * Para asignar el contenedor de Azure Storage a un destino de archivo de evento.
  * Para crear e iniciar la sesión del evento, etc.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de PowerShell de Azure Resource Manager es compatible aún con Azure SQL Database, pero todo el desarrollo futuro es para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos en el módulo de Az y en los módulos AzureRm son esencialmente idénticos.

* Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Cualquier base de datos donde pueda crear una tabla.
  
  * De manera opcional, puede [crear una base de datos de **AdventureWorksLT** de demostración](sql-database-get-started.md) en cuestión de minutos.
* SQL Server Management Studio (ssms.exe), idealmente la versión de actualización mensual más reciente. 
  Puede descargar la versión más reciente de ssms.exe desde:
  
  * El tema titulado [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Un vínculo directo a la descarga.](https://go.microsoft.com/fwlink/?linkid=616025)
* Debe tener instalados los [módulos de Azure PowerShell](https://go.microsoft.com/?linkid=9811175) .
  
  * Los módulos brindan comandos como - **New AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: código de PowerShell para el contenedor de Azure Storage

Esta es la fase 1 del ejemplo de código de dos fases.

El script comienza con comandos que realizan una limpieza después de una posible ejecución anterior y se puede volver a ejecutar.

1. Pegue el script de PowerShell en un editor de texto simple, como Notepad.exe, y guárdelo como archivo con extensión **.ps1**.
2. Inicie PowerShell ISE como administrador.
3. En el símbolo del sistema, escriba<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>y, luego, presione Entrar.
4. En PowerShell ISE, abra el archivo **.ps1** . Ejecute el script.
5. El script primero inicia una ventana nueva donde se inicia sesión en Azure.
   
   * Si vuelve a ejecutar el script sin interrumpir la sesión, tiene la opción conveniente de marcar el comando **Add-AzureAccount** como comentario.

![PowerShell ISE, con el módulo de Azure instalado, está preparado para ejecutar el script.][30_powershell_ise]

### <a name="powershell-code"></a>Código de PowerShell

Este script de PowerShell se da por supuesto que ya ha instalado el módulo de Az. Para obtener información, consulte [instalar el módulo Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Anote los valores con nombre que el script de PowerShell imprime cuando finaliza. Debe editar esos valores en el script de Transact-SQL que sigue en la fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: código de Transact-SQL que usa el contenedor de Azure Storage

* En la fase 1 de este ejemplo de código, ejecutó un script de PowerShell para crear un contenedor de Azure Storage.
* A continuación, en la fase 2, el siguiente script de Transact-SQL debe usar el contenedor.

El script comienza con comandos que realizan una limpieza después de una posible ejecución anterior y se puede volver a ejecutar.

El script de PowerShell imprimió algunos valores con nombre cuando finalizó. Debe editar el script de Transact-SQL para que use esos valores. Busque **TODO** en el script de Transact-SQL para ubicar los puntos de edición.

1. Abra SQL Server Management Studio (ssms.exe).
2. Conéctese a la base de datos de Azure SQL Database.
3. Haga clic para abrir un nuevo panel de consulta.
4. Pegue el siguiente script de Transact-SQL en el panel de consulta.
5. Busque cada **TODO** en el script y haga las ediciones correspondientes.
6. Guarde y, luego, ejecute el script.


> [!WARNING]
> El valor de clave SAS generado con el script de PowerShell anterior podría comenzar con un carácter "?" (signo de interrogación). Al utilizar la clave SAS en el siguiente script de T-SQL, debe *quitar el carácter "?" inicial*. De lo contrario, podrían bloquearse su trabajo por motivos de seguridad.


### <a name="transact-sql-code"></a>Código Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```


Si el destino no se adjunta cuando ejecuta el script, debe detener la sesión de evento y reiniciarla:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Salida

Cuando se complete el script de Transact-SQL, haga clic en una celda bajo el encabezado de la columna **event_data_XML**. Una  **\<evento >** se muestran los elementos que se muestra una instrucción UPDATE.

Este es un  **\<evento >** elemento que se generó durante las pruebas:


```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```


El script Transact-SQL anterior utiliza la siguiente función de sistema para leer el archivo event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

En la siguiente página encontrará una explicación de las opciones avanzadas de la visualización de datos de eventos extendidos:

* [Advanced Viewing of Target Data from Extended Events (Visualización avanzada de datos de destino de eventos extendidos)](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversión del ejemplo de código para ejecutarlo en SQL Server

Suponga que desea ejecutar el ejemplo de Transact-SQL anterior en Microsoft SQL Server.

* Para mantener la simplicidad, desearía reemplazar completamente el uso del contenedor de Azure Storage por un archivo simple como **C:\myeventdata.xel**. El archivo se escribiría en el disco duro local del equipo donde se hospeda SQL Server.
* No necesitaría ningún tipo de instrucción Transact-SQL para **CREATE MASTER KEY** y **CREATE CREDENTIAL**.
* En la instrucción **CREATE EVENT SESSION**, en su cláusula **ADD TARGET**, reemplazaría el valor Http asignado en **filename=** por una cadena de ruta de acceso completa como **C:\myfile.xel**.
  
  * No es necesario utilizar ninguna cuenta de Azure Storage.

## <a name="more-information"></a>Más información

Si desea obtener más información sobre las cuentas y los contenedores del servicio Azure Storage, consulte:

* [Uso del almacenamiento de blobs de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Asignar nombres y hacer referencia a contenedores, blobs y metadatos](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Trabajar con el contenedor raíz](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lección 1: Crear una directiva de acceso almacenada y una firma de acceso compartido en un contenedor de Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  * [Lección 2: Crear una credencial de SQL Server con una firma de acceso compartido](https://msdn.microsoft.com/library/dn466435.aspx)
* [Eventos extendidos para Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

