---
title: Creación de conjuntos de escalado de máquinas virtuales de Azure mediante Ansible
description: Aprenda a usar Ansible para crear y configurar un conjunto de escalado de máquinas virtuales de Azure
ms.service: azure
keywords: ansible, azure, devops, bash, cuaderno de estrategias, máquina virtual, conjunto de escalado de máquinas virtuales, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 1176987ab318a97a7db6a12e619e7b7db06ad2da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097896"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Creación de conjuntos de escalado de máquinas virtuales de Azure mediante Ansible
Ansible permite automatizar la implementación y la configuración de recursos en un entorno. Puede usar Ansible para administrar su conjunto de escalado de máquinas virtuales (VMSS) de Azure, al igual que podría hacerlo con cualquier otro recurso de Azure. En este artículo se muestra cómo usar Ansible para crear y escalar horizontalmente un conjunto de escalado de máquinas virtuales. 

## <a name="prerequisites"></a>Requisitos previos
- **Suscripción a Azure**: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6 es necesario para ejecutar los siguientes cuadernos de estrategias de ejemplo de este tutorial. 

## <a name="create-a-vmss"></a>Creación de un VMSS
En esta sección se presenta un cuaderno de estrategias de Ansible de ejemplo que define los siguientes recursos:
- **Grupo de recursos** en el que se implementarán todos sus recursos
- **Red virtual** en el espacio de direcciones de 10.0.0.0/16
- **Subred** en la red virtual
- **Dirección IP pública** que permite tener acceso a recursos de Internet
- **Grupo de seguridad de red** que controla el flujo del tráfico de red de entrada y salida de su conjunto de escalado de máquinas virtuales
- **Equilibrador de carga** que distribuye el tráfico a través de un conjunto de máquinas virtuales definidas usando reglas de equilibrador de carga.
- **Conjunto de escalado de máquinas virtuales** que usa todos los recursos creados

Escriba su propia contraseña para el valor *admin_password*.

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Para crear el entorno del conjunto de escalado de máquinas virtuales con Ansible, guarde el cuaderno de estrategias anterior como `vmss-create.yml`, o bien [descargue el cuaderno de estrategias de Ansible de ejemplo](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Para ejecutar el cuaderno de estrategias de Ansible, use el comando **ansible-playbook** de la siguiente manera:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Tras ejecutar el cuaderno de estrategias, una salida similar a la del siguiente ejemplo muestra que el conjunto de escalado de máquinas virtuales se ha creado correctamente:

  ```Output
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Escalar horizontalmente un VMSS
El conjunto de escalado de máquinas virtuales creado tiene dos instancias. Si va al conjunto de escalado de máquinas virtuales de Azure Portal, verá **Standard_DS1_v2 (2 instancias)**. También puede usar [Azure Cloud Shell](https://shell.azure.com/) ejecutando el siguiente comando en Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Debería ver resultados similares a la siguiente salida:

  ```bash
  {
    "capacity": 2,
  }
  ```

Ahora, vamos a escalar de dos a tres instancias. El siguiente código del cuaderno de estrategias de Ansible recupera información sobre el escalado de máquinas virtuales y cambia su capacidad de dos a tres. 

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Para escalar horizontalmente el conjunto de escalado de máquinas virtuales que ha creado, guarde el cuaderno de estrategias anterior como `vmss-scale-out.yml`, o bien [descargue el cuaderno de estrategias de Ansible de ejemplo](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

El siguiente comando ejecutará el cuaderno de estrategias:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

El resultado de la ejecución del cuaderno de estrategias de Ansible muestra que el conjunto de escalado de máquinas virtuales se ha escalado horizontalmente con éxito:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Si va al conjunto de escalado de máquinas virtuales que ha configurado en Azure Portal, verá **Standard_DS1_v2 (3 instancias)**. También puede comprobar el cambio con [Azure Cloud Shell](https://shell.azure.com/) ejecutando el siguiente comando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

El resultado de la ejecución del comando en Cloud Shell muestra que ahora existen tres instancias. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"] 
> [Implementación de aplicaciones en conjuntos de escalado de máquinas virtuales de Azure mediante Ansible](https://docs.microsoft.com/azure/ansible/ansible-deploy-app-vmss)
> 
> [Escalado automático de un conjunto de escalado de máquinas virtuales con Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)