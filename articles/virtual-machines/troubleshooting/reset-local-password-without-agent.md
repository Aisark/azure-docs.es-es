---
title: Restablecimiento de una contraseña local de Windows sin el agente de Azure| Microsoft Docs
description: Restablecimiento de la contraseña de una cuenta de usuario de Windows local cuando el agente invitado de Azure no está instalado o funcionando en una VM
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6b77ceb2ab9abe232cec75254b30ce37c3dbbf60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307725"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Restablecimiento de una contraseña de Windows local para VM de Azure sin conexión
Puede restablecer la contraseña de Windows local de una VM en Azure mediante [Azure Portal o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) siempre que el agente invitado de Azure esté instalado. Este método es la manera principal de restablecer una contraseña para una VM de Azure. Si tiene problemas con el agente de invitado de Azure, como puede ser que no responda o que no se pueda instalar después de cargar una imagen personalizada, puede restablecer manualmente una contraseña de Windows. En este artículo se detalla cómo restablecer la contraseña de una cuenta local asociando el disco virtual de SO de origen a otra VM. Los pasos descritos en este artículo no se aplican a los controladores de dominio de Windows. 

> [!WARNING]
> Utilice este proceso solamente como último recurso. Intente siempre restablecer una contraseña mediante [Azure Portal o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primero.
> 
> 

## <a name="overview-of-the-process"></a>Información general del proceso
Los pasos principales para realizar un restablecimiento de contraseña para una VM con Windows en Azure cuando no hay acceso al agente invitado de Azure son los siguientes:

* Eliminar la VM de origen. Los discos virtuales se conservan.
* Asociar el disco del SO de la máquina virtual de origen a otra máquina virtual con la misma ubicación dentro de su suscripción de Azure. Se hace referencia a esta VM como la VM de solución de problemas.
* Con la VM para solucionar problemas, cree algunos archivos de configuración en el disco del SO de la VM de origen.
* Desasocie el disco del SO de la VM de la VM de solución de problemas.
* Use una plantilla de Resource Manager para crear una VM mediante el disco virtual original.
* Cuando se inicia la nueva VM, los archivos de configuración que crea actualizan la contraseña del usuario necesario.

> [!NOTE]
> Puede automatizar los procesos siguientes:
>
> - Creando la VM de solución de problemas
> - Adjuntando el disco del sistema operativo
> - Volviendo a crear la VM original
> 
> Para ello, use los [scripts de recuperación de VM de Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Si decide usar los scripts de recuperación de VM de Azure, puede seguir el proceso a continuación en la sección "Pasos detallados":
> 1. Omita los pasos 1 y 2 con los scripts para adjuntar el disco del sistema operativo de la VM afectada a una VM de recuperación.
> 2. Siga los pasos de 3 a 6 para aplicar las mitigaciones.
> 3. Omitir los pasos 7 a 9 con los scripts para recompilar la VM.
> 4. Siga los pasos 10 y 11.

## <a name="detailed-steps"></a>Pasos detallados

> [!NOTE]
> Los pasos no se aplican a los controladores de dominio de Windows. Solo funcionan en un servidor independiente o en uno que sea miembro de un dominio.
> 
> 

Intente siempre restablecer una contraseña mediante [Azure Portal o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de intentar llevar a cabo los pasos siguientes. Asegúrese de que tiene una copia de seguridad de la VM antes de empezar. 

1. Elimine la VM afectada de Azure Portal. La eliminación de la VM, solo elimina los metadatos, la referencia de la VM dentro de Azure. Los discos virtuales se conservan cuando se elimina la VM:
   
   * Seleccione la VM en Azure Portal y haga clic en *Eliminar*:
     
     ![Eliminación de la VM existente](./media/reset-local-password-without-agent/delete_vm.png)
2. Asocie el disco del SO de la VM de origen a la VM de solución de problemas. La VM de solución de problemas debe estar en la misma región que el disco del SO de la VM de origen (como `West US`):
   
   * Seleccione la VM de solución de problemas en Azure Portal. Haga clic en *Discos* | *Asociar existente*:
     
     ![Conectar disco existente](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Seleccione *Archivo VHD* y seleccione la cuenta de almacenamiento que contiene su VM de origen:
     
     ![Selección de la cuenta de almacenamiento](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Seleccione el contenedor de origen. El contenedor de origen suele ser *vhds*:
     
     ![Selección del contenedor de almacenamiento](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Seleccione el disco duro virtual de SO para asociar. Haga clic en *Seleccionar* para completar el proceso:
     
     ![Seleccione el disco virtual de origen](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Conéctese a la VM de solución de problemas mediante Escritorio remoto y asegúrese de que el disco del SO de la VM de origen está visible:
   
   * Seleccione la VM de solución de problemas en Azure Portal y haga clic en *Conectar*.
   * Abra el archivo RDP que descarga. Escriba el nombre de usuario y la contraseña de la VM de solución de problemas.
   * En el Explorador de archivos, busque el disco de datos que se ha asociado. Si el origen de disco duro virtual de la VM es el único disco de datos asociado a la VM de solución de problemas, debe ser la unidad F:
     
     ![Visualización del disco de datos conectado](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Cree `gpt.ini` en `\Windows\System32\GroupPolicy` en la unidad de la VM de origen (si gpt.ini existe, cambie el nombre a gpt.ini.bak):
   
   > [!WARNING]
   > Asegúrese de que no crea accidentalmente los siguientes archivos en C:\Windows, la unidad del SO para la VM de solución de problemas. Cree los siguientes archivos en la unidad del SO para la VM que se asocia como disco de datos.
   > 
   > 
   
   * Agregue las líneas siguientes al archivo `gpt.ini` que creó:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Creación de gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Cree `scripts.ini` en `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Asegúrese de que se muestran las carpetas ocultas. Si es necesario, cree las carpetas `Machine` o `Scripts`.
   
   * Agregue las líneas siguientes al archivo `scripts.ini` que creó:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Creación de scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Crear `FixAzureVM.cmd` en `\Windows\System32` con el contenido siguiente, reemplazando `<username>` y `<newpassword>` por sus propios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Creación de FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Al definir la nueva contraseña debe cumplir los requisitos de complejidad de contraseña configurada para la VM.
7. En Azure Portal, desasocie el disco de la VM de solución de problemas:
   
   * Seleccione la VM de solución de problemas en Azure Portal y haga clic en *Discos*.
   * Seleccione el disco de datos asociado en el paso 2, haga clic en *Desasociar*:
     
     ![Desasociación del disco](./media/reset-local-password-without-agent/detach_disk.png)
8. Antes de crear una VM, obtenga el URI en el disco del SO de origen:
   
   * Seleccione la cuenta de almacenamiento en Azure Portal y haga clic en *Blobs*.
   * Seleccione el contenedor. El contenedor de origen suele ser *vhds*:
     
     ![Seleccione el blob de la cuenta de almacenamiento](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Seleccione el disco duro virtual del sistema operativo de la VM y haga clic en el botón *Copiar* situado junto al nombre de *URL*:
     
     ![Copia del URI de disco](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Cree una VM desde el disco del SO de la VM de origen:
   
   * Use [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) para crear una VM desde un disco duro virtual especializado. Haga clic en el botón `Deploy to Azure` para abrir Azure Portal con los detalles de la plantilla rellenados automáticamente.
   * Si desea conservar toda la configuración anterior de la VM, seleccione *Editar plantilla* para proporcionar la red virtual, la subred, el adaptador de red o la dirección IP pública existentes.
   * En el cuadro de texto del parámetro `OSDISKVHDURI`, pegue el URI de su disco duro virtual de origen obtenido en el paso anterior:
     
     ![Creación de una VM a partir de una plantilla](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Después de que la CM se ejecute, conéctese a la VM mediante Escritorio remoto con la nueva contraseña que especificó en el script `FixAzureVM.cmd`.
11. En la sesión remota respecto a la nueva VM, quite los archivos siguientes para limpiar el entorno:
    
    * De %windir%\System32
      * quite FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\
      * quite scripts.ini
    * De %windir%\System32\GroupPolicy
      * quite gpt.ini (si gpt.ini existía antes y cambió su nombre a gpt.ini.bak, vuelva a cambiar el nombre del archivo .bak a gpt.ini)

## <a name="next-steps"></a>Pasos siguientes
Si sigue sin poder conectarse mediante Escritorio remoto, consulte la [guía de solución de problemas de RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). La [guía detallada de solución de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) examina los métodos de solución de problemas en lugar de pasos específicos. También puede [abrir una solicitud de soporte técnico de Azure](https://azure.microsoft.com/support/options/) para obtener ayuda práctica.

