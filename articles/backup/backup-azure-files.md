---
title: Copia de seguridad y restauración de recursos compartidos de archivos de Azure
description: En este artículo se explican no solo cómo realizar una copia de seguridad de los recursos compartidos de archivos de Azure y cómo restaurarlos, sino también las tareas de administración.
author: dcurwin
ms.author: dacurwin
ms.date: 07/29/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 6f1a793432fa6403639726dbe4340bae6804fb9f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091088"
---
# <a name="back-up-and-restore-azure-file-shares"></a>Copia de seguridad y restauración de recursos compartidos de archivos de Azure

En este artículo se explica cómo usar Azure Portal para realizar una copia de seguridad de los [recursos compartidos de archivos de Azure](../storage/files/storage-files-introduction.md) y restaurarla.

En esta guía, aprenderá a:
> [!div class="checklist"]
>
> * Configurar un almacén de Recovery Services para realizar una copia de seguridad de archivos de Azure
> * Ejecutar un trabajo de copia de seguridad a petición para crear un punto de restauración
> * Restaurar archivos desde un punto de restauración
> * Administración de trabajos de copia de seguridad
> * Detener la protección en archivos de Azure
> * Eliminar los datos de copia de seguridad

## <a name="prerequisites"></a>Requisitos previos

Antes de realizar una copia de seguridad de un recurso compartido de archivos de Azure, asegúrese de que se encuentra en uno de los [tipos de cuenta de almacenamiento admitidos](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Cuando haya comprobado esto, puede proteger los recursos compartidos de archivos.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitaciones de la copia de seguridad de recursos compartidos de archivos de Azure en la versión preliminar

La copia de seguridad de los recursos compartidos de archivos de Azure está en versión preliminar. Los recursos compartidos de archivos de Azure se admiten en cuentas de almacenamiento de uso general v1 y uso general v2. No se admiten los siguientes escenarios de copia de seguridad para los recursos compartidos de archivos de Azure:

* La compatibilidad con la copia de seguridad de recursos compartidos de archivos de Azure en cuentas de almacenamiento con replicación de [almacenamiento con redundancia de zona](../storage/common/storage-redundancy-zrs.md) (ZRS) está limitada actualmente a [estas regiones](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares).
* Ni PowerShell ni la CLI están disponibles para proteger Azure Files mediante Azure Backup.
* Actualmente, Azure Backup admite la configuración de copias de seguridad programadas una vez al día para los recursos compartidos de archivos de Azure.
* El número máximo de copias de seguridad programadas al día es una.
* El número máximo de copias de seguridad a petición al día es cuatro.
* Use los [bloqueos de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) de la cuenta de almacenamiento para impedir la eliminación accidental de copias de seguridad del almacén de Recovery Services.
* No elimine las instantáneas que crea Azure Backup. La eliminación de instantáneas puede provocar la pérdida de puntos de recuperación o errores de restauración.
* No elimine los recursos compartidos de archivos que estén protegidos por Azure Backup. La solución actual eliminará todas las instantáneas realizadas por Azure Backup una vez que se elimina el recurso compartido de archivos y, por consiguiente, se pierden todos los puntos de restauración.

## <a name="configuring-backup-for-an-azure-file-share"></a>Configuración de la copia de seguridad de un recurso compartido de archivos de Azure

En este tutorial se da por supuesto que ya ha establecido un recurso compartido de archivos de Azure. Para realizar una copia de seguridad del recurso compartido de archivos de Azure, siga estos pasos:

1. Cree un almacén de Recovery Services en la misma región que el recurso compartido de archivos. Si ya tiene un almacén, abra la página de información general de su almacén y haga clic en **Backup** (Copia de seguridad).

    ![Elija Azure Fileshare como objetivo de copia de seguridad.](./media/backup-file-shares/overview-backup-page.png)

2. En el menú **Objetivo de Backup**, en **¿De qué quiere realizar una copia de seguridad?** , elija Recurso compartido de archivos de Azure.

    ![Elija Azure Fileshare como objetivo de copia de seguridad.](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Haga clic en **Backup** (Copia de seguridad) para configurar el recurso compartido de archivos de Azure en su almacén de Recovery Services.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/set-backup-goal.png)

    Cuando el almacén está asociado con el recurso compartido de archivos de Azure, se abre el menú Backup (Copia de seguridad) abre y se le pide que seleccione una cuenta de Storage. El menú muestra todas las cuentas de almacenamiento admitidas en la región donde existe el almacén que aún no están asociadas con un almacén de Recovery Services.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/list-of-storage-accounts.png)

4. En la lista de cuentas de almacenamiento, seleccione una cuenta y haga clic en **OK** (Aceptar). Azure busca en la cuenta de almacenamiento los recursos compartidos de archivos de los que se puede realizar una copia de seguridad. Si recientemente ha agregado sus recursos compartidos de archivos y no los ve en la lista, espere un poco a que aparezcan.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/discover-file-shares.png)

5. En la lista **File Shares** (Recursos compartidos de archivos), seleccione uno o varios recursos compartidos de archivos de los que quiera realizar una copia de seguridad y haga clic en **OK** (Aceptar).

6. Después de elegir los recursos compartidos de archivos, el menú de copia de seguridad cambia a la **directiva de copia de seguridad**. Desde este menú, seleccione una directiva de copia de seguridad existente o cree una nueva y, luego, haga clic en **Enable Backup** (Habilitar copia de seguridad).

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/apply-backup-policy.png)

    Después de establecer una directiva de copia de seguridad, se realiza una instantánea de los recursos compartidos de archivos a la hora programada y el punto de recuperación se conserva durante el período seleccionado.

## <a name="create-an-on-demand-backup"></a>Creación de una copia de seguridad a petición

En ocasiones, puede que quiera generar una instantánea de copia de seguridad, o un punto de recuperación, fuera de las horas programadas en la directiva de copia de seguridad. Un momento habitual para generar una copia de seguridad a petición es justo después de haber configurado la directiva de copia de seguridad. Según la programación de la directiva de copia de seguridad, pueden transcurrir horas y días hasta que se toma una instantánea. Para proteger los datos hasta que se aplique la directiva de copia de seguridad, inicie una copia de seguridad a petición. Con frecuencia, es necesario crear una copia de seguridad a petición antes de realizar los cambios planeados en los recursos compartidos de archivos.

### <a name="to-create-an-on-demand-backup"></a>Para crear una copia de seguridad a petición, siga estos pasos:

1. Abra el almacén de Recovery Services que contiene los puntos de recuperación del recurso compartido de archivos y haga clic en **Backup Items** (Elementos de copia de seguridad). Aparece la lista de tipos de elementos de copia de seguridad.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/list-of-backup-items.png)

2. En la lista, seleccione **Azure Storage (Azure Files)** . Aparece la lista de recursos compartidos de archivos de Azure.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. En la lista de recursos compartidos de archivos de Azure, seleccione el que desee. Aparecen los detalles de **Backup Item** (Elemento de copia de seguridad). En el menú **Backup Item** (Elemento de copia de seguridad), haga clic en **Backup Now** (Crear copia de seguridad ahora). Dado que se trata de un trabajo de copia de seguridad a petición, no hay ninguna directiva de retención asociada con el punto de recuperación.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/backup-item-menu.png)

4. Se abre el cuadro de diálogo **Backup Now** (Crear copia de seguridad ahora). Especifique el último día que quiere conservar el punto de recuperación.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/backup-now-menu.png)

5. Haga clic en **Ok** (Aceptar) para confirmar el trabajo de copia de seguridad a petición.

## <a name="restore-from-backup-of-azure-file-share"></a>Restauración a partir de la copia de seguridad de un recurso compartido de archivos de Azure

Si necesita restaurar un recurso compartido de archivos completo o archivos o carpetas individuales desde un punto de restauración, vaya al elemento de copia de seguridad como se ha explicado en la sección anterior. Elija **Restore Share** (Restaurar recurso compartido) para restaurar un recurso compartido de archivos entero desde un momento dado deseado. En la lista de puntos de restauración que se muestran, seleccione uno para poder sobrescribir el recurso compartido de archivos actual o restaurarlo a un recurso compartido de archivos alternativo de la misma región.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Restauración de archivos o carpetas individuales a partir de la copia de seguridad de recursos compartidos de archivos de Azure

Azure Backup ofrece la posibilidad de buscar un punto de restauración en Azure Portal. Para restaurar un archivo o una carpeta de su elección, haga clic en File Recovery (Recuperación de archivos) en la página de elementos de copia de seguridad y elija de la lista de puntos de restauración. Seleccione el destino de recuperación y, a continuación, haga clic en **Select File** (Seleccionar archivo) para buscar el punto de restauración. Seleccione el archivo o carpeta de su elección y elija **Restore** (Restaurar).

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Administración de copias de seguridad de recursos compartidos de archivos de Azure

Puede ejecutar varias tareas de administración sobre las copias de seguridad de recursos compartidos de archivos en la página **Backup Jobs** (Trabajos de copia de seguridad), por ejemplo:

* [Supervisión de trabajos](backup-azure-files.md#monitor-jobs)
* [Creación de una nueva directiva](backup-azure-files.md#create-a-new-policy)
* [Detención de la protección en un recurso compartido de archivos](backup-azure-files.md#stop-protecting-an-azure-file-share)
* [Reanudación de la protección en un recurso compartido de archivos](backup-azure-files.md#resume-protection-for-azure-file-share)
* [Eliminación de datos de copia de seguridad](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Supervisión de trabajos

Puede supervisar el progreso de todos los trabajos en la página **Backup Jobs** (Trabajos de copia de seguridad).

Para abrir la página **Backup Jobs** (Trabajos de copia de seguridad), siga estos pasos:

* Haga clic en el almacén de Recovery Services que quiere supervisar y, en su menú, haga clic en **Jobs** (Trabajos) y luego en **Backup Jobs** (Trabajos de copia de seguridad).

   ![Selección del trabajo para supervisar](./media/backup-file-shares/open-backup-jobs.png)

    Aparece la lista de trabajos de copia de seguridad y el estado de esos trabajos.

    ![Selección del trabajo para supervisar](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Creación de una nueva directiva

Puede crear una nueva directiva para realizar una copia de seguridad de los recursos compartidos de archivos de Azure desde **Backup Policies** (Directivas de copia de seguridad) del almacén de Recovery Services. Todas las directivas que se crean al configurar la copia de seguridad de los recursos compartidos de archivos se muestran con el tipo de directiva de recurso compartido de archivos de Azure.

Para ver las directivas de copia de seguridad existentes, siga estos pasos:

* Abra el almacén de Recovery Services que desee y, en su menú, haga clic en **Backup policies** (Directivas de copia de seguridad). Se muestran todas las directivas de copia de seguridad.

   ![Selección del trabajo para supervisar](./media/backup-file-shares/list-of-backup-policies.png)

Para crear una nueva directiva de copia de seguridad, siga estos pasos:

1. En el menú del almacén de Recovery Services, haga clic en **Backup policies** (Directivas de copia de seguridad).
2. En la lista de directivas de copia de seguridad, haga clic en **Add** (Agregar).

   ![Selección del trabajo para supervisar](./media/backup-file-shares/new-backup-policy.png)

3. En el menú **Add** (Agregar), seleccione **Azure File Share** (Recurso compartido de archivos de Azure). Se abre el menú de directivas de copia de seguridad del recurso compartido de archivos de Azure. Proporcione el nombre de la directiva, la frecuencia de copia de seguridad y el intervalo de retención para los puntos de recuperación. Cuando haya definido la directiva, haga clic en OK (Aceptar).

   ![Selección del trabajo para supervisar](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Anulación de la protección de un recurso compartido de archivos de Azure

Si elige dejar de proteger un recurso compartido de archivos de Azure, se le pregunta si desea conservar los puntos de recuperación. Hay dos maneras de dejar de proteger recursos compartidos de archivos de Azure:

* Detener todos los trabajos futuros de copia de seguridad y eliminar todos los puntos de recuperación.
* Detener todos los trabajos futuros de copia de seguridad pero dejar los puntos de recuperación.

Puede que dejar los puntos de recuperación en el almacenamiento conlleve un costo asociado, dado que las instantáneas subyacentes creadas por Azure Backup se conservan. Sin embargo, la ventaja de dejarlos es que puede restaurar el recurso compartido de archivos más adelante, si así lo desea. Para más información sobre del costo de dejar los puntos de recuperación, consulte la información sobre precios. Si opta por eliminar todos los puntos de recuperación, no podrá restaurar el recurso compartido de archivos.

Para dejar de proteger recursos compartidos de archivos de Azure, siga estos pasos:

1. Abra el almacén de Recovery Services que contiene los puntos de recuperación del recurso compartido de archivos y haga clic en **Backup Items** (Elementos de copia de seguridad). Aparece la lista de tipos de elementos de copia de seguridad.

   ![Haga clic en Backup (Copia de seguridad) para asociar el recurso compartido de archivos de Azure con el almacén](./media/backup-file-shares/list-of-backup-items.png)

2. En la lista **Backup Management Type** (Tipo de administración de copia de seguridad), seleccione **Azure Storage (Azure Files)** . Aparece la lista de elementos de copia de seguridad para (Azure Storage [Azure Files]).

   ![Hacer clic en un elemento para abrir el menú adicional](./media/backup-file-shares/azure-file-share-backup-items.png)

3. En la lista de elementos de copia de seguridad [Azure Storage (Azure Files)], seleccione el elemento de copia de seguridad que quiere detener.

4. En los elementos de recurso compartido de archivos de Azure, haga clic en el menú **More** (Más) y seleccione **Stop Backup** (Detener copia de seguridad).

   ![Hacer clic en un elemento para abrir el menú adicional](./media/backup-file-shares/stop-backup.png)

5. En el menú Stop Backup (Detener copia de seguridad), elija **Retain** (Retener) o **Delete Backup Data** (Eliminar datos de copia de seguridad) y haga clic en **Stop Backup** (Detener copia de seguridad).

   ![Hacer clic en un elemento para abrir el menú adicional](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Reanudación de la protección de un recurso compartido de archivos de Azure

Si eligió la opción Retener datos de copia de seguridad al detener la protección del recurso compartido de archivos, es posible reanudar la protección. Si eligió la opción **Eliminar datos de la copia de seguridad**, no se puede reanudar la protección del recurso compartido de archivos.

Para reanudar la protección del recurso compartido de archivos, vaya al elemento de copia de seguridad y haga clic en Resume Backup (Reanudar copia de seguridad). La directiva de copia de seguridad se abre y puede elegir una directiva de su elección para reanudar la copia de seguridad.

   ![Selección del trabajo para supervisar](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Eliminación de datos de copia de seguridad

Puede eliminar la copia de seguridad de un recurso compartido de archivos durante la detención del trabajo de copia de seguridad, o en cualquier momento después de haber detenido la protección. Incluso puede resultar útil esperar días o semanas antes de eliminar los puntos de recuperación. A diferencia de la restauración de puntos de recuperación, cuando se eliminan datos de copia de seguridad, no se pueden elegir los puntos de recuperación específicos que se van a eliminar. Si elige eliminar los datos de copia de seguridad, se eliminarán todos los puntos de recuperación asociados con el elemento.

En el siguiente procedimiento se da por sentado que se ha detenido el trabajo de copia de seguridad de la máquina virtual. Una vez que se detiene el trabajo de copia de seguridad, están disponibles las opciones Resume backup (Reanudar copia de seguridad) y Delete Backup Data (Eliminar datos de copia de seguridad) en el panel de elementos de copia de seguridad. Haga clic en Delete Backup Data (Eliminar datos de copia de seguridad) y escriba el nombre del recurso compartido de archivos para confirmar la eliminación. Opcionalmente, añada una razón para eliminar o un comentario.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los recursos compartidos de archivos de Azure, consulte

* [Preguntas frecuentes acerca de la copia de seguridad de recursos compartidos de archivos de Azure](backup-azure-files-faq.md)
* [Solución de problemas en la copia de seguridad de recursos compartidos de archivos de Azure](troubleshoot-azure-files.md)
