---
title: Cargar o copiar una máquina virtual de Linux personalizada con la CLI de Azure | Microsoft Docs
description: Cargar o copiar una máquina virtual personalizada con el modelo de implementación de Resource Manager y la CLI de Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 026cab6a5749f556d6f748c80e492d1c920767d1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708403"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Creación de una VM Linux desde un disco personalizado con la CLI de Azure

<!-- rename to create-vm-specialized -->

En este artículo se muestra cómo cargar un disco duro virtual (VHD) personalizado y cómo copiar un VHD existente en Azure. Después, el VHD recién creado se usa para crear nuevas máquinas virtuales (VM) Linux. Puede instalar y configurar una distribución de Linux para sus requisitos y, después, usar ese VHD para crear una nueva máquina virtual de Azure.

Para crear varias VM desde el disco personalizado, cree primero una imagen de la VM o el VHD. Para más información, vea [Creación de una imagen personalizada de una máquina virtual de Azure con la CLI](tutorial-custom-images.md).

Tiene dos opciones para crear un disco personalizado:
* Carga de un disco duro virtual
* Copia de una máquina virtual de Azure existente

## <a name="quick-commands"></a>Comandos rápidos

Al crear una nueva VM mediante [az vm create](/cli/azure/vm#az-vm-create) desde un disco personalizado o especializado, se **adjunta** el disco (--attach-os-disk) en lugar de especificar una imagen personalizada o de Marketplace (--image). En el ejemplo siguiente se crea una máquina virtual denominada *myVM* mediante el disco administrado llamado *myManagedDisk* creado a partir del VHD personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisitos
Para completar los pasos siguientes, necesita:

* Una máquina virtual Linux que se ha preparado para su uso en Azure. En la sección [Preparación de la VM](#prepare-the-vm) de este artículo se explica cómo encontrar información de distribución sobre la instalación del agente Linux de Azure (waagent), que es necesario para conectarse a una VM con SSH.
* El archivo VHD de una [distribución de Linux aprobada por Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) existente (o vea [Información para las distribuciones no aprobadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
  * Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](https://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert`.
  * También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX en VHD mediante [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas, como [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go), para convertir discos dinámicos durante el proceso de carga en Azure.
> 
> 


* Asegúrese de que tiene la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) instalada y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index#az-login).

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores, como *myResourceGroup*, *mystorageaccount* y *mydisks*.

<a id="prepimage"></a>

## <a name="prepare-the-vm"></a>Preparación de la VM

Azure admite varias distribuciones Linux (consulte [Distribuciones aprobadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). En los artículos siguientes se describe el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

* [Distribuciones basadas en CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES y openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Otras: distribuciones no aprobadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vea también las [notas de instalación de Linux](create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

> [!NOTE]
> El [Acuerdo de Nivel de Servicio de la plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a las VM que ejecutan Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección "Versiones admitidas" en [Linux en distribuciones aprobadas por Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opción 1: Carga de un disco duro virtual

Puede cargar un VHD personalizado que se esté ejecutando en un equipo local o que se haya exportado desde otra nube. Para usar un VHD para crear una nueva VM de Azure, debe cargarlo en una cuenta de almacenamiento y crear un disco administrado a partir del VHD. Para obtener más información, consulte [Azure Managed Disks overview](../windows/managed-disks-overview.md) (Información general sobre Azure Managed Disks).

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de cargar el disco personalizado y crear VM, es preciso crear un grupo de recursos con [az group create](/cli/azure/group#az-group-create).

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento para el disco personalizado y las máquinas virtuales con [az storage account create](/cli/azure/storage/account). En el ejemplo siguiente se crea una cuenta de almacenamiento denominada *mystorageaccount* en el grupo de recursos creado anteriormente:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Enumerar claves de cuentas de almacenamiento
Azure genera dos claves de acceso de 512 bits para cada cuenta de almacenamiento. Estas claves de acceso se usan al autenticarse en la cuenta de almacenamiento, como al realizar las operaciones de escritura. Para más información, consulte la [administración del acceso al almacenamiento](../../storage/common/storage-account-manage.md#access-keys). 

Vea las teclas de acceso con [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list). Por ejemplo, consulte las claves de acceso de la cuenta de almacenamiento que ha creado:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

La salida es parecida a esta:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Tome nota de **key1**, pues se usará para interactuar con la cuenta de almacenamiento en los siguientes pasos.

### <a name="create-a-storage-container"></a>Creación de un contenedor de almacenamiento
De la misma manera que crea directorios distintos para organizar de forma lógica el sistema de archivos local, creará contenedores dentro de una cuenta de almacenamiento para organizar los discos. Una cuenta de almacenamiento puede contener varios contenedores. Cree un contenedor con [az storage container create](/cli/azure/storage/container#az-storage-container-create).

En el siguiente ejemplo se crea un contenedor denominado *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Carga del disco duro virtual
Cargue su disco personalizado con [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload). Cargará y almacenará el disco personalizado como un blob en páginas.

Especifique la clave de acceso, el contenedor que creó en el paso anterior y, a continuación, la ruta de acceso al disco personalizado del equipo local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
La carga del VHD puede llevar un rato.

### <a name="create-a-managed-disk"></a>Creación de un disco administrado


Cree un disco administrado a partir del VHD con [az disk create](/cli/azure/disk#az-disk-create). En el ejemplo siguiente se crea un disco administrado denominado *myManagedDisk* a partir del VHD cargado en la cuenta de almacenamiento y el contenedor con nombre:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opción 2: Copiar una máquina virtual existente

También se puede crear una VM personalizada en Azure y, luego, copiar el disco del sistema operativo y asociarlo a una nueva VM para crear otra copia. Esto es adecuado para realizar pruebas, pero si quiere usar una VM existente de Azure como modelo para varias VM nuevas, en realidad debe crear una *imagen*. Para más información sobre cómo crear una imagen a partir de una VM existente de Azure, vea [Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Crear una instantánea

En este ejemplo se crea una instantánea de una máquina virtual denominada *myVM* en el grupo de recursos *myResourceGroup* y se crea una instantánea denominada *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Creación del disco administrado

Cree un nuevo disco administrado a partir de la instantánea.

Obtenga el identificador de la instantánea. En este ejemplo, la instantánea se denomina *osDiskSnapshot* y se encuentra en el grupo de recursos *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Cree el disco administrado. En este ejemplo se va a crear un disco administrado denominado *myManagedDisk* a partir de la instantánea, donde el disco tiene un almacenamiento estándar y un tamaño de 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Creación de la máquina virtual

Cree la VM con [az vm create](/cli/azure/vm#az-vm-create) y asocie (--attach-os-disk) el disco administrado como disco del sistema operativo. En el ejemplo siguiente se crea una VM denominada *myNewVM* mediante el disco administrado creado a partir del VHD cargado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Debe ser capaz de iniciar sesión con SSH en la VM con las credenciales de la VM de origen. 

## <a name="next-steps"></a>Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre el [uso de Resource Manager y las plantillas](../../azure-resource-manager/resource-group-overview.md). También es posible que quiera [agregar un disco de datos](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a las nuevas máquinas virtuales. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite tener acceso, asegúrese de [abrir puertos y puntos de conexión](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
