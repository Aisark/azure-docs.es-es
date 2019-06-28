---
title: Configuración de la GPU para Windows Virtual Desktop, versión preliminar - Azure
description: Cómo habilitar la representación y codificación de aceleración por GPU en la versión preliminar de Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159576"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configuración de la aceleración de la unidad de procesamiento de gráficos (GPU) para Windows Virtual Desktop, versión preliminar

La versión preliminar de Windows Virtual Desktop admite la representación y codificación de aceleración por GPU para mejorar el rendimiento y la escalabilidad de las aplicaciones. La aceleración de la GPU es especialmente importante para las aplicaciones que contienen muchos gráficos.

Siga las instrucciones de este artículo para crear una máquina virtual de Azure optimizada para GPU, agregarla al grupo host y configurarla para usar la aceleración de GPU para la representación y la codificación. En este artículo se da por supuesto que ya tiene configurado un inquilino de Windows Virtual Desktop.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selección de un tamaño de máquina virtual de Azure optimizada para GPU

Azure ofrece varios [tamaños de máquinas virtuales optimizadas para GPU](/azure/virtual-machines/windows/sizes-gpu). La elección correcta para el grupo host depende de una serie de factores, incluidas las cargas de trabajo de la aplicación en cuestión, la calidad de la experiencia del usuario deseada y el costo. En general, las GPU más grandes y con más capacidad ofrecen una experiencia mejor para una determinada densidad de usuarios.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creación de un grupo host, aprovisionamiento de la máquina virtual y configuración de un grupo de aplicaciones

Cree un nuevo grupo host con una máquina virtual del tamaño que ha seleccionado. Para obtener instrucciones, consulte: [Tutorial: Creación de un grupo host con Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

La versión preliminar de Windows Virtual Desktop admite la representación y la codificación de aceleración por GPU en los siguientes sistemas operativos:

* Windows 10, versión 1511 o posterior
* Windows Server 2016 o posterior

También debe configurar un grupo de aplicaciones o usar el grupo de aplicaciones de escritorio predeterminado (denominado "Grupo de aplicaciones de escritorio") que se crea automáticamente cuando se crea un nuevo grupo host. Para obtener instrucciones, consulte: [Tutorial: Administración de grupos de aplicaciones para la versión preliminar de Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>La versión preliminar de Windows Virtual Desktop solo admite el tipo de grupo de aplicaciones "Escritorio" para los grupos host habilitados para GPU. No se admiten los grupos de aplicaciones del tipo "RemoteApp" para los grupos host habilitados para GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalación de los controladores de gráficos admitidos en la máquina virtual

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure en la versión preliminar de Windows Virtual Desktop, debe instalar los controladores de gráficos NVIDIA. Siga las instrucciones de [Instalación de controladores de GPU NVIDIA en máquinas virtuales de la serie N que ejecutan Windows](/azure/virtual-machines/windows/n-series-driver-setup) para instalar controladores, ya sea manualmente o mediante la [extensión de controlador de GPU de NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Tenga en cuenta que, para la versión preliminar de Windows Virtual Desktop, solo se admiten los [controladores GRID de NVIDIA](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuidos por Azure.

Tras instalar los controladores, es necesario reiniciar la máquina virtual. Utilice los pasos de comprobación en las instrucciones anteriores para confirmar que los controladores de gráficos se han instalado correctamente.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configuración de la representación de aplicaciones de aceleración por GPU

De forma predeterminada, las aplicaciones y los escritorios que se ejecutan en configuraciones multisesión se representan mediante la CPU y no aprovechan las GPU disponibles para la representación. Configure la directiva de grupo para el host de sesión para habilitar la representación de aceleración por GPU:

1. Conéctese al escritorio de la máquina virtual mediante una cuenta con privilegios de administrador local.
2. Abra el menú Inicio y escriba "gpedit.msc" para abrir el Editor de directivas de grupo.
3. Desplácese por el árbol hasta **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Servicios de escritorio remoto** > **Host de sesión de escritorio remoto** > **Entorno de sesión remota**.
4. Seleccione la directiva **Utilizar el adaptador de gráficos de hardware predeterminado para todas las sesiones de Servicios de Escritorio remoto** y establezca esta directiva en **Habilitada** para habilitar la representación mediante GPU en la sesión remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configuración de la codificación de marcos de aceleración por GPU

El Escritorio remoto codifica todos los gráficos que representan las aplicaciones y los escritorios (tanto si se representan mediante GPU como si lo hacen mediante CPU) para la transmisión a los clientes de Escritorio remoto. De forma predeterminada, el Escritorio remoto no aprovecha las GPU disponibles para esta codificación. Configure la directiva de grupo para el host de sesión para habilitar la codificación de marcos de aceleración por GPU. Continúe con los pasos anteriores:

1. Seleccione la directiva **Priorizar el modo de gráficos H.264/AVC 444 para las conexiones de Escritorio remoto** y establezca esta directiva en **Habilitada** para aplicar el códec H.264/AVC 444 en la sesión remota.
2. Seleccione la directiva **Configurar la codificación de hardware H.264/AVC para las conexiones de Escritorio remoto** y establezca esta directiva en **Habilitada** para habilitar la codificación de hardware para AVC/H.264 en la sesión remota.

    >[!NOTE]
    >En Windows Server 2016, establezca la opción **Preferir la codificación de hardware AVC** en **Intentar siempre**.

3. Ahora que se han editado las directivas de grupo, fuerce una actualización de las directivas de grupo. Abra el símbolo del sistema y escriba:

    ```batch
    gpupdate.exe /force
    ```

4. Cierre sesión en la sesión de Escritorio remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Comprobación de la representación de aplicaciones de aceleración por GPU

Para comprobar que las aplicaciones usan la GPU para la representación, lleve a cabo cualquiera de las siguientes acciones:

* Use la utilidad `nvidia-smi` tal y como se describe en [Comprobar la instalación del controlador](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para comprobar la utilización de la GPU al ejecutar las aplicaciones.
* En las versiones de sistema operativo admitidas, puede usar el Administrador de tareas para comprobar la utilización de la GPU. Seleccione la GPU en la pestaña "Rendimiento" para ver si las aplicaciones usan la GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Comprobación de la codificación de marcos de aceleración por GPU

Para comprobar que Escritorio remoto utiliza la codificación de aceleración por GPU:

1. Conéctese al escritorio de la máquina virtual mediante el cliente de Windows Virtual Desktop.
2. Inicie el Visor de eventos y vaya hasta el siguiente nodo: **Registros de aplicaciones y servicios** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** >  **Operativo**
3. Para determinar si se utiliza la codificación de aceleración por GPU, busque el id. de evento 170. Si ve "Codificador de hardware AVC habilitado: 1", significa que se usa la codificación por GPU.
4. Para determinar si se usa el modo de AVC 444, busque el id. de evento 162. Si ve "AVC disponible: 1 perfil inicial: 2048", significa que se usa AVC 444.

## <a name="next-steps"></a>Pasos siguientes

Con estas instrucciones debería poder configurar y ejecutar la aceleración por GPU en una máquina virtual de un host de sesión. A continuación se indican algunas consideraciones adicionales para habilitar la aceleración por GPU en un grupo host más grande:

* Considere la posibilidad de usar la [Extensión de controlador de GPU de NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para simplificar la instalación de controladores y las actualizaciones en múltiples máquinas virtuales.
* Considere la posibilidad de usar la directivas de Active Directory para simplificar la configuración de directivas de grupo en varias máquinas virtuales. Para obtener información sobre cómo implementar la directiva de grupo en el dominio de Active Directory, consulte [Trabajar con objetos de directiva de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
