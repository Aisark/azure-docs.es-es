---
title: Realización de una copia de seguridad de SQL Server en Azure como una carga de trabajo DPM
description: Introducción a la copia de seguridad de bases de datos de SQL Server mediante el servicio Azure Backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505937"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Realización de una copia de seguridad de SQL Server en Azure como una carga de trabajo DPM

Este artículo le guiará por los pasos de configuración de la copia de seguridad de bases de datos de SQL Server mediante Azure Backup.

Para realizar una copia de seguridad de las bases de datos de SQL Server en Azure, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para más información, consulte [Creación de una cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

Para realizar una copia de seguridad de una base de datos de SQL Server en Azure y recuperarla de Azure:

1. Cree una directiva de copia de seguridad para proteger las bases de datos de SQL Server en Azure.
1. Cree copias de seguridad a petición en Azure.
1. Recuperación de la base de datos de Azure.

## <a name="before-you-start"></a>Antes de comenzar

Antes de comenzar, asegúrese de que se cumplen todos los [requisitos previos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para usar Azure Backup para proteger las cargas de trabajo. Estas son algunas de las tareas de requisitos previos: 
* Crear un almacén de copia de seguridad.
* Descargar las credenciales de almacén. 
* Instalar el agente de Azure Backup.
* Registrar el servidor en el almacén.

## <a name="create-a-backup-policy"></a>Crear una directiva de copia de seguridad 

Para proteger las bases de datos de SQL Server en Azure, debe crear primero una directiva de copia de seguridad:

1. En el servidor de Data Protection Manager (DPM), seleccione el área de trabajo **Protección**.
1. Seleccione **Nuevo** para crear un grupo de protección.

    ![Creación de un grupo de protección](./media/backup-azure-backup-sql/protection-group.png)
1. En la página de inicio, revise las instrucciones sobre cómo crear un grupo de protección. Luego, seleccione **Siguiente**.
1. Seleccione **Servidores**.

    ![Selección del tipo de grupo de protección Servidores](./media/backup-azure-backup-sql/pg-servers.png)
1. Expanda la máquina de SQL Server en la que se encuentran las bases de datos de las que se van a realizar copias de seguridad. Verá los orígenes de datos de los que se puede hacer copias de seguridad desde ese servidor. Expanda **Todos los recursos compartidos de SQL** y, a continuación, seleccione las bases de datos de las que desea realizar una copia de seguridad. En este ejemplo, seleccionamos ReportServer$MSDPM2012 y ReportServer$MSDPM2012TempDB. Luego, seleccione **Siguiente**.

    ![Selección de una base de datos de SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Asigne un nombre al grupo de protección y, a continuación, seleccione **Deseo protección en línea**.

    ![Elección de un método de protección de datos: protección en disco a corto plazo o protección en línea de Azure](./media/backup-azure-backup-sql/pg-name.png)
1. En la página **Especificar objetivos a corto plazo**, incluya las entradas necesarias para crear puntos de copia de seguridad en el disco.

    En este ejemplo, **Duración de retención** se establece en *5 días*. El campo **Frecuencia de sincronización** de la copia de seguridad se establece en una vez cada *15 minutos*. **Copia de seguridad completa rápida** está establecido en *8:00 P.M*.

    ![Configuración de los objetivos a corto plazo para la protección de copias de seguridad](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > En este ejemplo, se crea un punto de copia de seguridad a las 8:00 P.M. todos los días. Los datos que se han modificado desde el punto de copia de seguridad de las 8:00 P.M. del día anterior se transfieren. Este proceso se denomina **Copia de seguridad completa rápida**. Mientras los registros transaccionales se sincronizan cada 15 minutos, si es necesario recuperar la base de datos a las 9:00 P.M., se crea el punto mediante la reproducción de los registros de la última copia de seguridad completa rápida (8:00 P.M. en este caso).
   >
   >

1. Seleccione **Next** (Siguiente). DPM muestra el espacio de almacenamiento total disponible. También muestra el uso de espacio en disco potencial.

    ![Configuración de la asignación de discos](./media/backup-azure-backup-sql/pg-storage.png)

    De forma predeterminada, DPM crea un volumen por origen de datos (base de datos de SQL Server). El volumen se usa para la copia de seguridad inicial. En esta configuración, el Administrador de discos lógicos (LDM) limita la protección de DPM a 300 orígenes de datos (bases de datos de SQL Server). Para evitar esta limitación, seleccione **Colocar datos en bloque de almacenamiento de DPM**. Si utiliza esta opción, DPM usa un único volumen para varios orígenes de datos. Esta configuración permite a DPM proteger hasta 2 000 bases de datos de SQL Server.

    Si selecciona **Expandir automáticamente los volúmenes**, DPM puede tener en cuenta el aumento del volumen de copia de seguridad a medida que crecen los datos de producción. Si no selecciona **Expandir automáticamente los volúmenes**, DPM limita el almacenamiento de copia de seguridad usado para los orígenes de datos del grupo de protección.

1. Si es un administrador, puede optar por transferir esta copia de seguridad inicial **Automáticamente a través de la red** y elegir la hora de transferencia. O bien, elija transferir **manualmente** la copia de seguridad. Luego, seleccione **Siguiente**.

    ![Elección de un método de creación de réplicas](./media/backup-azure-backup-sql/pg-manual.png)

    La copia de seguridad inicial requiere la transferencia de todo el origen de datos (base de datos de SQL Server). Los datos de copia de seguridad se mueven desde el servidor de producción (máquina de SQL Server) al servidor de DPM. Si esta copia de seguridad es grande, la transferencia de los datos por la red podría provocar la congestión del ancho de banda. Por esta razón, los administradores pueden optar por usar medios extraíbles para transferir la copia de seguridad inicial **manualmente**. O bien, pueden transferir los datos **Automáticamente a través de la red** a una hora especificada.

    Una vez finalizada la copia de seguridad inicial, las copias de seguridad continuarán de forma incremental sobre la copia de seguridad inicial. Las copias de seguridad incrementales tienden a ser pequeñas y se transfieren fácilmente a través de la red.
    
1. Elija cuándo desea ejecutar una comprobación de coherencia. Luego, seleccione **Siguiente**.

    ![Elección de cuándo ejecutar una comprobación de coherencia](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM puede ejecutar una comprobación de coherencia sobre la integridad del punto de copia de seguridad. Calcula la suma de comprobación del archivo de copia de seguridad en el servidor de producción (máquina de SQL Server en este ejemplo) y los datos de copia de seguridad para ese archivo en DPM. Si la comprobación encuentra un conflicto, se supone que el archivo de copia de seguridad de DPM está dañado. DPM corrige los datos de copia de seguridad mediante el envío de los bloques correspondientes a la suma de comprobación no coincidente. Como la comprobación de coherencia es una operación que requiere un rendimiento intensivo, los administradores tienen la opción de programarla o ejecutarla automáticamente.

1. Seleccione los orígenes de datos que se van a proteger en Azure. Luego, seleccione **Siguiente**.

    ![Selección de los orígenes de datos que se van a proteger en Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Si es un administrador, puede elegir las programaciones de copia de seguridad y las directivas de retención que se ajusten a las directivas de la organización.

    ![Elección de programaciones y directivas de retención](./media/backup-azure-backup-sql/pg-schedule.png)

    En este ejemplo, las copias de seguridad se realizan diariamente a las 12:00 P.M. y las 8:00 P.M.

    > [!TIP]
    > Para una recuperación rápida, mantenga algunos puntos de recuperación a corto plazo en el disco. Estos puntos de recuperación se usan para la recuperación operacional. Azure actúa como una ubicación válida fuera de sitio y proporciona contratos de nivel de servicio mayores y una disponibilidad garantizada.
    >
    > Use DPM para programar copias de seguridad de Azure una vez finalizadas las copias de seguridad del disco local. Al seguir este procedimiento, la última copia de seguridad del disco se copia en Azure.
    >

1. Seleccione la programación de la directiva de retención. Para más información sobre el funcionamiento de la directiva de retención, consulte [Uso de Azure Backup para reemplazar la infraestructura de cintas](backup-azure-backup-cloud-as-tape.md).

    ![Elección de una directiva de retención](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    En este ejemplo:

    * Las copias de seguridad se realizan diariamente a las 12:00 P.M. y las 8:00 P.M. Se conservan durante 180 días.
    * La copia de seguridad del sábado a las 12:00 P.M. se mantiene durante 104 semanas.
    * La copia de seguridad del último sábado del mes a las 12:00 P.M. se conserva durante 60 meses.
    * La copia de seguridad del último sábado de marzo a las 12:00 P.M. se conserva durante 10 años.
    
    Después de elegir una directiva de retención, seleccione **Siguiente**.

1. Elija cómo transferir la copia de seguridad inicial a Azure.

    * La opción **Automáticamente a través de la red** sigue su programación de copia de seguridad para transferir los datos a Azure.
    * Para más información sobre la **copia de seguridad sin conexión**, consulte [Introducción a la copia de seguridad sin conexión](offline-backup-overview.md).

    Después de elegir un mecanismo de transferencia, seleccione **Siguiente**.

1. En la página **Resumen**, revise los detalles de la directiva. A continuación, seleccione **Crear grupo**. Puede seleccionar **Cerrar** y supervisar el progreso del trabajo en el área de trabajo **Supervisión**.

    ![Progreso de la creación del grupo de protección](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Creación de copias de seguridad a petición de una base de datos de SQL Server

Cuando se realiza la primera copia de seguridad, se crea un punto de recuperación. En lugar de esperar a que se ejecute la programación, puede desencadenar manualmente la creación de un punto de recuperación:

1. En el grupo de protección, asegúrese de que el estado de la base de datos es **Correcto**.

    ![Grupo de protección que muestra el estado de la base de datos](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Haga clic con el botón derecho en la base de datos y, a continuación, seleccione **Crear punto de recuperación**.

    ![Elección de la creación de un punto de recuperación en línea](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. En el menú desplegable, seleccione **Protección en línea**. A continuación, seleccione **Aceptar** para iniciar la creación de un punto de recuperación en Azure.

    ![Inicio de la creación de un punto de recuperación en Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Puede ver el progreso del trabajo en el área de trabajo **Supervisión**.

    ![Visualización del progreso del trabajo en la consola Supervisión](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperación de una base de datos SQL Server de Azure

Para recuperar una entidad protegida, como una base de datos de SQL Server, desde Azure:

1. Abra la consola de administración del servidor de DPM. Vaya al área de trabajo **Recuperación** para ver los servidores de los que DPM realiza una copia de seguridad. Seleccione la base de datos (en este ejemplo, ReportServer$MSDPM2012). Seleccione una **Hora de recuperación** que finalice con **En línea**.

    ![Seleccionar un punto de recuperación](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Haga clic con el botón derecho en el nombre de la base de datos y seleccione **Recuperar**.

    ![Recuperación de una base de datos desde Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM muestra los detalles del punto de recuperación. Seleccione **Next** (Siguiente). Para sobrescribir la base de datos, seleccione el tipo de recuperación **Recuperar en instancia original de servidor SQL Server**. Luego, seleccione **Siguiente**.

    ![Recuperación de una base de datos en su ubicación original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    En este ejemplo, DPM permite la recuperación de la base de datos en otra instancia de SQL Server o en una carpeta de red independiente.
1. En la página **Especificar opciones de recuperación**, puede seleccionar las opciones de recuperación. Por ejemplo, puede elegir **Limitación del uso del ancho de banda de red** para limitar el ancho de banda que utiliza la recuperación. Luego, seleccione **Siguiente**.
1. En la página **Resumen**, verá la configuración de recuperación actual. Seleccione **Recuperar**.

    El estado de recuperación muestra que la base de datos se está recuperando. Puede seleccionar **Cerrar** para cerrar el asistente y ver el progreso en el área de trabajo **Supervisión**.

    ![Inicio del proceso de recuperación](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Una vez completada la recuperación, la base de datos restaurada será coherente con la aplicación.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Preguntas más frecuentes sobre Azure Backup](backup-azure-backup-faq.md).
