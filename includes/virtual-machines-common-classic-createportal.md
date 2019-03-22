---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113955"
---
Una máquina virtual *personalizada* significa simplemente una máquina virtual que se crea mediante una **aplicación destacada** de **Marketplace** porque gran parte del trabajo lo hace ella. Aun así, puede elegir opciones de configuración que incluyan los siguientes elementos:

* Conectar la máquina virtual a una red virtual.
* Instalar el agente de máquina virtual de Azure y las extensiones de máquina virtual de Azure, como para antimalware.
* Agregar la máquina virtual a servicios en la nube existentes.
* Agregar la máquina virtual a una cuenta de almacenamiento existente.
* Agregar la máquina virtual a un conjunto de disponibilidad.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Si quiere que su máquina virtual use una red virtual, asegúrese de especificarla cuando cree la máquina virtual.
> 
> * Dos de las ventajas de usar una red virtual son la conexión directa a la máquina virtual y la configuración de conexiones entre locales.
> 
> * Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener detalles sobre las redes virtuales, consulte [Información general sobre Azure Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

## <a name="to-create-the-virtual-machine"></a>Para crear la máquina virtual
