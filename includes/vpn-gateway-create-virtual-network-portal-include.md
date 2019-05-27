---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d35da4f1eaed91411c015ed7665944d886f9d79c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170093"
---
Para crear una red virtual con el modelo de implementación de Resource Manager mediante Azure Portal, siga los pasos que se indican a continuación. Si va a utilizar estos pasos como tutorial, use los [valores de ejemplo](#values). Si no va a hacerlo, asegúrese de reemplazar los valores por los suyos. Para más información sobre redes virtuales, consulte [Información general sobre redes virtuales](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para que esta red virtual se conecte a una ubicación local, debe coordinarse con el administrador de la red local para delimitar un intervalo de direcciones IP que pueda usar específicamente para esta red virtual. Si existe un intervalo de direcciones duplicado en ambos lados de la conexión VPN, el tráfico no se enrutará como cabría esperar. Además, si desea conectar esta red virtual a otra red virtual, el espacio de direcciones no se puede superponer con otra red virtual. Por consiguiente, tenga cuidado al planear la configuración de red.
>
>

1. Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. Haga clic en **Crear un recurso**. En el campo **Buscar en el Marketplace**, escriba "Red virtual". En la lista de resultados, busque **Virtual Network** y haga clic para abrir la página **Virtual Network**.
3. En la parte inferior de la página Virtual Network, en la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y, finalmente, haga clic en **Crear**. Se abre la página "Crear red virtual".

   ![Página Creación de una red virtual](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "The Create virtual network page")
4. En la página **Crear red virtual**, configure los valores de la red virtual. Al rellenar los campos, el signo de exclamación rojo se convierte en una marca de verificación verde cuando los caracteres escritos en el campo sean válidos.

   - **Nombre**: escriba el nombre de la red virtual. En este ejemplo, se usa VNet1.
   - **Espacio de direcciones**: escriba el espacio de direcciones. Si tiene varios espacios de direcciones que agregar, agregue su primer espacio de direcciones. Podrá agregar más espacios de direcciones posteriormente, tras crear la red virtual. Asegúrese de que el espacio de direcciones que especifique no se superpone con el espacio de direcciones para la ubicación local.
   - **Suscripción**: compruebe que la suscripción que aparece en la lista es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.
   - **Grupo de recursos**: seleccione un grupo de recursos existente, o bien cree uno nuevo y escriba su nombre. Si va a crear un nuevo grupo, dé un nombre al grupo de recursos según los valores de configuración planeados. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   - **Ubicación**: seleccione la ubicación de la red virtual. La ubicación determina dónde van a residir los recursos que se implementen en esta red virtual.
   - **Subred**: agregue el nombre de la primera subred y el intervalo de direcciones de la subred. Más adelante, después de crear esta red virtual, podrá agregar más subredes y la subred de la puerta de enlace. 

5. Seleccione **Anclar al panel** si desea encontrar la red virtual fácilmente en el panel y, luego, haga clic en **Crear**. Después de hacer clic en **Crear**, verá un icono en el panel que reflejará el progreso de la red virtual. El icono cambiará a medida que se vaya creando la red virtual.
