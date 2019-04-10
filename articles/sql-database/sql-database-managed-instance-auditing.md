---
title: Auditoría de Instancia administrada de Azure SQL Database | Microsoft Docs
description: Obtención de información acerca de cómo auditar Instancia administrada de Azure SQL Database mediante T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 6ada2a5e505bfe37f4f9a956570d8b6f38f55e55
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357426"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introducción a la auditoría de Instancia administrada de Azure SQL Database

La auditoría de [Instancia administrada](sql-database-managed-instance.md) hace un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de su cuenta de Azure Storage. La auditoría también:

- Puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.
- Posibilita y facilita la observancia de estándares reguladores aunque no garantiza el cumplimiento. Para obtener más información sobre Azure programas ese cumplimiento de estándares de soporte técnico, consulte el [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) donde puede encontrar la lista más actualizada de certificaciones de cumplimiento de la base de datos SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configuración de la auditoría de un servidor en Azure Storage

En la sección siguiente se describe la configuración de auditoría en su Instancia administrada.

1. Vaya a [Azure Portal](https://portal.azure.com).
1. Cree un **contenedor** de Azure Storage donde se almacenan los registros de auditoría.

   1. Navegue hasta Azure Storage donde le gustaría almacenar los registros de auditoría.

      > [!IMPORTANT]
      > Use una cuenta de almacenamiento de la misma región que la Instancia administrada para evitar lecturas y escrituras entre regiones.

   1. En la cuenta de almacenamiento, vaya a **Información general** y haga clic en **Blobs**.

      ![Widget de blobs de Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. En el menú superior, haga clic en **+ Contenedor** para crear un nuevo contenedor.

      ![Icono Crear contenedor de blobs](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. En **Nombre** proporcione un nombre de contenedor, establezca el nivel de acceso público en **Privado**y haga clic en **Aceptar**.

      ![Configuración de la opción Crear contenedor de blobs](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Después de crear el contenedor para los registros de auditoría, hay dos maneras de configurarlo como destino de los registros de auditoría: [mediante T-SQL](#blobtsql) o [mediante la interfaz de usuario de SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Configuración del almacenamiento de blobs para los registros de auditoría con T-SQL:

     1. En la lista de contenedores, haga clic en el contenedor recién creado y luego en **Propiedades del contenedor**.

        ![Botón Propiedades del contenedor de blobs](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie la dirección URL del contenedor haciendo clic en el icono de copia y guarde dicha dirección (por ejemplo, en el Bloc de notas) para un uso futuro. El formato de dirección URL de contenedor debe ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Dirección URL de copia de contenedores de blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Genere un **token de SAS** de Azure Storage para conceder derechos de acceso de auditoría de Instancia administrada a la cuenta de almacenamiento:

        - Vaya a la cuenta de Azure Storage donde se creó el contenedor en el paso anterior.

        - Haga clic en **Firma de acceso compartido** en el menú Configuración de Storage.

          ![Icono de Firma de acceso compartido en el menú Configuración de Storage](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure SAS de la siguiente manera:

          - **Servicios permitidos**: Blob

          - **Fecha de inicio**: para evitar problemas relacionados con la zona horaria, se recomienda utilizar la fecha de ayer.

          - **Fecha de finalización**: elija la fecha en que expira este token de SAS.

            > [!NOTE]
            > Renueve el token tras la expiración para evitar errores de auditoría.

          - Haga clic en **Generar SAS**.
            
            ![Configuración de SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Después de hacer clic en Generar SAS, el token de SAS aparecerá en la parte inferior. Copie el token haciendo clic en el icono de copia y guárdelo (por ejemplo, en el Bloc de notas) para un uso futuro.

          ![Copia del token de SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Quite el signo de interrogación ("?") caracteres del principio del token.

     1. Conéctese a la Instancia administrada mediante SQL Server Management Studio (SSMS) o cualquier otra herramienta compatible.

     1. Ejecute la siguiente instrucción T-SQL para **crear una nueva credencial** utilizando la dirección URL del contenedor y el token de SAS que creó en los pasos anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Ejecute la siguiente instrucción T-SQL para crear una nueva auditoría de servidor (elija su propio nombre de auditoría y use la dirección URL del contenedor que creó en los pasos anteriores). Si no se especifica, el valor predeterminado de `RETENTION_DAYS` es 0 (retención ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Continúe por la [creación de una especificación de auditoría de servidor o una de auditoría de base de datos](#createspec)

   - <a id="blobssms"></a>Configuración del almacenamiento de blobs para los registros de auditoría mediante SQL Server Management Studio (SSMS) 18 (versión preliminar):

     1. Conéctese a la instancia administrada mediante la interfaz de usuario de SQL Server Management Studio (SSMS).

     1. Expanda la nota de raíz de Explorador de objetos.

     1. Expanda el nodo **Security**, haga clic con el botón derecho en el nodo **Audits** y haga clic en "Nueva auditoría":

        ![Expandir nodo security y audit](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Asegúrese de que la opción "URL" está seleccionada en **Audit destination** (Destino de auditoría) y haga clic en **Examinar**:

        ![Examinar Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Inicie sesión en la cuenta de Azure:

        ![Inicio de sesión en Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Seleccione una suscripción, la cuenta de almacenamiento y el contenedor de blobs en los menús desplegables o cree su propio contenedor. Para ello, haga clic en **Crear**. Cuando haya terminado, haga clic en **Aceptar**:

        ![Seleccione la suscripción de Azure, cuenta de almacenamiento y contenedor de blobs](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Haga clic en **Aceptar** en el cuadro de diálogo "Crear auditoría".

1. <a id="createspec"></a>Después de configurar el contenedor de blobs como destino para los registros de auditoría, cree una especificación de auditoría de servidor o una de auditoría de base de datos como lo haría para SQL Server:

   - [Crear la Guía de T-SQL de especificación de auditoría de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Crear la Guía de T-SQL de especificación de auditoría de base de datos](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Habilite la auditoría de servidor que creó en el paso 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para información adicional:

- [Diferencias de auditoría entre bases de datos únicas, grupo elástico, s y las instancias administradas de Azure SQL Database y bases de datos de SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREAR AUDITORÍA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [LA INSTRUCCIÓN ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurar la auditoría de su servidor para los registros del centro de eventos o Azure Monitor

Los registros de auditoría de una instancia administrada se pueden enviar a Event Hubs o registros de Azure Monitor. En esta sección se describe cómo configurar todo esto:

1. En [Azure Portal ](https://portal.azure.com/), vaya a la instancia administrada.

2. Haga clic en **Configuración de diagnóstico**.

3. Haga clic en **Activar diagnóstico**. Si ya está habilitado el diagnóstico, en su lugar, se mostrará *+Add diagnostic setting* (+Agregar configuración de diagnóstico).

4. Seleccione **SQLSecurityAuditEvents** en la lista de registros.

5. Seleccione un destino para los eventos de auditoría - centro de eventos, registros de Azure Monitor o ambos. Configure los parámetros necesarios (por ejemplo, el área de trabajo de Log Analytics) en cada destino.

6. Haga clic en **Save**(Guardar).

    ![Configuración de valores de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Conéctese a la instancia administrada mediante **SQL Server Management Studio (SSMS)** o cualquier otro cliente compatible.

8. Ejecute la siguiente instrucción T-SQL para crear una auditoría de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Cree una especificación de auditoría de servidor o la especificación de auditoría de base de datos como lo haría para SQL Server:

   - [Crear la Guía de T-SQL de especificación de auditoría de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Crear la Guía de T-SQL de especificación de auditoría de base de datos](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Habilitar la auditoría de servidor que creó en el paso 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Uso de registros de auditoría

### <a name="consume-logs-stored-in-azure-storage"></a>Uso de registros almacenados en Azure Storage

Existen varios métodos que puede usar para ver los registros de auditoría de blobs.

- Use la función del sistema `sys.fn_get_audit_file` (T-SQL) para devolver los datos de registro de auditoría en formato tabular. Para más información sobre el uso de esta función, vea la [documentación de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Puede explorar los registros de auditoría con una herramienta como el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). En Azure Storage, los registros de auditoría se guardan como una colección de archivos de blob dentro de un contenedor definido para que almacene los registros de auditoría. Para obtener más información sobre la jerarquía de la carpeta de almacenamiento, las convenciones de nomenclatura y el formato del registro, vea la [referencia del formato de registro de auditoría de blobs](https://go.microsoft.com/fwlink/?linkid=829599).

- Para una lista completa de métodos de consumo del registro de auditoría, consulte el [Get started with SQL database auditing](sql-database-auditing.md) (Introducción a la auditoría de base de datos SQL).

### <a name="consume-logs-stored-in-event-hub"></a>Uso de registros almacenados en Event Hubs

Para consumir datos de registros de auditoría desde el centro de eventos, deberá configurar una secuencia que consuma eventos y los escriba en un destino. Para obtener más información, consulte la documentación de Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir y analizar los registros almacenados en los registros de Azure Monitor

Si los registros de auditoría se escriben en los registros de Azure Monitor, están disponibles en el área de trabajo de Log Analytics, donde puede ejecutar las búsquedas avanzadas en los datos de auditoría. Como punto de partida, navegue hasta el área de trabajo de Log Analytics y en *General* sección, haga clic *registros* y escriba una consulta simple, como: `search "SQLSecurityAuditEvents"` para ver la auditoría de registros.  

Registros de Azure Monitor le ofrece visión operativa en tiempo real mediante búsqueda integrada y paneles personalizados para analizar fácilmente millones de registros en todas las cargas de trabajo y servidores. Para obtener información útil adicional sobre los comandos y lenguaje de búsqueda de registros de Azure Monitor, consulte [referencia sobre búsqueda de registros de Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Diferencias de auditoría entre bases de datos en Azure SQL Database y bases de datos en SQL Server

Las diferencias clave entre la auditoría en bases de datos en Azure SQL Database y bases de datos en SQL Server son las siguientes:

- Con la opción de implementación de instancia administrada en Azure SQL Database, la auditoría funciona en el nivel de servidor y almacena los archivos de registro `.xel` en Azure Blob Storage.
- Con las opciones de implementación de base de datos única y grupo elástico en Azure SQL Database, la auditoría funciona en el nivel de la base de datos.
- En las máquinas virtuales o instancias locales de SQL Server, la auditoría funciona en el nivel de servidor, pero almacena los eventos en registros de eventos de Windows o del sistema de archivos.

En Instancia administrada, la auditoría de XEvent admite Azure Blob Storage como destino. **No se admiten** archivos ni registros de Windows.

Las principales diferencias en la sintaxis de `CREATE AUDIT` para la auditoría en Azure Blob Storage son:

- Se proporciona una nueva sintaxis de `TO URL` que permite especificar la dirección URL del contenedor de Azure Blob Storage donde se colocarán los archivos `.xel`.
- Una nueva sintaxis `TO EXTERNAL MONITOR` se proporciona para permitir que los destinos de los registros de incluso Hub y Azure Monitor.
- La sintaxis `TO FILE` **no se admite** porque SQL Database no puede acceder a los recursos compartidos de archivos de Windows.
- La opción de apagado **no se admite**.
- `queue_delay` 0 es **no admite**.

## <a name="next-steps"></a>Pasos siguientes

- Para una lista completa de métodos de consumo del registro de auditoría, consulte el [Get started with SQL database auditing](sql-database-auditing.md) (Introducción a la auditoría de base de datos SQL).
- Para obtener más información sobre Azure programas ese cumplimiento de estándares de soporte técnico, consulte el [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) donde puede encontrar la lista más actualizada de certificaciones de cumplimiento de la base de datos SQL.

<!--Image references-->









