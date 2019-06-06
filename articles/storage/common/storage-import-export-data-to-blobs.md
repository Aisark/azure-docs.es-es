---
title: Uso de Azure Import/Export para transferir datos a blobs de Azure | Microsoft Docs
description: Aprenda a crear trabajos de importación y exportación en Azure Portal para transferir datos a los blobs de Azure y recibirlos de estos.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/31/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 68f62a6945f3b651781414e3194104b6d2e6295c
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455800"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Uso del servicio Azure Import/Export para importar datos de Azure Blob Storage

Este artículo proporciona instrucciones paso a paso sobre cómo usar el servicio Azure Import/Export para importar de forma segura grandes cantidades de datos a Azure Blob Storage. Para importar datos en los blobs de Azure, el servicio necesita que envíe las unidades de disco cifradas que contienen los datos a un centro de datos de Azure.  

## <a name="prerequisites"></a>Requisitos previos

Antes de crear un trabajo de importación para transferir datos a Azure Blob Storage, revise con cuidado y complete la siguiente lista de requisitos previos para este servicio. Debe:

- Tener una suscripción activa de Azure que pueda usarse para el servicio Import/Export.
- Tener por lo menos una cuenta de Azure Storage con un contenedor de almacenamiento. Consulte la lista de [las cuenta de almacenamiento y los tipos de almacenamiento admitidos para el servicio Import/Export](storage-import-export-requirements.md). 
    - Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-quickstart-create-account.md). 
    - Para información sobre contenedores de almacenamiento, vaya a [Creación de un contenedor de almacenamiento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Tener un número suficiente de discos de los [tipos admitidos](storage-import-export-requirements.md#supported-disks). 
- Tener un sistema de Windows que ejecute una [versión admitida del sistema operativo](storage-import-export-requirements.md#supported-operating-systems). 
- Habilitar BitLocker en el sistema de Windows. Consulte [Cómo habilitar BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Descargar la versión 1 de WAImportExport](https://aka.ms/waiev1) en el sistema de Windows. Descomprima en la carpeta predeterminada `waimportexportv1`. Por ejemplo, `C:\WaImportExportV1`.
- Tener una cuenta de FedEx o DHL. Si desea usar un operador distinto de FedEx o DHL, póngase en contacto con el equipo de operaciones de cuadro de datos de Azure en `adbops@microsoft.com`.  
    - La cuenta debe ser válida, debe tener saldo positivo y debe tener capacidades de devolución de envíos.
    - Generar un número de seguimiento del trabajo de exportación.
    - Cada trabajo debe tener un número de seguimiento independiente. No se admiten varios trabajos con el mismo número de seguimiento.
    - Si no tiene una cuenta de transportista, vaya a:
        - [Crear una cuenta de FedEX](https://www.fedex.com/en-us/create-account.html), o 
        - [Crear una cuenta de DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Paso 1: Preparación de las unidades de disco

Este paso genera un archivo de diario. El archivo de diario almacena información básica como el número de serie de unidad, la clave de cifrado y los detalles de la cuenta de almacenamiento. 

Realice los pasos siguientes para preparar las unidades de disco.

1.  Conecte las unidades de disco al sistema de Windows a través de los conectores SATA.
1.  Cree un volumen NTFS único en cada unidad. Asigne una letra de unidad al volumen. No utilice puntos de montaje.
2.  Habilite el cifrado de BitLocker en el volumen NTFS. Si usa un sistema de Windows Server, siga las instrucciones [How to enable BitLocker on Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Cómo habilitar BitLocker en Windows Server 2012 R2).
3.  Copie los datos en el volumen cifrado. Use arrastrar y soltar o Robocopy o cualquier herramienta de copia de este tipo.
4.  Abra una ventana de PowerShell o de línea de comandos con privilegios administrativos. Para cambiar el directorio a la carpeta descomprimida, ejecute el siguiente comando:
    
    `cd C:\WaImportExportV1`
5.  Para obtener la clave de BitLocker de la unidad, ejecute el siguiente comando:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Para preparar el disco, ejecute el siguiente comando. **Dependiendo del tamaño de los datos, esto puede tardar desde varias horas a días.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite /enablecontentmd5 
    ```
    Se crea un archivo de diario en la misma carpeta en la que se ejecutó la herramienta. También se crean otros dos archivos: un archivo *.xml* (la carpeta en la que se ejecuta la herramienta) y un archivo *drive-manifest.xml* (la carpeta en la que residen los datos).
    
    Los parámetros que se usan se describen en la tabla siguiente:

    |Opción  |DESCRIPCIÓN  |
    |---------|---------|
    |/j:     |nombre del archivo de diario, con la extensión .jrn. Se genera un archivo de diario por unidad. Se recomienda usar el número de serie del disco como nombre del archivo de diario.         |
    |/id:     |El identificador de sesión. Use un número de sesión único para cada instancia del comando.      |
    |/t:     |letra de unidad del disco que se va a enviar. Por ejemplo, unidad `D`.         |
    |/bk:     |clave de BitLocker de la unidad. Su contraseña numérica de salida de `manage-bde -protectors -get D:`      |
    |/srcdir:     |letra de unidad del disco que se va a enviar seguida de `:\`. Por ejemplo, `D:\`.         |
    |/dstdir:     |El nombre del contenedor de destino en Azure Storage.         |
    |/blobtype:     |Esta opción especifica el tipo de blobs que desea importar los datos. Los blobs en bloques, se trata de `BlockBlob` y para los blobs en páginas, es `PagaBlob`.         |
    |/skipwrite:     |Se debe preparar la opción que especifica que no es necesario copiar nuevos datos y que se deben preparar los datos existentes en el disco.          |
    |/enablecontentmd5:     |La opción cuando se habilita, se garantiza que MD5 se calcula durante la carga de blobs en bloques en Azure.          |
7. Repita el paso anterior para cada disco que vaya a enviar. Se crea un archivo de diario con el nombre proporcionad para cada ejecución de la línea de comandos.
    
    > [!IMPORTANT]
    > - Junto con el archivo de diario, se crea también un archivo `<Journal file name>_DriveInfo_<Drive serial ID>.xml` en la misma carpeta en la que reside la herramienta. Si al crear un trabajo el archivo de diario es demasiado grande se utiliza el archivo .xml en su lugar. 

## <a name="step-2-create-an-import-job"></a>Paso 2: Crear un trabajo de importación

Siga estos pasos para crear un trabajo de importación en Azure Portal.

1. Inicie sesión en https://portal.azure.com/.
2. Vaya a **Todos los servicios > Almacenamiento > Trabajos de importación o exportación**. 
    
    ![Ir a trabajos de importación o exportación](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Haga clic en **Crear el trabajo de importación o exportación**.

    ![Hacer clic en Crear el trabajo de importación o exportación](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. En **Aspectos básicos**:

   - Seleccione **Import into Azure** (Importar en Azure).
   - Escriba un nombre descriptivo para el trabajo de importación. Utilice el nombre para realizar un seguimiento del progreso de los trabajos.
       - El nombre solo pueden contener letras minúsculas, números y guiones.
       - El nombre tiene que empezar por una letra y no puede contener espacios.
   - Seleccione una suscripción.
   - Escriba o seleccione un grupo de recursos.  

     ![Creación del trabajo de importación: Paso 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. En **Detalles del trabajo**:

    - Cargue los archivos de diario de la unidad que haya obtenido durante el paso de preparación de la unidad. Si utilizó `waimportexport.exe version1`, cargue un archivo por cada unidad que haya preparado. Si el tamaño del archivo diario supera los 2 MB, puede usar el archivo `<Journal file name>_DriveInfo_<Drive serial ID>.xml` que se creó también junto con el archivo de diario. 
    - Seleccione la cuenta de almacenamiento de destino en la que residirán los datos. 
    - La ubicación de la entrega se rellena automáticamente según la región de la cuenta de almacenamiento seleccionada.
   
   ![Creación del trabajo de importación: Paso 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. En **Información de envío de devolución**:

   - Seleccione el transportista en la lista desplegable. Si desea usar un operador distinto de FedEx o DHL, elija una opción existente en la lista desplegable. Parte del equipo de operaciones de cuadro de datos de Azure contacto `adbops@microsoft.com` con la información relacionada con el transportista que se va a usar.
   - Escriba un número válido de cuenta de transportista que haya creado con ese transportista. Microsoft usa esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación. Si no tiene un número de cuenta, cree una cuenta de transportista [FedEx](https://www.fedex.com/us/oadr/) o [DHL](https://www.dhl.com/).
   - Proporcione información completa y válida del contacto: nombre, teléfono, correo electrónico, dirección postal, ciudad, código postal, estado o provincia y país o región. 
        
       > [!TIP] 
       > En lugar de especificar una dirección de correo electrónico para un solo usuario, proporcione un correo electrónico de grupo. Esto garantiza que recibirá notificaciones incluso si sale un administrador.

     ![Creación del trabajo de importación - Paso 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. En el **Resumen**:

   - Revise la información de trabajo proporcionada en el resumen. Anote el nombre del trabajo y la dirección de envío del centro de datos Azure para enviar discos a Azure. Esta información se utiliza posteriormente en la etiqueta de envío.
   - Haga clic en **Aceptar** para crear el trabajo de importación.

     ![Creación del trabajo de importación: Paso 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Paso 3: Envío de las unidades de disco 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Paso 4: Actualización del trabajo con información de seguimiento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Paso 5: Comprobación de la carga de datos en Azure

Siga el trabajo hasta su finalización. Una vez que se haya finalizado el trabajo, compruebe que los datos se han cargado en Azure. Elimine los datos de forma local después de comprobar que la carga fue correcta.

## <a name="next-steps"></a>Pasos siguientes

* [Visualización del estado del trabajo y de la unidad de disco](storage-import-export-view-drive-status.md)
* [Revisión de los requisitos de Import/Export](storage-import-export-requirements.md)


