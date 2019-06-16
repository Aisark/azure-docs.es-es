---
title: Creación de una VM de Azure administrada a partir de un VHD local generalizado | Microsoft Docs
description: Cargue un VHD generalizado en Azure y úselo para crear máquinas virtuales nuevas en el modelo de implementación de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ee2fe91d915faf7e09dee004891edfc6bef38d6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685414"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carga de un VHD generalizado y su uso para crear máquinas virtuales nuevas en Azure

En este artículo se explica cómo usar PowerShell para cargar un VHD de una máquina virtual generalizada en Azure, crear una imagen a partir del VHD y crear una máquina virtual nueva desde esa imagen. Puede cargar un VHD exportado de una herramienta de visualización local o desde otra nube. Usar [Managed Disks](managed-disks-overview.md) para la nueva VM simplifica la administración de la VM y proporciona una mejor disponibilidad cuando la VM se encuentra en un conjunto de disponibilidad. 

Para un script de ejemplo, consulte [Script de ejemplo para cargar un disco duro virtual en Azure y crear una máquina virtual nueva](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de empezar

- Antes de cargar los VHD en Azure, debe consultar [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Revise [Plan for the migration to Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Planeación de la migración a Managed Disks) antes de comenzar la migración a [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalización de la VM de origen mediante Sysprep

Entre otras características, Sysprep elimina toda la información personal de la cuenta y prepara, entre otras cosas, la máquina para usarse como imagen. Para más información acerca de Sysprep, consulte la [Introducción a Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)(Compatibilidad de Sysprep con roles de servidor).

> [!IMPORTANT]
> Si tiene pensado ejecutar Sysprep antes de cargar el VHD en Azure por primera vez, asegúrese de que tiene [preparada la máquina virtual](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Inicie sesión en la máquina virtual de Windows.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a %windir%\system32\sysprep, y, después, ejecute `sysprep.exe`.
3. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está seleccionada.
4. En **Opciones de apagado**, seleccione **Apagar**.
5. Seleccione **Aceptar**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Cuando Sysprep finaliza, apaga la máquina virtual. No reinicie la VM.


## <a name="get-a-storage-account"></a>Obtener una cuenta de almacenamiento

Necesitará una cuenta de almacenamiento de Azure para almacenar la imagen de VM cargada. Puede usar una cuenta de almacenamiento existente o crear una nueva. 

Si va a usar el VHD para crear un disco administrado para una VM, la ubicación de la cuenta de almacenamiento debe ser la misma ubicación donde va a crear la VM.

Para mostrar las cuentas de almacenamiento disponibles, escriba:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Carga del VHD en la cuenta de almacenamiento

Use el cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para cargar el VHD en un contenedor de su cuenta de almacenamiento. En este ejemplo se carga el archivo *myVHD.vhd* de *C:\Users\Public\Documents\Virtual hard disks\\* para una cuenta de almacenamiento denominada *mystorageaccount* en el grupo de recursos *myResourceGroup*. El archivo se colocará en el contenedor llamado *mycontainer* y el nuevo nombre de archivo será *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si se realiza correctamente, obtendrá una respuesta similar a la siguiente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependiendo de la conexión de red y del tamaño del archivo VHD, este comando puede tardar algún tiempo en completarse.

### <a name="other-options-for-uploading-a-vhd"></a>Otras opciones para cargar un disco duro virtual
 
También puede cargar un VHD en la cuenta de almacenamiento mediante uno de los siguientes medios:

- [AzCopy](https://aka.ms/downloadazcopy)
- [API de tipo Copy Blob de Azure Storage](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Blobs de carga de Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
- [Referencia de la API de REST del servicio Import/Export de Storage](https://msdn.microsoft.com/library/dn529096.aspx)
-   Se recomienda utilizar el servicio Import/Export si se calcula que el tiempo de carga va a ser superior a siete días. Puede usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para calcular el tiempo a partir del tamaño de los datos y la unidad de transferencia. 
    Import/Export se puede usar para realizar copias en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento prémium, use una herramienta como AzCopy.

> [!IMPORTANT]
> Si usa AzCopy para cargar su VHD en Azure, asegúrese de que ha establecido [ **/BlobType:page**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) antes de ejecutar el script de carga. Si el destino es un blob y esta opción no se especifica, AzCopy, de forma predeterminada, creará un blob en bloques.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Creación de una imagen administrada desde el VHD cargado 

Cree una imagen administrada desde el VHD del SO generalizado. Reemplace los valores siguientes por su propia información.


Primero, configure algunos parámetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Cree la imagen con el VHD del SO generalizado.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Creación de la máquina virtual

Ahora que tiene una imagen, puede crear una o varias VM desde la imagen. En este ejemplo se crea una máquina virtual denominada *myVM* a partir de *myImage*, en *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Pasos siguientes

Inicie sesión en la nueva máquina virtual. Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

