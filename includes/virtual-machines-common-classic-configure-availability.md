---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485382"
---
Un conjunto de disponibilidad ayuda a mantener las máquinas virtuales disponibles durante períodos de inactividad, como por ejemplo mientras se realiza mantenimiento. Colocar dos o más máquinas virtuales similarmente configuradas en un conjunto de disponibilidad crea la redundancia necesaria para mantener la disponibilidad de las aplicaciones o servicios que ejecuta su máquina virtual. Para obtener información detallada acerca de su funcionamiento, consulte [Administración de la disponibilidad de las máquinas virtuales Windows en Azure][Manage the availability of virtual machines].

Es una buena práctica usar una combinación de conjuntos de disponibilidad y extremos de equilibrio de carga para ayudar a asegurar de que su aplicación está siempre disponible y ejecutándose de forma eficaz. Para obtener información detallada acerca de los puntos de conexión de carga equilibrada, consulte [Load balancing for Azure infrastructure services][Load balancing for Azure infrastructure services] (Equilibrio de carga en los servicios de infraestructura de Azure).

Puede agregar máquinas virtuales clásicas en un conjunto de disponibilidad mediante una de estas dos opciones:

* [Opción 1: Crear una máquina virtual y un conjunto de disponibilidad al mismo tiempo][Option 1: Create a virtual machine and an availability set at the same time]. A continuación, agregue nuevas máquinas virtuales al conjunto cuando cree en dichas máquinas virtuales.
* [Opción 2: Agregar una máquina virtual existente a un conjunto de disponibilidad][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> En el modelo clásico, las máquinas virtuales que desee poner en el mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.
> 
> 

## <a id="createset"> </a>Opción 1: Crear una máquina virtual y un conjunto de disponibilidad al mismo tiempo
Puede usar el Portal de Azure o los comandos de Azure PowerShell para ello.

Para utilizar el Portal de Azure:

1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Crear un recurso** > **Proceso**.
3. Seleccione la imagen de máquina virtual de Marketplace que desee usar. Puede crear una máquina virtual con Linux o con Windows.
4. En la máquina virtual seleccionada, compruebe que el modelo de implementación está establecido en **Clásico** y, luego, haga clic en **Crear**
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Escriba un nombre de máquina virtual, un nombre de usuario y una contraseña (para máquinas con Windows) o la clave pública SSH (para equipos con Linux). 
6. Elija un tamaño de máquina virtual y, luego, haga clic en **Seleccionar** para continuar.
7. Elija **Configuración opcional > Conjunto de disponibilidad** y seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Revise las opciones de configuración. Cuando haya terminado, haga clic en **Crear**.
9. Mientras Azure crea la máquina virtual, puede realizar el seguimiento del progreso desde el menú del concentrador, en **Máquinas virtuales** .

Para usar los comandos de Azure PowerShell para crear una máquina virtual de Azure y agregarla a un conjunto de disponibilidad nuevo o existente, consulte [Creación de una máquina virtual de Windows con PowerShell y el modelo de implementación clásica](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Opción 2: Agregar una máquina virtual existente a un conjunto de disponibilidad
En el Portal de Azure, puede agregar máquinas virtuales clásicas existentes a un conjunto de disponibilidad existente o crear uno nuevo para ellas. (Recuerde que las máquinas virtuales del mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.) Estos pasos son prácticamente los mismos. Con Azure PowerShell, puede agregar la máquina virtual a un conjunto de disponibilidad existente.

1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Máquinas virtuales (clásico)**.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. En la lista de máquinas virtuales, seleccione el nombre de la máquina virtual que quiera agregar al conjunto.
4. Elija **Conjunto de disponibilidad** en la opción **Configuración** de la máquina virtual.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual. La máquina virtual debe pertenecer al mismo servicio en la nube que el conjunto de disponibilidad.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Haga clic en **Save**(Guardar).

Para utilizar los comandos de Azure PowerShell, abra una sesión de Azure PowerShell con nivel de administrador y ejecute el siguiente comando. En el caso de los marcadores de posición (como &lt;VmCloudServiceName&gt;), reemplace todo el contenido que esté entre comillas, lo que incluye los caracteres < y >, por los nombres correctos.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Es posible que haya que reiniciar la máquina virtual para terminar de agregarla al conjunto de disponibilidad.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

