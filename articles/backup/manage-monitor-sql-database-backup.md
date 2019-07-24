---
title: Administración y supervisión de bases de datos de SQL Server en una máquina virtual de Azure con copia de seguridad de Azure Backup | Microsoft Docs
description: En este artículo se describe cómo administrar y supervisar las bases de datos de SQL Server que se ejecutan en una máquina virtual de Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: 913140a51603429e003f04f860bca9b4ddb1c214
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704877"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Administración y supervisión de bases de datos SQL Server con copia de seguridad

En este artículo se describen las tareas comunes para administrar y supervisar bases de datos de SQL Server que se ejecutan en una máquina virtual de Azure cuya copia de seguridad se ha realizado en un almacén de Azure Backup Recovery Services con el servicio [Azure Backup](backup-overview.md). Aprenderá cómo supervisar los trabajos y las alertas, detener y reanudar la protección de la base de datos, ejecutar trabajos de copia de seguridad y anular el registro de una máquina virtual de las copias de seguridad.

Si todavía no ha configurado las copias de seguridad para las bases de datos de SQL Server, consulte [Copia de seguridad de bases de datos de SQL Server en máquinas virtuales de Azure](backup-azure-sql-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Supervisión de trabajos de copia de seguridad manuales en el portal

Azure Backup muestra todos los trabajos desencadenados manualmente en el portal **Trabajos de copia de seguridad**. Los trabajos que vea en este portal incluyen operaciones de detección y registro de base de datos, y copia de seguridad y restauración.

![Portal Trabajos de copia de seguridad](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Los trabajos de copia de seguridad programados no se muestran en el portal **Trabajos de copia de seguridad**. Use SQL Server Management Studio para supervisar los trabajos de copia de seguridad programados, como se describe en la sección siguiente.
>

Para más información sobre los escenarios de supervisión, consulte [Supervisión en Azure Portal](backup-azure-monitoring-built-in-monitor.md) y [Supervisión con Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Visualización de alertas de copia de seguridad

Dado que las copias de seguridad de registros se producen con una frecuencia de 15 minutos, la supervisión de los trabajos de copia de seguridad puede resultar tediosa. Azure Backup facilita la supervisión mediante el envío de alertas por correo electrónico. Las alertas por correo electrónico:

- Se desencadenan para todos los errores de copia de seguridad.
- Se consolidan en el nivel de base de datos por código de error.
- Se envían solo para el primer error de copia de seguridad de una base de datos.

Para supervisar las alertas de copia de seguridad de bases de datos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. En el panel del almacén, seleccione **Alertas y eventos**.

   ![Seleccionar Alertas y eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. En **Alertas y eventos**, seleccione **Alertas de copia de seguridad**.

   ![Seleccionar alertas de copia de seguridad](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Detención de la protección de una base de datos de SQL Server

Puede detener la copia de seguridad de una base de datos SQL Server de las dos formas siguientes:

* Detener todos los trabajos futuros de copia de seguridad y eliminar todos los puntos de recuperación.
* Detener todos los trabajos futuros de copia de seguridad y dejar intactos los puntos de recuperación.

Si decide dejar los puntos de recuperación, tenga en cuenta estos detalles:

* Todos los puntos de recuperación permanecerán intactos para siempre; al detenerse la protección de los datos se detendrá la eliminación de todos los puntos y se conservarán los datos.
* Se le cobrará la instancia protegida y el almacenamiento consumido. Para más información, consulte [Precios de Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Si elimina un origen de datos sin detener las copias de seguridad, las nuevas copias de seguridad producirán errores.

Para detener la protección de una base de datos:

1. En el panel del almacén, seleccione **Elementos de copia de seguridad**.

2. En **Tipo de administración de copias de seguridad**, seleccione **SQL en Azure VM**.

    ![Seleccionar SQL en la máquina virtual de Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Seleccione la base de datos para la que desea detener la protección.

    ![Seleccionar la base de datos en la que detener la protección](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. En el menú de la base de datos, seleccione **Detener copia de seguridad**.

    ![Seleccionar Detener copia de seguridad](./media/backup-azure-sql-database/stop-db-button.png)


5. En el menú **Detener copia de seguridad**, seleccione si desea conservar o eliminar los datos. También puede proporcionar una razón y un comentario.

    ![Conservación o eliminación de datos en el menú Detener copia de seguridad](./media/backup-azure-sql-database/stop-backup-button.png)

6. Seleccione **Detener copia de seguridad**.

>
> [!NOTE]
Consulte las siguientes preguntas frecuentes para obtener más información sobre la opción de eliminación de datos: <br/>
* [Si elimino una base de datos de una instancia protegida automáticamente, ¿qué ocurrirá con las copias de seguridad?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)<br/>
* [Si detengo la operación de copia de seguridad de una base de datos protegida automáticamente, ¿cuál será su comportamiento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Reanudación de la protección en una base de datos SQL

Si seleccionó la opción **Retener datos de copia de seguridad** al detener la protección de la base de datos SQL, se puede reanudar la protección más adelante. Si no retiene los datos de copia de seguridad, no puede reanudar la protección.

Para reanudar la protección en una base de datos SQL

1. Abra el elemento de copia de seguridad y seleccione **Reanudar copia de seguridad**.

    ![Seleccionar Reanudar copia de seguridad para reanudar la protección de la base de datos](./media/backup-azure-sql-database/resume-backup-button.png)

2. En el menú **Directiva de copia de seguridad**, seleccione una directiva y, después, seleccione **Guardar**.

## <a name="run-an-on-demand-backup"></a>Ejecución de una copia de seguridad a petición

Puede ejecutar diferentes tipos de copias de seguridad a petición:

* Copia de seguridad completa
* Copiar solo copia de seguridad completa
* Copia de seguridad diferencial
* Copia de seguridad de registro

Aunque puede que tenga que especificar la duración de la retención de la copia de seguridad completa de solo copia, la duración de la retención para otros tipos de copia de seguridad se establece automáticamente en 30 días desde el momento actual. <br/>
Para más información, vea [Tipos de copia de seguridad en SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Anulación del registro de una instancia de SQL Server

Anule el registro de una instancia de SQL Server después de deshabilitar la protección, pero antes de eliminar el almacén:

1. En el panel del almacén, en **Administrar**, seleccione **Infraestructura de Backup**.  

   ![Seleccionar Infraestructura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. En **Servidores de administración**, seleccione **Servidores protegidos**.

   ![Seleccionar servidores protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. En **Servidores protegidos**, seleccione el servidor del que desea anular el registro. Para eliminar el almacén, debe anular el registro de todos los servidores.

4. Haga clic con el botón derecho en el servidor protegido y seleccione **Anular registro**.

   ![Seleccionar Eliminar](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Volver a registrar la extensión en la máquina virtual de SQL Server

A veces, la extensión de la carga de trabajo en la máquina virtual puede verse afectada por diversas razones. En tales casos, todas las operaciones que se desencadenen en la máquina virtual comenzarán a generar errores. Quizás tenga que volver a registrar la extensión en la máquina virtual. La operación **Volver a registrar** vuelve a instalar la extensión de copia de seguridad de cargas de trabajo en la máquina virtual para que las operaciones puedan continuar.  <br>

Se recomienda usar esta opción con precaución; cuando se desencadena en una máquina virtual que ya tiene una extensión correcta, esta operación hará que la extensión se reinicie. Esto puede dar lugar a errores en todos los trabajos en curso. Compruebe uno o varios de los [síntomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de desencadenar la operación de repetición del registro.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Solución de problemas de copias de seguridad de bases de datos de SQL Server](backup-sql-server-azure-troubleshoot.md).
