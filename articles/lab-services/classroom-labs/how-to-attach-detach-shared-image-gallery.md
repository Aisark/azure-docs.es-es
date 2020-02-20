---
title: Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services | Microsoft Docs
description: En este artículo se describe cómo asociar una galería de imágenes compartidas a un aula laboratorio en Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 1267487e44556a468db26a2ffaaa163dce540571
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443489"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services
Los profesores y administradores del laboratorio pueden guardar una imagen de máquina virtual de plantilla en una [galería de imágenes compartidas](../../virtual-machines/windows/shared-image-galleries.md) de Azure para que otros usuarios puedan reutilizarla. Como primer paso, el administrador del laboratorio asocia una galería de imágenes compartidas existente a la cuenta de laboratorio. Una vez que se ha asociado la galería de imágenes compartidas, los laboratorios creados en la cuenta de laboratorio pueden guardar imágenes en la galería de imágenes compartidas. Otros profesores pueden seleccionar esta imagen de la galería de imágenes compartidas para crear una plantilla para sus clases. 

En este artículo se muestra cómo asociar o desasociar una galería de imágenes compartidas en una cuenta de laboratorio. 

> [!NOTE]
> Cuando una imagen se guarda en una galería de imágenes compartida, Azure Lab Services replica la imagen guardada en otras regiones disponibles en la misma [ubicación geográfica](https://azure.microsoft.com/global-infrastructure/geographies/). Ello garantiza que la imagen está disponible para los laboratorios creados en otras regiones de la misma ubicación geográfica. Guardar las imágenes en una galería de imágenes compartida conlleva un costo adicional, lo que incluye el costo de todas las imágenes replicadas. Este costo es independiente del costo de uso de Azure Lab Services. Para más información sobre los precios de Shared Image Gallery, vea [Introducción a la galería de imágenes compartidas - Facturación]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configuración al crear la cuenta de laboratorio
Al crear una cuenta de laboratorio, puede asociarle una galería de imágenes compartidas. Puede seleccionar una galería de imágenes compartidas existente en la lista desplegable o bien crear una. Para crear y adjuntar una galería de imágenes compartidas a la cuenta de laboratorio, seleccione **Crear nuevo**, escriba un nombre para la galería y seleccione **Aceptar**. 

![Configuración de la Galería de imágenes compartidas en el momento de crear la cuenta de laboratorio](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configuración después de crear la cuenta de laboratorio
Una vez que se ha creado la cuenta de laboratorio, puede realizar las tareas siguientes:

- Creación y asociación de una galería de imágenes compartidas
- Asociar una galería de imágenes compartidas a la cuenta de laboratorio
- Desasociar una galería de imágenes compartidas de la cuenta de laboratorio

## <a name="create-and-attach-a-shared-image-gallery"></a>Creación y asociación de una galería de imágenes compartidas
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** en el menú de la izquierda. Seleccione **Lab Services** en la sección **DEVOPS**. Si selecciona la estrella (`*`) junto a **Lab Services**, se agrega a la sección **FAVORITOS** en el menú de la izquierda. A partir de la próxima vez, seleccione **Lab Services** en **FAVORITOS**.

    ![Todos los servicios -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Seleccione la cuenta de laboratorio para ver la página **Cuenta de laboratorio**. 
4. Seleccione **Galería de imágenes compartidas** en el menú de la izquierda y **+ Crear** en la barra de herramientas.  

    ![Botón para crear la galería de imágenes compartidas](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. En la ventana **Crear galería de imágenes compartidas**, escriba un **nombre** para la galería y seleccione **Aceptar**. 

    ![Ventana Crear galería de imágenes compartidas](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la galería de imágenes compartidas y la asocia a la cuenta de laboratorio. Todos los laboratorios creados en esta cuenta de laboratorio tienen acceso a la galería de imágenes compartidas adjunta. 

    ![Galería de imágenes adjunta](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    En el panel inferior, verá imágenes en la galería de imágenes compartidas. En esta galería nueva no hay ninguna imagen. Al cargar imágenes a la galería, las verá en esta página.     

    Todas las imágenes de la galería de imágenes compartidas adjunta están habilitadas de forma predeterminada. Puede habilitar o deshabilitar imágenes concretas si las selecciona en la lista y usa el botón **Enable selected images** (Habilitar imágenes seleccionadas) o **Disable selected images** (Deshabilitar imágenes seleccionadas).

## <a name="attach-an-existing-shared-image-gallery"></a>Asociación de una galería de imágenes compartidas existente
En el procedimiento siguiente se muestra cómo asociar una galería de imágenes compartidas existente a una cuenta de laboratorio. 

1. En la página **Cuenta de laboratorio**, seleccione **Galería de imágenes compartidas** en el menú de la izquierda y **Asociar** en la barra de herramientas. 

    ![Galería de imágenes compartidas: botón Agregar](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. En la página **Asociar una galería de imágenes compartidas existente**, seleccione la galería de imágenes compartidas y después **Aceptar**.

    ![Selección de una galería existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Verá la pantalla siguiente: 

    ![Mi galería en la lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    En este ejemplo, todavía no hay ninguna imagen en la galería de imágenes compartidas.

    La identidad de Azure Lab Services se agrega como colaborador a la galería de imágenes compartidas asociada al laboratorio. Permite a profesores y administradores de TI guardar imágenes de máquina virtual en la galería de imágenes compartidas. Todos los laboratorios creados en esta cuenta de laboratorio tienen acceso a la galería de imágenes compartidas adjunta. 

    Todas las imágenes de la galería de imágenes compartidas adjunta están habilitadas de forma predeterminada. Puede habilitar o deshabilitar imágenes concretas si las selecciona en la lista y usa el botón **Enable selected images** (Habilitar imágenes seleccionadas) o **Disable selected images** (Deshabilitar imágenes seleccionadas). 

## <a name="detach-a-shared-image-gallery"></a>Desasociación de una galería de imágenes compartidas
Solo se puede asociar una galería de imágenes compartidas a un laboratorio. Si quiere asociar otra galería de imágenes compartidas, desasocie la actual antes de asociar la nueva. Para desasociar una galería de imágenes compartidas del laboratorio, seleccione **Desasociar** en la barra de herramientas y confirme la operación de desasociación. 

![Desasociación de la galería de imágenes compartidas de la cuenta de laboratorio](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo guardar una imagen de laboratorio en la galería de imágenes compartidas o cómo usar una imagen de la galería para crear una máquina virtual, vea [Procedimientos para usar la galería de imágenes compartidas](how-to-use-shared-image-gallery.md).

Para obtener más información sobre las galerías de imágenes compartidas en general, vea [Galería de imágenes compartidas](../../virtual-machines/windows/shared-image-galleries.md).
