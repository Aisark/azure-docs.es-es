---
title: 'Preguntas frecuentes: copia de seguridad de bases de datos de SAP HANA en máquinas virtuales de Azure'
description: En este artículo, descubra las respuestas a preguntas comunes sobre la copia de seguridad de bases de datos de SAP HANA con el servicio Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919692"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Preguntas frecuentes: copia de seguridad de bases de datos de SAP HANA en máquinas virtuales de Azure

Este artículo responde preguntas comunes sobre la copia de seguridad de bases de datos de SAP HANA con el servicio Azure Backup.

## <a name="backup"></a>Copia de seguridad

### <a name="how-many-full-backups-are-supported-per-day"></a>¿Cuántas copias de seguridad completas se admiten al día?

Solo se admite una copia de seguridad completa al día. No se puede desencadenar una copia de seguridad diferencial y una copia de seguridad completa en el mismo día.

### <a name="do-successful-backup-jobs-create-alerts"></a>¿Generan alertas los trabajos de copia de seguridad que se han realizado correctamente?

No. Los trabajos de copia de seguridad correctos no generan alertas. Las alertas se envían únicamente para los trabajos de copia de seguridad con errores. El comportamiento detallado para alertas del portal está documentado [aquí](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Sin embargo, si está interesado en recibir alertas incluso para trabajos realizados correctamente, puede usar [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>¿Se pueden ver los trabajos de copia de seguridad programados en el menú de Trabajos de copia de seguridad?

El menú Trabajo de copia de seguridad solo muestra los trabajos de copia de seguridad ad hoc. Para los trabajos programados, use [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>¿Las bases de datos futuras se agregan automáticamente para copia de seguridad?

No, actualmente no se admite.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Si elimino una base de datos de una instancia, ¿qué ocurrirá con las copias de seguridad?

Si se elimina una base de datos de una instancia de SAP HANA, se siguen intentando las copias de seguridad de la base de datos. Esto implica que la base de datos eliminada comienza a aparecer como incorrecta en **Elementos de copia de seguridad** y todavía está protegida.
La manera correcta de detener la protección de esta base de datos es realizar una acción **Detener copia de seguridad con eliminación de datos** en esta base de datos.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Si se cambia el nombre de la base de datos después de haberla protegido, ¿cuál será el comportamiento?

Una base de datos después de un cambio de nombre se trata como una nueva base de datos. Por lo tanto, el servicio trata esta situación como si no se encontrara la base de datos y producirá un error en las copias de seguridad. La base de datos cuyo nombre se ha cambiado aparecerá como una nueva base de datos y se debe configurar su protección.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>¿Cuáles son los requisitos previos para realizar copias de seguridad de bases de datos de SAP HANA en una máquina virtual de Azure?

Consulte las secciones relativas a los [requisitos previos](tutorial-backup-sap-hana-db.md#prerequisites) y [Qué hace el script de registro previo](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>¿Qué permisos se deben establecer para que Azure pueda realizar copias de seguridad de bases de datos de SAP HANA?

Al ejecutar el script de registro previo, se establecen los permisos necesarios para permitir que Azure realice copias de seguridad de bases de datos de SAP HANA. Puede encontrar más información sobre el script de registro previo [aquí](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>¿Funcionarán las copias de seguridad después de migrar SAP HANA de la versión 1.0 a la 2.0?

Consulte [esta sección](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) de la guía de solución de problemas.

## <a name="restore"></a>Restauración

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>¿Por qué no puedo ver el sistema HANA en el que deseo que se restaure la base de datos?

Compruebe si se cumplen todos los requisitos previos para la restauración en la instancia de SAP HANA de destino. Para más información, consulte [Requisitos previos: restauración de bases de datos de SAP HANA en máquinas virtuales de Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>¿Por qué se produce un error en la restauración de la base de datos con sobrescritura para la base de datos?

Asegúrese de que la opción **Forzar sobrescritura** está seleccionada durante la restauración.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>¿Por qué veo el mensaje de error "Los sistemas de origen y destino para la restauración no son compatibles"?

Consulte la Nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) de SAP HANA para ver qué tipos de restauración se admiten actualmente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [hacer copias de seguridad de bases de datos de SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) que se ejecutan en máquinas virtuales de Azure.
