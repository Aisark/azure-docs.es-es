---
title: Crear imágenes de máquina virtual de Azure compartidas para Windows mediante el portal
description: Obtenga información sobre cómo usar Azure Portal para crear y compartir imágenes de máquina virtual.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 0823942964f2bdb9f943d6eb778a3132574af0f7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065525"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Creación de una galería de imágenes compartidas mediante Azure Portal

Una [galería de imágenes compartidas](shared-image-galleries.md) simplifica el uso compartido de imágenes personalizadas en toda una organización. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas se pueden usar para realizar tareas de implementación de arranque, como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. 

La Galería de imágenes compartidas le permite compartir sus imágenes de máquina virtual personalizadas con otros usuarios de su organización, ya sea dentro o entre regiones, dentro de un inquilino de AAD. Elija las imágenes que desea compartir, qué regiones desea que estén disponibles en ellas y con quién desea compartirlas. Puede crear varias galerías que le permitirán agrupar lógicamente las imágenes compartidas. 

La galería es un recurso de nivel superior que proporciona control de acceso basado en roles (RBAC). Las imágenes pueden tener varias versiones y se puede optar por replicar cada versión de la imagen en un conjunto diferente de regiones de Azure. La galería solo funciona con imágenes administradas.

La característica de galería de imágenes compartidas tiene varios tipos de recursos. En este artículo, usaremos o crearemos los siguientes elementos:

| Resource | DESCRIPCIÓN|
|----------|------------|
| **Imagen administrada** | Una imagen básica que se puede usar por sí sola o para crear una **versión de imagen** de una galería de imágenes. Las imágenes administradas se crean a partir de máquinas virtuales [generalizadas](shared-image-galleries.md#generalized-and-specialized-images). Una imagen administrada es un tipo de VHD especial que se puede usar para crear varias máquinas virtuales y que ahora se puede usar para crear versiones de imágenes compartidas. |
| **Instantánea** | Una copia de un disco duro virtual que se puede usar para crear una **versión de imagen**. Las instantáneas pueden tomarse de una máquina virtual [especializada](shared-image-galleries.md#generalized-and-specialized-images) (una que no se ha generalizado) y, luego, usarse por sí sola o con instantáneas de discos de datos para crear una versión de imagen especializada.
| **Galería de imágenes** | Al igual que Azure Marketplace, una **galería de imágenes** es un repositorio para administrar y compartir imágenes, pero usted puede controlar quién tiene acceso. |
| **Definición de la imagen** | Las imágenes se definen dentro de una galería y contienen información sobre la imagen y los requisitos para usarla en la organización. Puede incluir información como si la imagen es generalizada o especializada, el sistema operativo, los requisitos de memoria mínima y máxima, y las notas de la versión. Es una definición de un tipo de imagen. |
| **Versión de la imagen** | Una **versión de la imagen** es lo que se usa para crear una VM cuando se usa una galería. Puede tener varias versiones de una imagen según sea necesario para su entorno. Al igual que una imagen administrada, cuando se usa una **versión de la imagen** para crear una VM, la versión de la imagen se usa para crear nuevos discos para la VM. Las versiones de las imágenes pueden usarse varias veces. |

<br>


> [!IMPORTANT]
> Las imágenes especializadas están actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitaciones conocidas de la versión preliminar** las máquinas virtuales solo se pueden crear a partir de imágenes especializadas mediante el portal o la API. No es compatible con la CLI ni con PowerShell para la versión preliminar.

## <a name="before-you-begin"></a>Antes de empezar

Para completar el ejemplo de este artículo, debe tener una imagen administrada existente de una máquina virtual generalizada o una instantánea de una máquina virtual especializada. Puede seguir [Tutorial: Cree una imagen personalizada de una máquina virtual de Azure con Azure PowerShell](tutorial-custom-images.md) para crear una imagen administrada o [Cree una instantánea](snapshot-copy-managed-disk.md) para una máquina virtual especializada. En el caso de las instantáneas e imágenes administradas, el tamaño del disco de datos no puede ser superior a 1 TB.

Al trabajar en este artículo, reemplace los nombres de grupo de recursos y máquina virtual cuando proceda.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Creación de máquinas virtuales

Ahora puede crear una o varias máquinas virtuales nuevas. En este ejemplo se crea una máquina virtual denominada *myVM*, en el *myResourceGroup*, en el centro de recursos del *Este de EE. UU.*

1. Vaya a la definición de la imagen. Puede usar el filtro de recursos para mostrar todas las definiciones de imagen disponibles.
1. En la página de la definición de la imagen, seleccione **Crear máquina virtual** en el menú de la parte superior de la página.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myResourceGroup* como nombre.
1. En **Nombre de máquina virtual**, escriba *myVM*.
1. En **Región**, seleccione *Este de EE. UU.* .
1. En **Opciones de disponibilidad**, deje el valor predeterminado *No se requiere redundancia de la infraestructura*.
1. El valor de **Imagen** se completa automáticamente con la versión de imagen `latest` si se inició desde la página para la definición de imagen.
1. En **Tamaño**, elija un tamaño de máquina virtual en la lista de tamaños disponibles y, después, elija **Seleccionar**.
1. En **Cuenta de administrador**, si la imagen estaba generalizada, debe proporcionar un nombre de usuario, como *azureuser* y una contraseña. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Si la imagen estaba especializada, los campos de nombre de usuario y contraseña se atenuarán porque se usan el nombre de usuario y la contraseña de la máquina virtual de origen.
1. Si quiere permitir el acceso remoto a la VM, en **Puertos de entrada públicos**, elija **Permitir puertos seleccionados** y, a continuación, seleccione **SSH (3389)** en la lista desplegable. Si no desea permitir el acceso remoto a la máquina virtual, deje **Ninguno** seleccionado en **Puertos de entrada públicos**.
1. Cuando haya terminado, seleccione el botón **Revisar y crear** situado en la parte inferior de la página.
1. Una vez que la máquina virtual haya superado la validación, seleccione **Crear** en la parte inferior de la página para iniciar la implementación.


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede eliminar el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la máquina virtual, seleccione **Eliminar** y luego confirme el nombre del grupo de recursos para eliminar.

Si desea eliminar recursos individuales, deberá eliminarlos en el orden inverso. Por ejemplo, para eliminar una definición de la imagen, deberá eliminar todas las versiones de la imagen creadas a partir de esa imagen.

## <a name="next-steps"></a>Pasos siguientes

Puede crear también recursos de galería de imágenes compartidas con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creación de una máquina virtual a partir de la versión de la imagen](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para más información sobre las galerías de imágenes compartidas, consulte la [Introducción](shared-image-galleries.md). Si encuentra problemas, consulte [Solución de problemas de galerías de imágenes compartidas](troubleshooting-shared-images.md).

