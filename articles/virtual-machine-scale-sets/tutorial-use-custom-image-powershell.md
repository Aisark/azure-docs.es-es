---
title: 'Tutorial: Uso de una imagen de máquina virtual personalizada en un conjunto de escalado con Azure PowerShell'
description: Obtenga información sobre cómo usar Azure PowerShell para crear una imagen de máquina virtual personalizada que puede usar para implementar un conjunto de escalado de máquinas virtuales
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4f47c4118db9d5fc799193f4abeea142c74ec691
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551580"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Creación y uso de una imagen personalizada para conjuntos de escalado de máquinas virtuales con Azure PowerShell

Al crear el conjunto de escalado, se especifica la imagen que se usará cuando se implementen las instancias de máquina virtual. Para reducir el número de tareas después de implementar las instancias de máquina virtual, puede usar una imagen de máquina virtual personalizada. Esta imagen de máquina virtual personalizada incluye la instalación o configuración de las aplicaciones necesarias. Las instancias de máquina virtual creadas en el conjunto de escalado usan la imagen de máquina virtual personalizada y están listas para atender el tráfico de la aplicación. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada a partir de la máquina virtual de origen
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Creación y configuración de una máquina virtual de origen

>[!NOTE]
> Este tutorial le guía por el proceso de creación y uso de una imagen de máquina virtual generalizada. No se admite la creación de un conjunto de escalado a partir de un disco duro virtual especializado.

Primero, cree un grupo de recursos con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) y, después, cree una máquina virtual con [New-AzVM](/powershell/module/az.compute/new-azvm). Esta máquina virtual se usará después como origen para la imagen de máquina virtual personalizada. En el ejemplo siguiente, se crea una máquina virtual llamada *myCustomVM* en el grupo de recursos llamado *myResourceGroup*. Cuando se le solicite, escriba el nombre de usuario y la contraseña que se usarán como credenciales de inicio de sesión para la máquina virtual:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Para conectarse a la máquina virtual, obtenga la dirección IP pública con [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), tal y como se indica a continuación:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Cree una conexión remota con la máquina virtual. Si se utiliza Azure Cloud Shell, realice este paso desde un símbolo del sistema local de PowerShell o un cliente de Escritorio remoto. Proporcione su propia dirección IP mediante el comando anterior. Cuando se le solicite, escriba las credenciales que usó al crear la máquina virtual en el primer paso:

```powershell
mstsc /v:<IpAddress>
```

Para personalizar la máquina virtual, vamos a instalar un servidor web básico. Cuando la instancia de máquina virtual del conjunto de escalado se implemente, tendrá todos los paquetes necesarios para ejecutar una aplicación web. Abra un símbolo de PowerShell local en la máquina virtual e instale el servidor web de IIS con [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) como se indica a continuación:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

El último paso para preparar la máquina virtual para su uso como una imagen personalizada es generalizar la máquina virtual. Sysprep quita todas las configuraciones y la información de la cuenta personal y restablece la máquina virtual a un estado limpio para futuras implementaciones. Para más información, consulte [Uso de Sysprep: Introducción](https://technet.microsoft.com/library/bb457073.aspx).

Para generalizar la máquina virtual, ejecute Sysprep y prepare la máquina virtual para poder usarla sin necesidad de configuraciones adicionales. Cuando termine, indique a Sysprep que apague la máquina virtual:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

La conexión remota a la máquina virtual se cierra automáticamente cuando termina el proceso de Sysprep y se apaga la máquina virtual.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Crear una imagen de máquina virtual personalizada a partir de la máquina virtual de origen
La máquina virtual de origen ahora está personalizada con el servidor web de IIS instalado. Vamos a crear la imagen de máquina virtual personalizada para usarla con un conjunto de escalado.

Para crear una imagen, es necesario desasignar la máquina virtual. Desasigne la máquina virtual con [Stop-AzVm](/powershell/module/az.compute/stop-azvm). A continuación, establezca el estado de la máquina virtual como generalizado con [Set-AzVm](/powershell/module/az.compute/set-azvm) para que la plataforma Azure sepa que la máquina virtual está preparada para usarse como imagen personalizada. Solo se puede crear una imagen de una máquina virtual generalizada:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

La desasignación y generalización de la máquina virtual puede tardar unos minutos.

Ahora, cree una imagen de la máquina virtual mediante [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) y [New-AzImage](/powershell/module/az.compute/new-azimage). En el ejemplo siguiente se crea una imagen llamada *myImage* a partir de la máquina virtual:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```

## <a name="configure-the-network-security-group-rules"></a>Configuración de reglas del grupo de seguridad de red
Antes de crear el conjunto de escalado, es necesario configurar las reglas del grupo de seguridad de red asociadas para permitir el acceso a HTTP, RDP y la comunicación remota. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$rule2 = New-AzNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389

$rule3 = New-AzNetworkSecurityRuleConfig -Name remoting-rule -Description "Allow PS Remoting" -Access Allow -Protocol Tcp -Direction Inbound -Priority 120 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 5985

New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName "myResourceGroup" -Location "EastUS" -SecurityRules $rule1,$rule2,$rule3
```

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Creación de un conjunto de escalado a partir de la imagen de máquina virtual personalizada
Ahora, cree un conjunto de escalado con [New-AzVmss](/powershell/module/az.compute/new-azvmss), que usa el parámetro `-ImageName` para definir la imagen de máquina virtual personalizada creada en el paso anterior. Para distribuir el tráfico a las instancias individuales de VM, también se crea un equilibrador de carga. El equilibrador de carga incluye reglas para distribuir el tráfico en el puerto TCP 80, y permitir el tráfico de Escritorio remoto en el puerto TCP 3389 y la conexión remota de PowerShell en el puerto TCP 5985. Cuando se le solicite, proporcione sus propias credenciales administrativas para las instancias de máquina virtual en el conjunto de escalado:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNSG"
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.


## <a name="test-your-scale-set"></a>Prueba del conjunto de escalado
Para ver el conjunto de escalado en acción, obtenga la dirección IP pública del equilibrador de carga con [Get-AzPublicIPAddress](/powershell/module/az.network/Get-AzPublicIpAddress) como se muestra a continuación:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Escriba la dirección IP pública en un explorador web. Se muestra la página web predeterminada de IIS, como en el ejemplo siguiente:

![IIS se ejecuta desde la imagen de máquina virtual personalizada](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Para quitar el conjunto de escalado y los recursos adicionales, elimine el grupo de recursos y todos sus recursos con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). El parámetro `-Force` confirma que desea eliminar los recursos sin pedir confirmación adicional. El parámetro `-AsJob` devuelve el control a la petición de confirmación sin esperar a que finalice la operación.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a crear y utilizar una imagen de máquina virtual personalizada para los conjuntos de escalado con Azure PowerShell:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Vaya al siguiente tutorial para aprender cómo implementar aplicaciones en el conjunto de escalado.

> [!div class="nextstepaction"]
> [Implementación de aplicaciones en los conjuntos de escalado](tutorial-install-apps-powershell.md)
