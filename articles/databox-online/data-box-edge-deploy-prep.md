---
title: Tutorial sobre la preparación de Azure Portal para la implementación de Data Box Edge | Microsoft Docs
description: El primer tutorial para implementar Azure Data Box Edge trata sobre la preparación de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 19c4fc96653f966ea5642149d944886e4b7f4483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401674"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: Preparación para la implementación de Azure Data Box Edge  


Este es el primer tutorial de la serie de tutoriales de implementación necesarios para implementar completamente Azure Data Box Edge. En este tutorial se describe cómo preparar Azure Portal para implementar un recurso de Data Box Edge.

Para completar el proceso de instalación y configuración se necesitan privilegios de administrador. La preparación del portal dura menos de 10 minutos.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un nuevo recurso
> * Obtención de la clave de activación

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


### <a name="get-started"></a>Introducción

Para implementar Data Box Edge, consulte los siguientes tutoriales en el orden indicado.

| **#** | **En este paso** | **Use estos documentos** |
| --- | --- | --- | 
| 1. |**[Preparación de Azure Portal para Data Box Edge](data-box-edge-deploy-prep.md)** |Cree y configure el recurso de Data Box Edge antes de instalar un dispositivo físico de Data Box Edge. |
| 2. |**[Instalación de Data Box Edge](data-box-edge-deploy-install.md)**|Desempaquete, monte en el bastidor y realice el cableado del dispositivo físico de Data Box Edge.  |
| 3. |**[Conexión, configuración y activación de Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Conéctese a la interfaz de usuario web local, complete la configuración del dispositivo y active el dispositivo. El dispositivo está listo para configurar recursos compartidos de SMB o NFS.  |
| 4. |**[Transferencia de datos con Data Box Edge](data-box-edge-deploy-add-shares.md)** |Agregue recursos compartidos y conéctese a ellos mediante SMB o NFS. |
| 5. |**[Transformación de datos con Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configure los módulos de Edge en el dispositivo para que transformen los datos a medida que estos se mueven a Azure. |

Ya puede empezar a configurar Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

A continuación, encontrará los requisitos previos para configurar su recurso de Data Box Edge, el dispositivo Data Box Edge y la red de centros de datos.

### <a name="for-the-data-box-edge-resource"></a>Para el recurso de Data Box Edge

Antes de comenzar, asegúrese de que:

- Su suscripción de Microsoft Azure debe estar habilitada para el recurso de Data Box Edge. No se admiten suscripciones de pago por uso.
- Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

### <a name="for-the-data-box-edge-device"></a>Para el dispositivo de Data Box Edge

Antes de implementar un dispositivo físico, asegúrese de que:

- Ha revisado la información de seguridad que se incluyó en el paquete de envío.
- Tiene una ranura 1 U disponible en un bastidor estándar de 19" en su centro de datos para montar el dispositivo. 
- Tiene acceso a una superficie de trabajo plana, estable y nivelada en la que el dispositivo pueda apoyarse con seguridad.
- La ubicación en la que desea efectuar la instalación del dispositivo dispone de corriente alterna estándar de una fuente independiente o una unidad de distribución de energía (PDU) en bastidor con un sistema de alimentación ininterrumpida (UPS).
- Tiene acceso a un dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para la red del centro de datos

Antes de comenzar, asegúrese de que:

- La red del centro de datos está configurada según los requisitos de red para el dispositivo de Data Box Edge. Para más información, consulte [Requisitos del sistema de Data Box Edge](data-box-edge-system-requirements.md).

- Para que Data Box Edge funcione normalmente, necesita lo siguiente:

    - Un ancho de banda mínimo de 10 Mbps para garantizar que el dispositivo se mantiene actualizado.
    - Un ancho de banda mínimo de 20 Mbps dedicado a carga y descarga para transferir archivos.

## <a name="create-a-new-resource"></a>Crear un nuevo recurso

Si ya tiene un recurso de Data Box Edge para administrar el dispositivo físico, omita este paso y vaya a [Obtención de la clave de activación](#get-the-activation-key).

Siga estos pasos en Azure Portal para crear un recurso de Data Box Edge.

1. Use sus credenciales de Microsoft Azure para iniciar sesión en: 
    
    - Azure Portal en esta dirección URL: [https://portal.azure.com](http://portal.azure.com).
    - O bien, el portal de Azure Government en esta dirección URL: [https://portal.azure.us](https://portal.azure.us)

2. En el menú izquierdo, seleccione **+ Crear un recurso**. Busque **Data Box Edge/Data Box Gateway**. Seleccione **Data Box Edge/Data Box Gateway**. Seleccione **Crear**.
3. Seleccione la suscripción que quiere usar para el dispositivo de Data Box Edge. Seleccione la región donde desea implementar el recurso Data Box Edge. En esta versión, están disponibles las regiones de Este de EE. UU., Sudeste Asiático y Europa Occidental. Elija la ubicación más cercana a la región geográfica donde quiera implementar el dispositivo. En la opción **Data Box Edge**, haga clic en **Crear**.

    ![Búsqueda del servicio Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. En la pestaña **Datos básicos**, escriba o seleccione los siguientes **detalles del proyecto**.
    
    |Configuración  |Valor  |
    |---------|---------|
    |Subscription    |Este valor se rellena automáticamente según la selección anterior. La suscripción está vinculada a la cuenta de facturación. |
    |Grupos de recursos  |Cree un nuevo grupo o seleccione uno existente.<br>Más información sobre los [grupos de recursos de Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Escriba o seleccione los siguientes **detalles de la instancia**.

    |Configuración  |Valor  |
    |---------|---------|
    |NOMBRE   | Nombre descriptivo que identifique el recurso.<br>El nombre tiene entre 2 y 50 caracteres que contiene letras, números y guiones.<br> El nombre comienza y termina con una letra o un número.        |
    |Region     |En esta versión, están disponibles las regiones de Este de EE. UU., Sudeste Asiático y Europa Occidental para implementar el recurso. Si usa Azure Government, todas las regiones de gobierno están disponibles como se muestra en las [regiones de Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Elija la ubicación más cercana a la región geográfica donde quiera implementar el dispositivo.|

    ![Detalles del proyecto e instancia](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Seleccione **Siguiente: Dirección de envío**.

    - Si ya tiene un dispositivo, seleccione el cuadro combinado **Tengo un dispositivo Data Box Edge**.
    - Si este es el pedido del nuevo dispositivo, escriba el nombre de contacto, la empresa, la dirección para enviar el dispositivo y la información de contacto.

    ![Dirección de envío del nuevo dispositivo](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Seleccione **Siguiente: Review + create** (Revisar y crear).

7. En la pestaña **Revisar y crear**, revise la información de **Detalles de precios**, **Términos de uso** y los detalles de su recurso. Seleccione el cuadro combinado **I have reviewed the privacy terms** (He revisado los términos de privacidad).

    ![Revisión de los detalles del recurso y los términos de privacidad de Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Seleccione **Crear**.

Se tarda unos minutos en crear el recurso. Cuando el recurso se haya creado e implementado correctamente, recibirá una notificación. Haga clic en **Go to resource** (Ir al recurso).

![Ir al recurso de Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Tras realizar el pedido, Microsoft lo revisa y se pone en contacto con usted (por correo electrónico) para indicarle los detalles del envío.

![Notificación de revisión del pedido de Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Obtención de la clave de activación

Cuando el recurso de Data Box Edge esté en funcionamiento, deberá obtener la clave de activación. Esta clave se utiliza para activar y conectar el dispositivo de Data Box Edge con el recurso. Puede obtener esta clave ahora mientras está en Azure Portal.

1. Seleccione el recurso que ha creado. Seleccione **Información general** y, luego, **Instalación del dispositivo**.

    ![Selección de instalación del dispositivo](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. En el icono **Activar**, seleccione **Generar clave** para crear una clave de activación. Seleccione el icono de copia para copiar la clave y guárdela para su uso posterior.

    ![Obtención de la clave de activación](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La clave de activación expira tres días después de generarla.
> - Si la clave ha expirado, genere una nueva. La clave antigua no es válida.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Data Box Edge, como:

> [!div class="checklist"]
> * Crear un nuevo recurso
> * Obtención de la clave de activación

En el siguiente tutorial aprenderá a instalar un dispositivo de Data Box Edge.

> [!div class="nextstepaction"]
> [Instalación de Data Box Edge](./data-box-edge-deploy-install.md)



