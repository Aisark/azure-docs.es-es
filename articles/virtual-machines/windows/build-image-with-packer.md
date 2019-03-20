---
title: Creación de imágenes de VM de Windows con Packer en Azure | Microsoft Docs
description: Aprenda a usar Packer para crear imágenes de máquinas virtuales Windows en Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: f768582e8ef32bc654a2f797c5c7a481a26fb643
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734190"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Uso de Packer para crear imágenes de máquinas virtuales Windows en Azure
Cada máquina virtual (VM) en Azure se crea a partir de una imagen que define la distribución de Windows y la versión del sistema operativo. Las imágenes pueden incluir configuraciones y aplicaciones preinstaladas. Azure Marketplace proporciona muchas imágenes propias y de terceros para los entornos de aplicaciones y sistemas operativos más comunes, pero también puede crear sus propias imágenes personalizadas adaptadas a sus necesidades. En este artículo se detalla cómo utilizar la herramienta de código abierto [Packer](https://www.packer.io/) para definir y crear imágenes personalizadas en Azure.

En este artículo se ha probado por última vez en 21/2/2019 utilizando el [módulo de PowerShell de Az](https://docs.microsoft.com/powershell/azure/install-az-ps) versión 1.3.0 y [Packer](https://www.packer.io/docs/install/index.html) versión 1.3.4.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-azure-resource-group"></a>Creación del grupo de recursos de Azure
Durante el proceso de compilación, Packer crea recursos de Azure temporales mientras genera la máquina virtual de origen. Para capturar dicha máquina virtual para usarla como imagen, debe definir un grupo de recursos. La salida del proceso de compilación de Packer se almacena en este grupo de recursos.

Cree un grupo de recursos con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurepowershell
$rgName = "mypackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Creación de credenciales de Azure
Packer se autentica con Azure mediante una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como Packer. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure.

Cree una entidad de servicio con [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) y asigne permisos para que la entidad de servicio cree y administre recursos con [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). El valor de `-DisplayName` debe ser único; reemplace por su propio valor según sea necesario.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A continuación, genera el identificador de aplicación y la contraseña.

```powershell
$plainPassword
$sp.ApplicationId
```


Para autenticarse en Azure, también tendrá que obtener los identificadores de suscripción e inquilino de Azure con [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definición de la plantilla de Packer
Para crear imágenes, es preciso crear una plantilla en forma de archivo JSON. En la plantilla, se definen los generadores y aprovisionadores que realizan el proceso de creación real. Packer tiene un [generador para Azure](https://www.packer.io/docs/builders/azure.html) que permite definir los recursos de Azure, como las credenciales de la entidad de servicio creadas en el paso anterior.

Cree un archivo denominado *windows.json* y pegue el siguiente contenido. Escriba sus propios valores para los siguientes elementos:

| Parámetro                           | Dónde se obtiene |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Vea el identificador de la entidad de servicio con `$sp.applicationId` |
| *client_secret*                     | Ver la contraseña generada automáticamente con `$plainPassword` |
| *tenant_id*                         | Salida del comando `$sub.TenantId` |
| *subscription_id*                   | Salida del comando `$sub.SubscriptionId` |
| *managed_image_resource_group_name* | Nombre del grupo de recursos que creó en el primer paso |
| *managed_image_name*                | Nombre de la imagen de disco administrado que se crea |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Esta plantilla crea una máquina virtual de Windows Server 2016, instala IIS y generaliza la máquina virtual con Sysprep. La instalación de IIS muestra cómo puede usar el aprovisionador de PowerShell para ejecutar comandos adicionales. La imagen final de Packer incluye así la instalación y configuración del software necesario.


## <a name="build-packer-image"></a>Creación de una imagen de Packer
Si Packer aún no está instalado en el equipo local, de compresor [siga las instrucciones de instalación de Packer](https://www.packer.io/docs/install/index.html).

Compilar la imagen, abra un símbolo del sistema y especifique su Packer archivo de plantilla como sigue:

```
./packer build windows.json
```

A continuación puede ver un ejemplo del resultado de los comandos anteriores:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer tarda unos minutos en crear la máquina virtual, ejecutar los aprovisionadores y limpiar la implementación.


## <a name="create-a-vm-from-the-packer-image"></a>Creación de una máquina virtual a partir de la imagen de Packer
Ya puede crear una VM a partir de la imagen con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Si aún no existen, se crean los recursos de red complementarios. Cuando se le solicite, escriba un nombre de usuario administrativo y una contraseña que se crearán en la máquina virtual. En el ejemplo siguiente se crea una máquina virtual denominada *myVM* a partir de *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Si desea crear máquinas virtuales en un grupo de recursos o una región distintos a los de la imagen de Packer, especifique el identificador de la imagen en lugar de su nombre. Puede obtener el identificador de imagen con [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

La operación de creación de la máquina virtual a partir de la imagen de Packer tarda unos minutos.


## <a name="test-vm-and-webserver"></a>Prueba de la máquina virtual y el servidor web
Obtenga la dirección IP pública de la VM con [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de *myPublicIP* que se ha creado anteriormente:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Para ver en acción la máquina virtual, que incluye la instalación de IIS desde el aprovisionador de Packer, escriba la dirección IP pública en un explorador web.

![Sitio predeterminado de IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, ha utilizado Packer para crear una imagen de máquina virtual con IIS instalado. Puede usar esta imagen de máquina virtual junto con flujos de trabajo de implementación existentes, como implementar la aplicación en máquinas virtuales creadas a partir de la imagen con Azure DevOps Services, Ansible, Chef o Puppet.

Para ver más plantillas de Packer de ejemplo para otras distribuciones de Windows, consulte [este repositorio de GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
