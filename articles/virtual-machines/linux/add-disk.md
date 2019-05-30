---
title: Adición de un disco de datos a una máquina virtual de Linux con la CLI de Azure | Microsoft Docs
description: Aprenda a agregar un disco de datos persistente a una máquina virtual de Linux con la CLI de Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 6f4bd125847aa789f6f3ed06e808b40738e12260
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304103"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adición de un disco a una máquina virtual de Linux
En este artículo se muestra cómo conectar un disco persistente a la máquina virtual para que se puedan conservar los datos, incluso si la máquina virtual se vuelve a aprovisionar por mantenimiento o cambio de tamaño.


## <a name="attach-a-new-disk-to-a-vm"></a>Conexión de un nuevo disco a una máquina virtual

Si quiere agregar un nuevo disco de datos en la VM, use el comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) con el parámetro `--new`. Si la máquina virtual está en una zona de disponibilidad, el disco se crea automáticamente en la misma zona que la máquina virtual. Para obtener más información, consulte [Introducción a las zonas de disponibilidad](../../availability-zones/az-overview.md). En el ejemplo siguiente se crea un disco denominado *myDataDisk* que tiene un tamaño de 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>un disco existente

Para conectar un disco existente, busque el identificador del disco y páselo al comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest). El ejemplo siguiente consulta un disco llamado *myDataDisk* en *myResourceGroup* y, a continuación, lo asocia a la máquina virtual denominada *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conexión a la máquina virtual con Linux para montar el nuevo disco

Para la partición, el formato y el montaje del disco nuevo para que la máquina virtual con Linux pueda usarlo, necesita SSH en la VM. Para más información, consulte [Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure](mac-create-ssh-keys.md). En el siguiente ejemplo se realiza la conexión a una máquina virtual con la entrada DNS pública de *mypublicdns.westus.cloudapp.azure.com* con el nombre de usuario *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Una vez conectado a la máquina virtual, está listo para conectar un disco. Primero, busquemos el disco con `dmesg` (el método usado para detectar el nuevo disco puede variar). En el ejemplo siguiente se usa dmesg para filtrar por los discos *SCSI*:

```bash
dmesg | grep SCSI
```

La salida es similar a la del ejemplo siguiente:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

En este caso, *sdc* es el disco que queremos. Cree una partición del disco con `parted`. Si el tamaño del disco es de 2 tebibytes (TiB) u otro mayor, deberá usar la creación de particiones de GPT. Si es menor, podrá usar la de MBR o la de GPT. Si usa particiones MBR, puede usar `fdisk`. Conviértalo en un disco principal en la partición 1 y acepte los demás valores predeterminados. En el ejemplo siguiente se inicia el proceso `fdisk` en */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Use el comando `n` para agregar una nueva partición. En este ejemplo, también elegimos `p` para una partición principal y aceptamos el resto de los valores predeterminados. El resultado será similar al ejemplo siguiente:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Para imprimir la tabla de particiones, escriba `p` y luego use `w` para escribir la tabla en el disco y salga. El resultado debería similar al ejemplo siguiente:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Use el siguiente comando para actualizar el kernel:
```
partprobe 
```

Ahora, escriba un sistema de archivos en la partición con el comando `mkfs`. Especifique el tipo de sistema de archivos y el nombre del dispositivo. En el ejemplo siguiente se crea un sistema de archivos *ext4* en la partición */dev/sdc1* que se creó en los pasos anteriores:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

La salida es similar a la del ejemplo siguiente:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Ahora, cree un directorio para montar el sistema de archivos mediante `mkdir`. En el ejemplo se crea un directorio en */datadrive*:

```bash
sudo mkdir /datadrive
```

A continuación, use `mount` para montar el sistema de archivos. En el ejemplo siguiente, se monta la partición */dev/sdc1* en el punto de montaje */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo */etc/fstab*. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en */etc/fstab* para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (por ejemplo, */dev/sdc1*). Si el SO detecta un error de disco durante el arranque, con el UUID se evita que se monte el disco incorrecto en una ubicación especificada. A los discos de datos restantes se les asignarán después esos mismos identificadores de dispositivo. Para buscar el UUID de la unidad nueva, use la utilidad `blkid`:

```bash
sudo blkid
```

La salida es similar a la siguiente:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> La edición incorrecta del archivo **/etc/fstab** puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

Después, abra el archivo */etc/fstab* en un editor de texto como se indica a continuación:

```bash
sudo vi /etc/fstab
```

En este ejemplo, use el valor de UUID para el dispositivo */dev/sdc1* que se creó en los pasos anteriores y el punto de montaje */datadrive*. Agregue la siguiente línea al final del archivo */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Después, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. La mayoría de las distribuciones proporcionan las opciones de fstab *nofail* y/o *nobootwait*. Estas opciones permiten que el sistema arranque incluso si no se monta el disco en el momento del arranque. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.
>
> La opción *nofail* garantiza que la máquina virtual se inicia incluso si el sistema de archivos está dañado o el disco no existe en tiempo de arranque. Sin esta opción, puede encontrarse con el comportamiento que se describe en [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (No se puede conectar mediante SSH a una máquina virtual Linux debido a errores de FSTAB).
>
> La consola de serie de máquina virtual de Azure puede usarse para el acceso a la consola a la máquina virtual si modificar fstab ha producido un error de arranque. Encontrará más detalles en el [documentación de la consola serie](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Compatibilidad de TRIM/UNMAP con Linux en Azure
Algunos kernels de Linux admiten operaciones TRIM/UNMAP para descartar bloques no usados del disco. Esta característica es especialmente útil en el almacenamiento estándar para informar a Azure de que las páginas eliminadas ya no son válidas y se pueden descartar. Además, le permitirá ahorrar dinero si crea archivos de gran tamaño y luego los elimina.

Hay dos maneras de habilitar la compatibilidad con TRIM en su máquina virtual Linux. Como es habitual, consulte la documentación de distribución para ver el enfoque recomendado:

* Use la opción de montaje `discard` en */etc/fstab*, por ejemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* En algunos casos, la opción `discard` podría tener afectar al rendimiento. Como alternativa, puede ejecutar el comando `fstrim` manualmente desde la línea de comandos o agregarlo a su crontab para ejecutar con regularidad:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>solución de problemas

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Pasos siguientes

* Revise las recomendaciones para [optimizar el rendimiento de la máquina Linux](optimization.md) para asegurarse de que la máquina virtual Linux está configurada correctamente.
* Amplíe la capacidad de almacenamiento mediante la adición de discos adicionales y [configure RAID](configure-raid.md) para obtener un mayor rendimiento.
