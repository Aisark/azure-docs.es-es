---
title: Migración de datos a Azure File Sync mediante Azure Data Box y otros métodos
description: Migración masiva de datos con un procedimiento compatible con Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9264aa6d24256b991abefe35b41045caa2e76d67
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997784"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migración masiva de datos a Azure File Sync
Puede migrar datos de forma masiva a Azure File Sync de dos maneras:

* **Cargue los archivos mediante Azure File Sync.** Este es el método más sencillo. Mueva los archivos localmente a un servidor de Windows Server 2012 R2 o posterior e instale el agente de Azure File Sync. Después de configurar la sincronización, se cargarán los archivos desde el servidor. (Actualmente, nuestros clientes obtienen una velocidad media de carga de 1 TiB cada dos días). Para asegurarse de que el servidor no usa mucho ancho de banda del centro de datos, puede que desee configurar una [programación de limitación de ancho de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfiera los archivos sin conexión.** Si no tiene suficiente ancho de banda, es posible que no pueda cargar archivos en Azure en un período de tiempo razonable. La dificultad en este caso es la sincronización inicial de todo el conjunto de archivos. Para superar esta dificultad, use herramientas de migración masiva sin conexión, como la [familia de herramientas de Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

En este artículo se explica cómo migrar archivos sin conexión de una forma que es compatible con Azure File Sync. Siga estas instrucciones para evitar conflictos de archivos y conservar sus listas de control de acceso (ACL) a archivos y carpetas, y las marcas de tiempo después de habilitar la sincronización.

## <a name="migration-tools"></a>Herramientas de migración
El proceso que se describe en este artículo funciona no solo con Data Box sino también con otras herramientas de migración sin conexión. También sirve para herramientas como AzCopy, Robocopy o herramientas y servicios de asociados que funcionan directamente a través de Internet. Independientemente de cómo se solucione la dificultad inicial, siga los pasos de este artículo para usar estas herramientas de una forma que sea compatible con Azure File Sync.

En algunos casos, necesitará pasar de un servidor de Windows a otro antes de adoptar Azure File Sync. El [Servicio de migración de almacenamiento](https://aka.ms/storagemigrationservice) (SMS) puede ayudar en eso. Si necesita migrar a una versión de sistema operativo del servidor compatible con Azure File Sync (Windows Server 2012R2 y versiones posteriores) o simplemente tiene que migrar porque está comprando un nuevo sistema para Azure File Sync, SMS tiene numerosas características y ventajas que lo ayudarán a tener realizar su migración sin ningún problema.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Ventajas de usar una herramienta de transferencia de datos sin conexión
Estas son las principales ventajas de usar una herramienta de transferencia como Data Box para la migración sin conexión:

- No necesita cargar todos los archivos en la red. En los espacios de nombres de gran tamaño, esta herramienta podría suponer un ahorro considerable de tiempo y ancho de banda de red.
- Cuando se usa Azure File Sync, independientemente de qué herramienta de transferencia utilice (Data Box, el servicio Azure Import/Export, etc.), el servidor activo carga solo los archivos que cambian después de mover los datos a Azure.
- Azure File Sync sincroniza las ACL de archivos y carpetas, incluso si la herramienta de migración masiva sin conexión no transporta ACL.
- Data Box y Azure File Sync no requieren ningún tiempo de inactividad. Cuando usa Data Box para transferir datos a Azure, puede usar el ancho de banda de red eficazmente y conservar la fidelidad de los archivos. También se mantiene el espacio de nombres actualizado cargando solo los archivos que han cambiado desde que se movieron los datos a Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Requisitos previos de la transferencia de datos sin conexión
No se debe habilitar la sincronización en el servidor al que se va a migrar antes de completar la transferencia de datos sin conexión. Otras cosas que debe tener en cuenta antes de comenzar son las siguientes:

- Si planea usar Data Box para la migración masiva, siga estos pasos: Revise [los requisitos previos de implementación de Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planee la topología de Azure File Sync final: [Planeamiento de una implementación de Azure Files Sync](storage-sync-files-planning.md)
- Seleccione las cuentas de almacenamiento de Azure que contendrán los recursos compartidos de archivos con los que desea sincronizarlas. Asegúrese de que la migración masiva a recursos compartidos de almacenamiento provisionales temporales se realiza en las mismas cuentas de almacenamiento. La migración masiva solo puede habilitarse mediante un recurso compartido final de almacenamiento provisional que resida en la misma cuenta de almacenamiento.
- Solo se puede usar una migración masiva cuando se crea una nueva relación de sincronización con una ubicación de servidor. No se puede habilitar una migración masiva con una relación de sincronización existente.


## <a name="process-for-offline-data-transfer"></a>Proceso de transferencia de datos sin conexión
Aquí se indica cómo configurar Azure File Sync de una forma compatible con las herramientas de migración masiva, como Azure Data Box:

![Diagrama que muestra cómo configurar Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Paso | Detalles |
|---|---------------------------------------------------------------------------------------|
| ![Paso 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Pida su Data Box](../../databox/data-box-deploy-ordered.md). La familia de herramientas de Data Box ofrece [varios productos](https://azure.microsoft.com/services/storage/databox/data) para satisfacer sus necesidades. Cuando reciba su Data Box, siga la [documentación para copiar los datos](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) en esta ruta de acceso UNC de Data Box: *\\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>* . Aquí, *ShareName* es el nombre del recurso compartido de almacenamiento provisional. Envíe el dispositivo Data Box a Azure. |
| ![Paso 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Espere hasta que los archivos se muestren en los recursos compartidos de archivos de Azure que haya elegido como recursos compartidos de almacenamiento provisional temporal. *No habilite la sincronización con estos recursos compartidos.* |
| ![Paso 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Cree un recurso compartido vacío para cada recurso compartido de archivos que Data Box creó automáticamente. Este nuevo recurso compartido debe estar en la misma cuenta de almacenamiento que el recurso compartido de Data Box. [Creación de un recurso compartido de archivos de Azure](storage-how-to-create-file-share.md). |
| ![Paso 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Cree un grupo de sincronización](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) en un servicio de sincronización de almacenamiento. Haga referencia al recurso compartido vacío como un punto de conexión en la nube. Repita este paso para cada recurso compartido de archivos de Data Box. [Configuración de Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Paso 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Agregue el directorio de servidor activo como un punto de conexión de servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Durante el proceso, especifique que ya ha movido los archivos a Azure y haga referencia a los recursos compartidos del almacenamiento provisional. Puede habilitar o deshabilitar la nube por niveles según sea necesario. Al crear un punto de conexión de servidor en el servidor activo, haga referencia al recurso compartido de almacenamiento provisional. En la hoja **Agregar punto de conexión del servidor**, en **Transferencia de datos sin conexión**, seleccione **Habilitada** y, a continuación, seleccione el recurso compartido de almacenamiento provisional que deberá estar en la misma cuenta de almacenamiento que el punto de conexión en la nube. En este caso, la lista de recursos compartidos disponibles se filtra por cuenta de almacenamiento y recursos compartidos que ya no se están sincronizando. |

![Captura de pantalla de la interfaz de usuario de Azure Portal que muestra cómo habilitar la transferencia de datos sin conexión durante la creación de un nuevo punto de conexión del servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronización del recurso compartido
Después de crear el punto de conexión del servidor, se iniciará la sincronización. El proceso de sincronización determina si cada archivo del servidor también existe en el recurso compartido de almacenamiento provisional en el que Data Box depositó los archivos. Si el archivo existe, el proceso de sincronización copia el archivo del recurso compartido de almacenamiento provisional en lugar de cargarlo desde el servidor. Si el archivo no está en el recurso compartido de almacenamiento provisional o si hay una versión más reciente en el servidor local, el proceso de sincronización cargará el archivo desde el servidor local.

> [!IMPORTANT]
> Solo se puede habilitar el modo de migración masiva durante la creación de un punto de conexión de servidor. Una vez que se ha establecido un punto de conexión de servidor, no se pueden integrar los datos migrados de forma masiva desde un servidor que ya está sincronizándose en el espacio de nombres.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL y marcas de tiempo en archivos y carpetas
Azure File Sync se asegura de que las ACL de archivos y carpetas se sincronizan desde el servidor activo, aunque la herramienta de migración masiva que se usara no transportara ACL inicialmente. Por este motivo, el recurso compartido de almacenamiento provisional no tiene que contener las ACL de archivos y carpetas. Al habilitar la característica de migración de datos sin conexión cuando crea un punto de conexión de servidor, todas las ACL se sincronizan en el servidor. Las marcas de tiempo recién creadas y modificadas también se sincronizan.

## <a name="shape-of-the-namespace"></a>Forma del espacio de nombres
Cuando habilita la sincronización, el contenido del servidor determina la forma del espacio de nombres. Si se eliminan archivos del servidor local después de que finalicen los procesos de migración y creación de instantáneas de Data Box, estos archivos no se moverán al espacio de nombres activo que se está sincronizando. Permanecerán en el recurso compartido de almacenamiento provisional, pero no se copiarán. Esto es necesario, ya que la sincronización conserva el espacio de nombres según el servidor activo. La *instantánea* de Data Box es simplemente un espacio de almacenamiento provisional para copiar archivos de forma eficaz. No es la autoridad de la forma del espacio de nombres activo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpieza después de una migración masiva 
Después de que el servidor complete la sincronización inicial del espacio de nombres, los archivos migrados de forma masiva de Data Box utilizarán el recurso compartido de almacenamiento provisional. En la hoja **Propiedades del punto de conexión de servidor** de Azure Portal, en la sección **Transferencia de datos sin conexión**, el estado cambia de **En curso** a **Completado**. 

![Captura de pantalla de la hoja Propiedades del punto de conexión de servidor donde se encuentran los controles de estado y deshabilitación para la transferencia de datos sin conexión](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Ahora puede limpiar el recurso compartido de almacenamiento provisional para ahorrar costos:

1. En la hoja **Propiedades del punto de conexión de servidor**, cuando el estado sea **Completado**, seleccione **Deshabilitar la transferencia de datos sin conexión**.
2. Considere la posibilidad de eliminar el recurso compartido de almacenamiento provisional para ahorrar costos. El recurso compartido de almacenamiento provisional probablemente no contiene las ACL de archivos y carpetas, por lo que no es muy útil. Con fines de realizar copias de seguridad a un momento dado, cree una [instantánea real del recurso compartido de archivos de Azure que se está sincronizando](storage-snapshots-files.md). También puede [configurar Azure Backup para realizar instantáneas]( ../../backup/backup-azure-files.md) según una programación.

Deshabilite el modo de transferencia de datos sin conexión solo si el estado es **Completado** o si desea cancelar debido a una configuración incorrecta. Si deshabilita el modo durante una implementación, los archivos comenzarán a cargarse desde el servidor, aunque el recurso compartido de almacenamiento provisional siga estando disponible.

> [!IMPORTANT]
> Después de deshabilitar la transferencia de datos sin conexión no puede habilitarla de nuevo, incluso si el recurso compartido de almacenamiento provisional de la migración masiva sigue estando disponible.

## <a name="next-steps"></a>Pasos siguientes
- [Planeamiento de una implementación de Azure Files Sync](storage-sync-files-planning.md)
- [Implementación de Azure File Sync](storage-sync-files-deployment-guide.md)
