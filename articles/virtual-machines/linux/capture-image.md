---
title: Capturar una imagen de una máquina virtual de Linux mediante la CLI de Azure | Microsoft Docs
description: Capture una imagen de una máquina virtual de Azure que se usará para realizar implementaciones masivas mediante la CLI de Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 461df93400380ac74fa87eebc81fa06f77d7bf76
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551650"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Creación de una imagen de una máquina virtual o un disco duro virtual

<!-- generalize, image - extended version of the tutorial-->

Para crear varias copias de una máquina virtual para usarla en Azure, capture una imagen de la máquina virtual o el disco duro virtual del sistema operativo. Para crear una imagen para la implementación, deberá quitar la información de la cuenta personal. En los pasos siguientes, se desaprovisiona una máquina virtual existente, se desasigna y se crea una imagen. Puede usar esta imagen para crear VM en cualquier grupo de recursos dentro de su suscripción.

Para crear una copia de la máquina virtual Linux existente para la realización de una copia de seguridad o para su depuración o para cargar un disco duro virtual Linux especializado desde una máquina virtual local, consulte [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](upload-vhd.md).  

Puede utilizar el servicio **Azure VM Image Builder (versión preliminar pública)** para crear la imagen personalizada, sin necesidad de aprender las herramientas o configurar las canalizaciones de compilación, simplemente proporcionando una configuración de imagen e Image Builder creará la imagen. Para más información, consulte [Introducción a Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

También puede usar **Packer** para crear la configuración personalizada. Para más información, consulte [Uso de Packer para crear imágenes de máquinas virtuales Linux en Azure](build-image-with-packer.md).

Necesitará los siguientes elementos antes de crear una imagen:

* Una máquina virtual de Azure creada en el modelo de implementación de Resource Manager mediante discos administrados. Si aún no ha creado una máquina virtual Linux, puede usar el [portal](quick-create-portal.md), la [CLI de Azure](quick-create-cli.md) o [plantillas de Resource Manager](create-ssh-secured-vm-from-template.md). Configure la máquina virtual como considere necesario. Por ejemplo, [agregue discos de datos](add-disk.md), aplique actualizaciones e instale aplicaciones. 

* Necesita tener instalada la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) y haber iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Comandos rápidos

Para una versión simplificada de este artículo, con fines de prueba, evaluación o aprendizaje sobre máquinas virtuales de Azure, consulte [Creación de una imagen personalizada de una máquina virtual de Azure mediante la CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Paso 1: Desaprovisionar la máquina virtual
En primer lugar, desaprovisione la máquina virtual con el agente de máquinas virtuales de Azure para eliminar los archivos y datos específicos de la máquina. Use el comando `waagent` con el parámetro `-deprovision+user` en la máquina virtual Linux de origen. Consulte la [Guía de usuario del Agente de Linux de Azure](../extensions/agent-linux.md) para más información.

1. Conéctese a la máquina virtual Linux con un cliente de SSH.
2. En la ventana de SSH, escriba el siguiente comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. Este comando no garantiza que se haya borrado toda información confidencial de la imagen o que sea adecuada para su redistribución. El parámetro `+user` también elimina la última cuenta de usuario aprovisionada. Para mantener las credenciales de la cuenta de usuario en la máquina virtual, use solo `-deprovision`.
 
3. Escriba **s** para continuar. Puede agregar el parámetro `-force` para evitar este paso de confirmación.
4. Una vez finalizado el comando, escriba **exit** para cerrar el cliente de SSH.

## <a name="step-2-create-vm-image"></a>Paso 2: Crear imagen de máquina virtual
Use la CLI de Azure para marcar la máquina virtual como generalizada y capture la imagen. En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *myVnet* y *myVM*.

1. Desasigne la VM que desaprovisionó con [az vm deallocate](/cli/azure/vm). En el ejemplo siguiente se desasigna la máquina virtual llamada *myVM* en el grupo de recursos *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Marque la máquina virtual como generalizada con [az vm generalize](/cli/azure/vm). En el ejemplo siguiente se marca como generalizada la máquina virtual llamada *myVM* en el grupo de recursos *myResourceGroup*.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Cree una imagen del recurso de máquina virtual con [az image create](/cli/azure/image#az-image-create). En el ejemplo siguiente se crea una imagen llamada *myImage* en el grupo de recursos llamado *myResourceGroup* por medio del recurso de máquina virtual llamado *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > La imagen se crea en el mismo grupo de recursos que la VM de origen. Puede crear VM en cualquier grupo de recursos dentro de la suscripción a partir de esta imagen. Desde una perspectiva de administración, puede que desee crear un grupo de recursos específico para las imágenes y los recursos de VM.
   >
   > Si desea almacenar la imagen en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad ](../../availability-zones/az-overview.md) e incluir el parámetro `--zone-resilient true`.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Paso 3: Crear una máquina virtual a partir de la imagen capturada
Cree una máquina virtual con la imagen que ha creado con [az vm create](/cli/azure/vm). En el ejemplo siguiente se crea una máquina virtual llamada *myVMDeployed* a partir de la imagen *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Creación de la máquina virtual en otro grupo de recursos 

Puede crear máquinas virtuales en cualquier grupo de recursos dentro de la suscripción a partir de una imagen. Para crear una VM en un grupo de recursos diferente al de la imagen, especifique el identificador de recurso completo para la imagen. Use [az image list](/cli/azure/image#az-image-list) para ver una lista de imágenes. La salida será similar al del ejemplo siguiente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

En el ejemplo siguiente se utiliza [az vm create](/cli/azure/vm#az-vm-create) para crear una máquina virtual en un grupo de recursos diferente al de la imagen de origen especificando el identificador de recurso de imagen.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Paso 4: Comprobar la implementación

Conéctese por SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse mediante SSH, busque la dirección IP o el nombre de dominio completo de la máquina virtual con [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Pasos siguientes
Puede crear varias VM a partir de la imagen de VM de origen. Para realizar cambios en la imagen: 

- Cree una máquina virtual a partir de la imagen.
- Realice actualizaciones o cambios de configuración.
- Vuelva a seguir los pasos para desaprovisionar, desasignar, generalizar y crear una imagen.
- Use esta nueva imagen para implementaciones futuras. Puede eliminar la imagen original.

Para obtener más información sobre la administración de las máquinas virtuales con la CLI, consulte [CLI de Azure](/cli/azure).
