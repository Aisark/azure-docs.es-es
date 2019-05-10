---
title: 'Tutorial: Configurar colas de Azure Service Bus con Ansible | Microsoft Docs'
description: Obtenga información sobre cómo usar Ansible para crear una cola de Azure Service Bus
keywords: ansible, azure, devops, bash, cuaderno de estrategias, service bus, cola
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 96ac420b3d84792705209055e6665e37ef161620
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192182"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar las colas de Azure Service Bus con Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creación de una cola
> * Creación de una política de SAS
> * Recuperación de información del espacio de nombres
> * Recuperación de información de la cola
> * Revocación de la política SAS de cola

## <a name="prerequisites"></a>Requisitos previos

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Creación de la cola de Service Bus

El código del cuaderno de estrategias de ejemplo crea los siguientes recursos:
- Grupo de recursos de Azure
- El espacio de nombres de Service Bus donde se encuentra el recurso
- Cola de Service Bus con el espacio de nombres

Guarde el siguiente cuaderno de estrategias como `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Creación de la política SAS

Una [Firma de acceso compartido](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) es un mecanismo de autorización basada en notificaciones que utiliza tokens. 

El código del cuaderno de estrategias de ejemplo crea dos directivas SAS para una cola de Service Bus con privilegios diferentes.

Guarde el siguiente cuaderno de estrategias como `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Antes de ejecutar el cuaderno de estrategias, vea las notas siguientes:
- El valor `rights` representa el privilegio que tiene un usuario con la cola. El valor debe ser uno de los siguientes: `manage`, `listen`, `send` o `listen_send`.

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Recuperación de información del espacio de nombres

El código del cuaderno de estrategias de ejemplo consulta la información del espacio de nombres.

Guarde el siguiente cuaderno de estrategias como `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Antes de ejecutar el cuaderno de estrategias, vea las notas siguientes:
- El valor `show_sas_policies` indica si se muestran las directivas SAS en el espacio de nombres especificado. De forma predeterminada, el valor es `False` para evitar más sobrecarga de red.

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Recuperación de información de la cola

El código del cuaderno de estrategias de ejemplo consulta la información de la cola. 

Guarde el siguiente cuaderno de estrategias como `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Antes de ejecutar el cuaderno de estrategias, vea las notas siguientes:
- El valor `show_sas_policies` indica si se muestran las directivas SAS en la cola especificada. De forma predeterminada, el valor se establece en `False` para evitar más sobrecarga de red.

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revocación de la política SAS de cola

El código del cuaderno de estrategias de ejemplo elimina una directiva SAS de cola.

Guarde el siguiente cuaderno de estrategias como `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine los recursos creados en este artículo. 

Guarde el siguiente código como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Use el comando `ansible-playbook` para ejecutar el cuaderno de estrategias:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"] 
> [Tutorial: Configurar un tema de Service Bus de Azure con Ansible](ansible-service-bus-topic-configure.md)