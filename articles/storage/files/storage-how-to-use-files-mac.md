---
title: Montaje de un recurso compartido de archivos de Azure mediante SMB con macOS | Microsoft Docs
description: Obtenga información acerca de cómo montar un recurso compartido de archivos de Azure mediante SMB con macOS.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f2abbb355513c175329d01b9d5ed8884f2c27ef
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763539"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montaje de un recurso compartido de archivos de Azure mediante SMB con macOS
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar con el protocolo estándar del sector SMB 3 a partir de macOS El Capitan 10.11+. En este artículo se muestran dos maneras diferentes de montar un recurso compartido de archivos de Azure en macOS, con la interfaz de usuario de Finder y utilizando el Terminal.

> [!Note]  
> Antes de montar un recurso compartido de archivos de Azure con SMB, se recomienda deshabilitar la firma de paquetes SMB. De no hacerlo, puede producirse un rendimiento deficiente en el acceso al recurso compartido de archivos de Azure desde macOS. La conexión SMB será cifrada, por lo que esto no afecta a la seguridad de la conexión. Desde el terminal, los siguientes comandos deshabilitan firma del paquete SMB, como se describe en este [artículo de soporte técnico de Apple acerca de cómo deshabilitar la firma de paquetes SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Requisitos previos para el montaje de un recurso compartido de archivos de Azure en macOS
* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará la clave principal (o secundaria). Actualmente no se admiten claves SAS para el montaje.

* **Asegúrese de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445. En el equipo cliente (Mac), compruebe que el firewall no bloquea el puerto TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montaje de un recurso compartido de archivos de Azure con Finder
1. **Abra Finder**: Finder está abierto en macOS de forma predeterminada, pero para asegurarse de que es la aplicación actualmente seleccionada, haga clic en el "icono de cara de macOS" en el Dock:  
    ![Icono de cara de macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Seleccione "Connect to Server" (Conectar a servidor) en el menú "Go" (Ir)**: en la ruta de acceso UNC de los requisitos previos, convierta la doble barra diagonal inversa de comienzo (`\\`) en `smb://` y todas las otras barras diagonales inversas (`\`) en barras diagonales (`/`). El vínculo debería tener este aspecto: ![Cuadro de diálogo "Connect to Server" (Conectar a servidor)](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Use el nombre y la clave de la cuenta de almacenamiento cuando se le pida un nombre de usuario y una contraseña**: al hacer clic en "Connect" (Conectar) en el cuadro de diálogo "Connect to Server" (Conectar a servidor), se le solicitará el nombre de usuario y la contraseña (este se rellenará automáticamente con el nombre de usuario de macOS). Puede guardar el nombre y la clave de la cuenta de almacenamiento en la cadena de claves de macOS.

4. **Use el recurso compartido de archivos de Azure de la forma deseada**: después de sustituir el nombre del recurso compartido y la clave de la cuenta de almacenamiento por el nombre de usuario y la contraseña, se montará el recurso compartido. Se puede utilizar como lo haría normalmente con una carpeta local o un recurso compartido de archivos, incluido arrastrar y soltar archivos en el recurso compartido de archivos:

    ![Captura de pantalla de un recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montaje de un recurso compartido de archivos de Azure con el Terminal
1. Sustituya  `<storage-account-name>`  por el nombre de la cuenta de almacenamiento. Proporcione la clave de cuenta de almacenamiento como contraseña cuando se le solicite. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Use el recurso compartido de archivos de Azure de la forma deseada**: el recurso compartido de archivos de Azure se montará en el punto de montaje especificado por el comando anterior.  

    ![Captura de pantalla del recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información acerca Azure Files.

* [Artículo de soporte técnico de Apple: cómo conectarse con uso compartido de archivos en el equipo Mac](https://support.apple.com/HT204445)
* [P+F](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solución de problemas en Linux](storage-troubleshoot-linux-file-connection-problems.md)    
