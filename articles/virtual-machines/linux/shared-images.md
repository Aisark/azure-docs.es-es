---
title: Creación de galerías de imágenes compartidas con la CLI de Azure | Microsoft Docs
description: En este artículo, aprenderá a usar la CLI de Azure para crear una imagen compartida de una máquina virtual en Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: ea884b80698fb257106ef68d293f350b2f55dc06
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103555"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Creación de una galería de imágenes compartidas con la CLI de Azure

Una [galería de imágenes compartidas](shared-image-galleries.md) simplifica el uso compartido de imágenes personalizadas en toda una organización. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. 

La Galería de imágenes compartidas le permite compartir sus imágenes de máquina virtual personalizadas con otros usuarios de su organización, ya sea dentro o entre regiones, dentro de un inquilino de AAD. Elija las imágenes que desea compartir, qué regiones desea que estén disponibles en ellas y con quién desea compartirlas. Puede crear varias galerías que le permitirán agrupar lógicamente las imágenes compartidas. 

La galería es un recurso de nivel superior que proporciona control de acceso basado en roles (RBAC). Las imágenes pueden tener varias versiones y se puede optar por replicar cada versión de la imagen en un conjunto diferente de regiones de Azure. La galería solo funciona con imágenes administradas.

La característica de galería de imágenes compartidas tiene varios tipos de recursos. En este artículo, usaremos o crearemos los siguientes elementos:

| Recurso | DESCRIPCIÓN|
|----------|------------|
| **Imagen administrada** | Se trata de una imagen básica que se puede usar por sí sola o para crear una **versión de imagen** de una galería de imágenes. Las imágenes administradas se crean desde máquinas virtuales generalizadas. Una imagen administrada es un tipo de VHD especial que se puede usar para crear varias máquinas virtuales y que ahora se puede usar para crear versiones de imágenes compartidas. |
| **Galería de imágenes** | Al igual que Azure Marketplace, una **galería de imágenes** es un repositorio para administrar y compartir imágenes, pero usted puede controlar quién tiene acceso. |
| **Definición de la imagen** | Las imágenes se definen dentro de una galería y contienen información sobre la imagen y los requisitos para usarla internamente. Esto incluye si la imagen es Windows o Linux, notas de la versión y los requisitos de memoria mínima y máxima. Es una definición de un tipo de imagen. |
| **Versión de la imagen** | Una **versión de la imagen** es lo que se usa para crear una VM cuando se usa una galería. Puede tener varias versiones de una imagen según sea necesario para su entorno. Al igual que una imagen administrada, cuando se usa una **versión de la imagen** para crear una VM, la versión de la imagen se usa para crear nuevos discos para la VM. Las versiones de las imágenes pueden usarse varias veces. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Crear una VM

Cree una máquina virtual a partir de la versión de imagen más reciente mediante [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

También puede usar una versión específica con el identificador de la versión de imagen del parámetro `--image`. Por ejemplo, para usar la versión de imagen *1.0.0*, escriba: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Pasos siguientes
[Azure Image Builder (versión preliminar)](image-builder-overview.md)puede ayudar a automatizar la creación de la versión de imagen, incluso lo puede usar para actualizar y [crear una versión de imagen nueva a partir de una versión de imagen existente](image-builder-gallery-update-image-version.md). 

Puede crear también recursos de Shared Image Gallery con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creación de una máquina virtual a partir de la versión de la imagen](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para más información sobre las galerías de imágenes compartidas, consulte la [Introducción](shared-image-galleries.md). Si encuentra problemas, consulte [Solución de problemas de galerías de imágenes compartidas](troubleshooting-shared-images.md).
