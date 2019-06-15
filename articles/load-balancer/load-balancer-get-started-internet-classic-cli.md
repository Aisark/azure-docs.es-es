---
title: Creación de un equilibrador de carga con conexión a Internet en la CLI clásica de Azure
titlesuffix: Azure Load Balancer
description: Aprenda a crear un equilibrador de carga accesible desde Internet en el modelo de implementación clásica mediante la CLI clásica de Azure
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544894"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Introducción a la creación de un equilibrador de carga accesible desde Internet (clásico) en la CLI clásica de Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI de Azure clásica](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](../azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Creación de un equilibrador de carga accesible desde Internet con CLI

Esta guía muestra cómo crear un equilibrador de carga de Internet basado en el escenario anterior.

1. Si es la primera vez que usa la CLI clásica de Azure, consulte [Install the Azure classic CLI](../cli-install-nodejs.md) (Instalar la CLI clásica de Azure) y siga las instrucciones hasta el punto donde tiene que seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo clásico, como se muestra a continuación.

    ```azurecli
    azure config mode asm
    ```

    Resultado esperado:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Crear punto de conexión y conjunto de equilibrador de carga

En esta situación se supone que se han creado las máquinas virtuales "web1" y "web2".
Esta guía creará un conjunto de equilibrador de carga mediante el puerto 80 como puerto público y el puerto 80 como puerto local. También se configura un puerto de sondeo en el puerto 80 y se llama al conjunto de equilibrador de carga "lbset".

### <a name="step-1"></a>Paso 1

Crear el primer punto de conexión y conjunto de equilibrador de carga mediante `azure network vm endpoint create` para la máquina virtual "web1"

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Paso 2

Agregar una segunda máquina virtual "web2" al conjunto de equilibrador de carga

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Paso 3

Comprobar la configuración del equilibrador de carga mediante `azure vm show`

```azurecli
azure vm show web1
```

El resultado será:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Crear un punto de conexión de escritorio remoto para una máquina virtual

Puede crear un punto de conexión de escritorio remoto para reenviar el tráfico de red desde un puerto público a un puerto local para una máquina virtual específica mediante `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Quitar máquina virtual del equilibrador de carga

Tendrá que eliminar el punto de conexión asociado al conjunto de equilibrador de carga desde la máquina virtual. Una vez eliminado el punto de conexión, la máquina virtual deja de pertenecer al conjunto de equilibrador de carga.

En el ejemplo anterior, puede quitar el punto de conexión creado para la máquina virtual "web1" del equilibrador de carga "lbset" mediante el comando `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Puede explorar más opciones para administrar los puntos de conexión con el comando `azure vm endpoint --help`.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
