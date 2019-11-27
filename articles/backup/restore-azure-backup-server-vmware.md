---
title: Restauración de máquinas virtuales de VMware con Azure Backup Server
description: Use Azure Backup Server (MABS) para restaurar máquinas virtuales de VMware que se ejecutan en un servidor de VMWare vCenter y ESXi.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: dacurwin
ms.openlocfilehash: 909274fc9b30d5d6a4caf8d2019bbcecddf2a3c7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073954"
---
# <a name="restore-vmware-virtual-machines"></a>Restauración de máquinas virtuales de VMware

En este artículo se explica cómo usar Microsoft Azure Backup Server (MABS) para restaurar puntos de recuperación de máquinas virtuales de VMware. Para obtener información general sobre el uso de MABS para recuperar datos, vea [Recuperación de datos protegidos](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). En la Consola de administrador de MABS se pueden hallar datos recuperables de dos formas: buscando o examinando. Al recuperar datos, puede que quiera (o no) restaurar los datos o una máquina virtual en la misma ubicación. Por este motivo, MABS admite tres opciones de recuperación en las copias de seguridad de máquinas virtuales de VMware:

* **Recuperación de ubicación original (OLR)** : use OLR para restaurar una máquina virtual protegida en la ubicación original correspondiente. Una máquina virtual se puede restaurar en su ubicación original únicamente si no se han agregado o eliminado discos desde que se hizo la copia de seguridad. Si se han agregado o eliminado discos, deberá usar otra forma de recuperación de ubicación.

* **Recuperación de ubicación alternativa (ALR)** : si la máquina virtual original falta o no quiere suponer una molestia en la máquina virtual original, recupere la máquina virtual en una ubicación alternativa. Para recuperar una máquina virtual en una ubicación alternativa, debe proporcionar la ubicación de un host ESXi, un grupo de recursos de servidor, una carpeta y la ruta de acceso y el almacén de información de almacenamiento. Para poder diferenciar la máquina virtual restaurada de la máquina virtual original, MABS anexa "-Recuperado" al nombre de la máquina virtual.

* **Recuperación de ubicación de archivo individual (ILR)** : si la máquina virtual protegida es una máquina virtual de Windows Server, se pueden recuperar archivos o carpetas individuales de la máquina virtual con la capacidad ILR de MABS. Para recuperar archivos individuales, vea el procedimiento correspondiente más adelante en este artículo.

## <a name="restore-a-recovery-point"></a>Restauración de un punto de recuperación

1. En la Consola de administrador de MABS, haga clic en la vista Recuperación.

2. Mediante el panel Examinar, busque o filtre para encontrar la máquina virtual que quiera recuperar. Una vez que seleccione una máquina virtual o una carpeta, el panel Puntos de recuperación para muestra los puntos de recuperación disponibles.

    ![Puntos de recuperación disponibles](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. En el campo **Puntos de recuperación para**, use el calendario y los menús desplegables para seleccionar una fecha en la que se tomó un punto de recuperación. Las fechas del calendario en negrita son las que tienen puntos de recuperación disponibles.

4. En la cinta de herramientas, haga clic en **Recuperar** para abrir el **Asistente de recuperación**.

    ![Asistente de recuperación, Revisar selección de recuperación](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Haga clic en **Siguiente** para ir a la pantalla **Especificar opciones de recuperación**.

6. En la pantalla **Especificar opciones recuperación**, haga clic en **Modificar** si desea habilitar el límite de ancho de banda de red. Para dejar el límite de red deshabilitado, haga clic en **Siguiente**. No hay más opciones disponibles en esta pantalla del asistente para las máquinas virtuales de VMware. Si decide modificar el límite de ancho de banda de red, seleccione **Habilitar el límite de uso del ancho de banda de la red** en el cuadro de diálogo Limitación para activarla. Una vez habilitada, configure las opciones **Configuración** y **Programación de trabajos**.

7. En la pantalla **Seleccionar tipo de recuperación**, elija si quiere realizar la recuperación en la instancia original o en una nueva ubicación y haga clic en **Siguiente**.

     * Si elige **Recuperar en instancia original**, no será necesario realizar más opciones en el asistente. Se utilizan los datos de la instancia original.

     * Si elige **Recuperar como máquina virtual en cualquier host**, indique en la pantalla **Especificar destino** la información que sea necesaria relativa al **host ESXi, al grupo de recursos, a la carpeta** y a la **ruta de acceso**.

      ![Seleccionar tipo de recuperación](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. En la pantalla**Resumen**, revise la configuración y haga clic en **Recuperar** para iniciar el proceso de recuperación. La pantalla **Estado de la recuperación** muestra el progreso de la operación de recuperación.

## <a name="restore-an-individual-file-from-a-vm"></a>Restauración de un archivo individual desde una máquina virtual

Puede restaurar archivos individuales desde un punto de recuperación de una máquina virtual protegida. Esta característica solo está disponible en máquinas virtuales de Windows Server. La restauración de archivos individuales es similar a la restauración de toda la máquina virtual, excepto por el hecho de que se examina el VMDK y se buscan los archivos que se quieren antes de iniciar el proceso de recuperación en sí. Para recuperar un archivo individual o seleccionar archivos de una máquina virtual de Windows Server:

1. En la Consola de administrador de MABS, haga clic en vista **Recuperación**.

2. Mediante el panel **Examinar**, busque o filtre para encontrar la máquina virtual que quiera recuperar. Una vez que seleccione una máquina virtual o una carpeta, el panel Puntos de recuperación para muestra los puntos de recuperación disponibles.

    ![Puntos de recuperación disponibles](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. En el panel **Puntos de recuperación para:** , use el calendario para seleccionar la fecha que contenga los puntos de recuperación que quiera. En función de cómo esté configurada la directiva de copia de seguridad, es posible que las fechas tengan más de un punto de recuperación. Una vez que haya seleccionado el día en que se tomó el punto de recuperación, asegúrese de elegir la **Hora de recuperación** adecuada. Si la fecha seleccionada tiene varios puntos de recuperación, elija el punto de recuperación seleccionándolo en el menú desplegable Hora de recuperación. Una vez elegido el punto de recuperación, la lista de elementos recuperables aparecerá en el panel **Ruta de acceso:** .

4. Para encontrar los archivos que quiere recuperar, en el panel **Ruta de acceso**, haga doble clic en el elemento en la columna **Elemento recuperable** para abrirlo. Seleccione el archivo, los archivos o las carpetas que quiera recuperar. Para seleccionar varios elementos, presione la tecla **Ctrl** mientras realiza la selección. Use el panel **Ruta de acceso** para buscar en la lista de archivos o carpetas que se muestra en la columna**Elemento recuperable**. La opción **Lista de búsqueda a continuación** no busca en las subcarpetas. Para buscar en las subcarpetas, haga doble clic en la carpeta. Use el botón **Subir** para pasar de una carpeta secundaria a la carpeta principal. Se pueden seleccionar varios elementos (archivos y carpetas), pero deben estar en la misma carpeta principal. No se pueden recuperar elementos de varias carpetas en el mismo trabajo de recuperación.

5. Una vez seleccionados los elementos que se van a recuperar, en la cinta de opciones de la Consola de administrador, haga clic en **Recuperar** para abrir el **Asistente de recuperación**. En el Asistente de recuperación, la pantalla **Revisar selección de recuperación** muestra los elementos seleccionados que se van a recuperar.
    ![Revisar selección de recuperación](./media/restore-azure-backup-server-vmware/review-recovery.png)

6. En la pantalla **Especificar opciones recuperación**, haga clic en **Modificar** si desea habilitar el límite de ancho de banda de red. Para dejar el límite de red deshabilitado, haga clic en **Siguiente**. No hay más opciones disponibles en esta pantalla del asistente para las máquinas virtuales de VMware. Si decide modificar el límite de ancho de banda de red, seleccione **Habilitar el límite de uso del ancho de banda de la red** en el cuadro de diálogo Limitación para activarla. Una vez habilitada, configure las opciones **Configuración** y **Programación de trabajos**.
7. En la pantalla **Seleccionar tipo de recuperación**, haga clic en **Siguiente**. Los archivos o las carpetas solo se pueden recuperar en una carpeta de red.
8. En la pantalla **Especificar destino**, haga clic en **Examinar** para buscar una ubicación de red para los archivos o carpetas. MABS crea una carpeta donde se copian todos los elementos recuperados. El nombre de la carpeta tiene el prefijo MABS_día-mes-año. Al seleccionar una ubicación para los archivos o carpetas recuperados, se facilitan los detalles de esa ubicación (como el destino, la ruta de acceso del destino y el espacio disponible).

       ![Specify location to recover files](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. En la pantalla **Especificar opciones de recuperación**, elija la configuración de seguridad que quiera aplicar. Puede optar por modificar el límite de uso del ancho de banda de red, pero este límite está deshabilitado de forma predeterminada. Las opciones **Recuperación de SAN** y **Notificación** tampoco están habilitadas.
10. En la pantalla **Resumen**, revise la configuración y haga clic en **Recuperar** para iniciar el proceso de recuperación. La pantalla **Estado de la recuperación** muestra el progreso de la operación de recuperación.

## <a name="next-steps"></a>Pasos siguientes

Para solucionar problemas al usar de Azure Backup Server, revise la [guía de solución de problemas de Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
