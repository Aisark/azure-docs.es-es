---
title: Configuración de direcciones IP privadas para máquinas virtuales (Azure Portal) | Microsoft Docs
description: Obtenga información sobre cómo configurar direcciones IP privadas para máquinas virtuales mediante Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: 31aeab946b9ad740e2f56eb1ecaafd3e76cc42b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723796"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configuración de direcciones IP privadas para una máquina virtual mediante Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [CLI de Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portal de Azure (clásico)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (clásico)](virtual-networks-static-private-ip-classic-ps.md)
> * [CLI de Azure (clásico)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes pasos de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los pasos que aparecen en este documento, cree primero el entorno de prueba descrito en [Creación de una red virtual](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Creación de una VM para probar las direcciones IP privadas estáticas
No se puede establecer una dirección IP privada estática durante la creación de una VM en el modo de implementación del Administrador de recursos mediante el Portal de Azure. En primer lugar debe crear la máquina virtual y luego establecer la IP privada como estática.

Para crear una máquina virtual denominada *DNS01* en la subred *FrontEnd* de una red virtual denominada *TestVNet*, siga estos pasos:

1. Desde un explorador, vaya a https://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Crear un recurso** > **Proceso** > **Windows Server 2012 R2 Datacenter**. Vea que la lista **Seleccionar un modelo de implementación** ya muestre **Resource Manager** y, después, haga clic en **Crear**, como se muestra en la figura siguiente.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. En el panel **Básico**, especifique el nombre de la máquina virtual que se va a crear (*DNS01* en el escenario), la cuenta de administrador local y la contraseña, como se muestra en la figura siguiente.
   
    ![Panel Básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Asegúrese de que la **Ubicación** seleccionada sea *Centro de EE. UU.* y, después, haga clic en **Seleccionar existente** en **Grupo de recursos**. Después, haga clic de nuevo en **Grupo de recursos**, luego en *TestRG* y, después, en **Aceptar**.
   
    ![Panel Básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. En el panel **Elegir un tamaño**, seleccione **A1 Estándar** y, después, haga clic en **Seleccionar**.
   
    ![Panel Elegir un tamaño](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. En el panel **Configuración**, asegúrese de que las propiedades están establecidas con los valores siguientes y, a continuación, haga clic en **Aceptar**.
   
    -**Cuenta de almacenamiento**: *vnetstorage*
   
   * **Network** (Red): *TestVNet*
   * **Subred**: *FrontEnd*
     
     ![Panel Elegir un tamaño](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. En el panel **Resumen**, haga clic en **Aceptar**. Observe que el icono siguiente aparece en el panel.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-portal.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private). No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática de la máquina virtual creada con los pasos anteriores, ejecute los siguientes pasos.

1. En Azure Portal, haga clic en **EXAMINAR TODO** > **Máquinas virtuales** > **DNS01** > **Toda la configuración** > **Interfaces de red** y, después, haga clic en la única interfaz de red que aparece.
   
    ![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. En el panel **Interfaz de red**, haga clic en **Toda la configuración** > **Direcciones IP** y vea los valores de **Asignación** y **Dirección IP**.
   
    ![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la máquina virtual creada con los pasos anteriores, siga estos pasos:

1. En el panel **Direcciones IP** que se muestra arriba, haga clic en **Estática** en **Asignación**.
2. Escriba *192.168.1.101* en **Dirección IP** y, después, haga clic en **Guardar**.
   
    ![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Si después de hacer clic en **Guardar** se da cuenta de que la asignación sigue establecida en **Dinámica**, significa que la dirección IP que ha escrito sigue estando en uso. Pruebe una dirección IP distinta.
> 
> 

Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-portal.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private). No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática de la máquina virtual creada anteriormente, siga este paso:

En el panel **Direcciones IP** que se muestra arriba, haga clic en **Dinámica** en **Asignación** y, después, haga clic en **Guardar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Configuración de direcciones IP en el sistema operativo

Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-portal.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure; de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private). No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la administración de la [configuración de la dirección IP privada](virtual-network-network-interface-addresses.md).

