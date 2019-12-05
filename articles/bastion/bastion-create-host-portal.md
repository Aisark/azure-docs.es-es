---
title: Creación de un host de Azure Bastion | Microsoft Docs
description: En este artículo aprenderá a crear un host de Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: 7838e1930a8ccd190c95740a6a8e3dadc68149f0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422119"
---
# <a name="create-an-azure-bastion-host"></a>Creación de un host de Azure Bastion

En este artículo aprenderá a crear un host de Azure Bastion. Una vez que haya aprovisionado el servicio Azure Bastion en su red virtual, la experiencia fluida mediante RDP/SSH estará disponible para todas las máquinas virtuales de la misma red virtual. Esta implementación se realiza por red virtual, no por suscripción o cuenta, ni por máquina virtual.

Hay dos formas de crear un recurso de host de Bastion:

* Crear un recurso de Bastion mediante Azure Portal.
* Crear un recurso de Bastion en Azure Portal mediante la configuración de máquina virtual existente.

## <a name="before-you-begin"></a>Antes de empezar

Bastion está disponible en las regiones públicas de Azure siguientes:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Creación de un host de Bastion

Esta sección le ayuda a crear un recurso de Azure Bastion desde Azure Portal.

1. En el menú [Azure Portal](https://portal.azure.com) o en la página **Inicio**, seleccione **Crear un recurso**.

1. En la página **Nuevo**, en el campo *Buscar en Marketplace*, escriba **Bastion** y haga clic en **Entrar** para ir a los resultados de la búsqueda.

1. En los resultados, haga clic en **Bastion**. Asegúrese de que el publicador es *Microsoft* y la categoría es *Redes*.

1. En la página **Bastion**, haga clic en **Crear** para abrir la página **Crear un bastión**.

1. En la página **Create a bastion** (Crear un recurso de Bastion), configure un nuevo recurso de Bastion. Especifique las opciones de configuración del recurso de Bastion.

    ![Creación de un recurso de Bastion](./media/bastion-create-host-portal/settings.png)

    * **Suscripción**: suscripción de Azure que quiere usar para crear un recurso de Bastion.
    * **Grupo de recursos**: grupo de recursos en el que se creará el recurso de Bastion. Si no tiene un grupo de recursos existente, puede crear uno.
    * **Nombre**: nombre del nuevo recurso de Bastion.
    * **Región**: región pública de Azure en la que se creará el recurso.
    * **Red virtual**: red virtual en la que se creará el recurso de Bastion. Puede crear una red virtual en el portal durante este proceso, en caso de que no la tenga o no quiera usarla. Si usa una red virtual existente, asegúrese de que tenga suficiente espacio de direcciones libre para adaptarse a los requisitos de subred de Bastion.
    * **Subred**: subred de la red virtual en la que se implementará el nuevo recurso de host de Bastion. Debe crear una subred con el valor de nombre **AzureBastionSubnet**. Este valor permite a Azure saber en qué subred se deben implementar los recursos de Bastion. Esto no es lo mismo que una subred de puerta de enlace. Debe usar una subred de al menos /27 o una subred más grande (/27, /26, etc.). Cree **AzureBastionSubnet** sin ninguna delegación ni tabla de rutas. Cuando use los grupos de seguridad de red en **AzureBastionSubnet**, consulte [Trabajar con Grupos de seguridad de red](bastion-nsg.md).
    * **Dirección IP pública**: dirección IP pública del recurso de Bastion en la que se accederá a RDP/SSH (a través del puerto 443). Cree una dirección IP pública o use una existente. La dirección debe estar en la misma región que el recurso de Bastion que está creando.
    * **Nombre de dirección IP pública**: nombre del recurso de la dirección IP pública.
    * **SKU de la dirección IP pública**: está rellenada previamente de forma predeterminada como **Estándar**. Azure Bastion solo usa o admite la SKU de IP pública estándar.
    * **Asignación**: está rellenada previamente de forma predeterminada como **Estática**.

1. Cuando termine de especificar la configuración, haga clic en **Revisar + Crear**. Esto valida los valores. Una vez que se apruebe la validación, podrá comenzar el proceso de creación.
1. En la página para crear un recurso de Bastion, haga clic en **Crear**.
1. Verá un mensaje en el que se le indica que la implementación está en curso. El estado se mostrará en esta página a medida que se creen los recursos. El recurso de Bastion tardará aproximadamente cinco minutos en crearse e implementarse.

## <a name="createvmset"></a>Creación de un host de Bastion con la configuración de máquina virtual

Si crea un host de Bastion en el portal mediante una máquina virtual existente, varias opciones de configuración tendrán automáticamente como valores predeterminados los de la máquina virtual o la red virtual.

1. Abra [Azure Portal](https://portal.azure.com). Vaya a la máquina virtual y, a continuación, haga clic en **Conectar**.

   ![Conexión de la máquina virtual](./media/bastion-create-host-portal/vmsettings.png)
1. En la barra lateral derecha, haga clic en **Bastion** y en **Use Bastion** (Usar Bastion).

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. En la página de Bastion, rellene los campos de configuración siguientes:

   * **Nombre**: nombre del host de Bastion que quiere crear.
   * **Subred**: subred de la red virtual en la que se implementará el recurso de Bastion. La subred debe crearse con el nombre **AzureBastionSubnet**. De este modo, Azure sabe en qué subred se debe implementar el recurso de Bastion. Esto no es lo mismo que una subred de puerta de enlace. Haga clic en **Administrar configuración de subred** para crear la subred de Azure Bastion. Recomendamos encarecidamente que use al menos una subred /27 o mayor (/27, /26, etc.). Cree la subred **AzureBastionSubnet** sin grupos de seguridad de red, tablas de rutas ni delegaciones. Haga clic en **Crear** para crear la subred y, después, siga configurando los valores siguientes.
   * **Dirección IP pública**: dirección IP pública del recurso de Bastion en la que se accederá a RDP/SSH (a través del puerto 443). Cree una dirección IP pública o use una existente. La dirección debe estar en la misma región que el recurso de Bastion que está creando.
   * **Nombre de dirección IP pública**: nombre del recurso de la dirección IP pública.
1. En la pantalla de validación, haga clic en **Crear**. El recurso de Bastion tardará aproximadamente cinco minutos en crearse e implementarse.

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).