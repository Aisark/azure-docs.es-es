---
title: Conexión remota a un nodo de clúster de Azure Service Fabric | Microsoft Docs
description: Aprenda a conectarse de forma remota a una instancia del conjunto de escalado (un nodo de clúster de Service Fabric).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110930"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conexión remota a una instancia de conjunto de escalado de máquinas virtuales o a un nodo de clúster
En un clúster de Service Fabric que se ejecuta Azure, cada tipo de nodo de clúster que defina [configura una escala independiente de la máquina virtual](service-fabric-cluster-nodetypes.md).  También puede conectarse de forma remota a instancias específicas del conjunto de escalado (nodos de clúster).  A diferencia de las máquinas virtuales de instancia única, las instancias de conjuntos de escalado no tienen direcciones IP virtuales. Esto puede resultar un poco complicado si desea obtener una dirección IP y un puerto que pueda usar para conectarse de manera remota a una instancia específica.

Para buscar una dirección IP y un puerto que pueda usar para conectarse remotamente a una instancia concreta, realice los pasos siguientes.

1. Obtenga las reglas de NAT de entrada para Protocolo de escritorio remoto (RDP).

    Normalmente, cada tipo de nodo definido en el clúster tiene su propia dirección IP virtual y un equilibrador de carga dedicado. De forma predeterminada, el equilibrador de carga para un tipo de nodo se denomina con el formato siguiente: *LB-{nombre-clúster}-{tipo de nodo}*; por ejemplo, *mycluster-LB-FrontEnd*. 
    
    En Azure Portal, en la página del equilibrador de carga, seleccione **Configuración** > **Reglas NAT de entrada**. 

    ![Reglas NAT de entrada del equilibrador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    La siguiente captura de pantalla muestra las reglas NAT de entrada para un tipo de nodo con el nombre FrontEnd: 

    ![Reglas NAT de entrada del equilibrador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nodo, la dirección IP aparece en la columna **DESTINO**, la columna **OBJETIVO** indica la instancia del conjunto de escalado y la columna **SERVICIO** indica el número de puerto. Para la conexión remota, los puertos se asignan a cada nodo en orden ascendente a partir del puerto 3389.

    También puede encontrar las reglas NAT de entrada en la sección `Microsoft.Network/loadBalancers` de la plantilla de Resource Manager del clúster.
    
2. Para confirmar la asignación del puerto de entrada al puerto de destino para un nodo, puede hacer clic en la regla y consultar el valor **Puerto de destino**. La siguiente captura de pantalla muestra la regla NAT de entrada para el nodo **FrontEnd (instancia 1)** del paso anterior. Tenga en cuenta que, aunque el número de puerto (entrada) es 3390, el puerto de destino está asignado al puerto 3389, el puerto para el servicio RDP en el destino.  

    ![Asignación de puertos de destino](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    De forma predeterminada, para los clústeres Windows, el puerto de destino es 3389, que se asigna al servicio RDP en el nodo de destino. Para los clústeres Linux, el puerto de destino es 22, que se asigna al servicio de Secure Shell (SSH).

3. Conéctese de forma remota al nodo específico (instancia del conjunto de escalado). Puede usar el nombre de usuario y la contraseña que estableció cuando creó el clúster u otras credenciales que haya configurado. 

    La siguiente captura de pantalla muestra cómo usar Conexión a Escritorio remoto para conectarse al nodo **FrontEnd (instancia 1)** en un clúster Windows:
    
    ![Conexión del Escritorio remoto](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    En los nodos Linux, puede conectarse mediante SSH (en el ejemplo siguiente se vuelve a utilizar la misma dirección IP y puerto para abreviar):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para ver los próximos pasos, lea los siguientes artículos:
* Consulte [Descripción general de la característica "Deploy anywhere" y comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md).
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* [Actualización de los valores del intervalo de puertos RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) en máquinas virtuales del clúster después de la implementación
* [Cambio de la contraseña y el nombre de usuario administrador](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para máquinas virtuales del clúster

