---
title: Uso de Terraform para crear una máquina virtual Linux completa en Azure
description: Aprenda a usar Terraform para crear un entorno completo de máquina virtual Linux y administrarla en Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: 819aeb225c4f55f803a5fad19eff33bd1748bf46
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75368936"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Creación de una infraestructura completa de máquina virtual Linux en Azure con Terraform

Terraform permite definir y crear implementaciones completas de infraestructura de Azure. Se crean plantillas de Terraform en un formato legible para el usuario para crear y configurar recursos de Azure de forma coherente y reproducible. En este artículo se muestra cómo crear un entorno completo de Linux y recursos de apoyo con Terraform. Aprenda también a [instalar y configurar Terraform](terraform-install-configure.md).

> [!NOTE]
> Para obtener soporte técnico específico de Terraform, póngase en contacto directamente con Terraform mediante uno de sus canales de la comunidad:
>
>   • La [sección de Terraform](https://discuss.hashicorp.com/c/terraform-core) del portal de la comunidad contiene preguntas, casos de uso y patrones útiles.
>
>   • Para preguntas relacionadas con el proveedor, visite la sección de [proveedores de Terraform](https://discuss.hashicorp.com/c/terraform-providers) del portal de la comunidad.


## <a name="create-azure-connection-and-resource-group"></a>Creación de la conexión y el grupo de recursos de Azure

Analicemos las secciones de las plantillas de Terraform. También puede ver la versión completa de la [plantilla de Terraform](#complete-terraform-script) para copiarla y pegarla.

En la sección `provider` se indica a Terraform que utilice un proveedor de Azure. Para obtener valores para *subscription_id*, *client_id*, *client_secret* y *tenant_id*, consulte el artículo de [instalación y configuración de Terraform](terraform-install-configure.md). 

> [!TIP]
> Si crea las variables de entorno para los valores o está usando la [experiencia de Bash de Azure Cloud Shell](/azure/cloud-shell/overview), no tendrá que incluir las declaraciones de variables en esta sección.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

En la sección siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

En las secciones adicionales, se hace referencia al grupo de recursos con *${azurerm_resource_group.myterraformgroup.name}* .

## <a name="create-virtual-network"></a>Creación de una red virtual
Con la siguiente sección se crea una red virtual llamada *myVnet* en el espacio de direcciones *10.0.0.0/16*:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

En la siguiente sección se crea una subred denominada *mySubnet* en la red virtual *myVnet*:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Creación de una dirección IP pública
Para acceder a recursos de Internet, cree una dirección IP pública y asígnela a la VM. Con la siguiente sección se crea una dirección IP pública llamada *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Creación de un grupo de seguridad de red
Los grupos de seguridad de red controlan el flujo del tráfico de red entrada y salida de la VM. Con la siguiente sección se crea un grupo de seguridad de red llamado *myNetworkSecurityGroup* y se define una regla para permitir el tráfico de SSH por el puerto TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Creación de una tarjeta de interfaz de red virtual
Las tarjetas de interfaz de red (NIC) virtuales conectan la VM a una red virtual determinada, una dirección IP pública y un grupo de seguridad de red. Con la siguiente sección de una plantilla de Terraform se crea un NIC virtual llamado *myNIC* que se conecta a los recursos de red virtual que se hayan creado:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    network_security_group_id   = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Creación de una cuenta de almacenamiento para el diagnóstico
Para almacenar el diagnóstico de arranque para una máquina virtual, se necesita una cuenta de almacenamiento. Estos diagnósticos de arranque pueden ayudarle a solucionar problemas y a supervisar el estado de la máquina virtual. La cuenta de almacenamiento que se crea solo sirve para almacenar datos de diagnóstico de arranque. Como las cuentas de almacenamiento deben tener un nombre único, con la siguiente sección se genera un texto aleatorio:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Ahora puede crear una cuenta de almacenamiento. Con la siguiente sección se crea una cuenta de almacenamiento, con el nombre según el texto aleatorio generado en el paso anterior:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Crear máquina virtual

El último paso es crear una máquina virtual y usar todos los recursos creados. Con la siguiente sección se crea una máquina virtual *myVM* y se conecta la NIC virtual *myNIC*. Se utiliza la imagen de *Ubuntu 16.04-LTS* más reciente y se crea un usuario llamado *azureuser* con la autenticación de contraseña deshabilitada.

 Los datos de la clave SSH se proporcionan en la sección *ssh_keys*. Proporcione una clave SSH pública válida en el campo *key_data*.

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Script completo de Terraform

Para reunir todas estas secciones y ver Terraform en acción, cree un archivo denominado *terraform_azure.tf* y pegue el siguiente contenido:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Compilación e implementación de la infraestructura
Una vez creada la plantilla de Terraform, el primer paso es inicializar Terraform. Este paso garantiza que Terraform cumple todos los requisitos previos para compilar la plantilla en Azure.

```bash
terraform init
```

El siguiente paso es que Terraform revise y valide la plantilla. En este paso se comparan los recursos solicitados con la información de estado que guarda Terraform y se genera la ejecución planeada. Los recursos *no* se crean en Azure.

```bash
terraform plan
```

Después de ejecutar el comando anterior, debería ver una pantalla similar a la siguiente:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Si todo parece correcto y está listo para compilar la infraestructura de Azure, aplique la plantilla en Terraform:

```bash
terraform apply
```

Cuando Terraform finalice, la infraestructura de máquina virtual estará lista. Obtenga la dirección IP pública de la máquina virtual con [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Después, puede acceder mediante SSH a la VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Pasos siguientes
Ha creado una infraestructura básica en Azure mediante Terraform. Para escenarios más complejos, incluidos ejemplos sobre cómo usar equilibradores de carga y conjuntos de escalado de máquinas virtuales, consulte los diversos [ejemplos de Terraform para Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para una lista actualizada de los proveedores admitidos de Azure, consulte la [documentación de Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
