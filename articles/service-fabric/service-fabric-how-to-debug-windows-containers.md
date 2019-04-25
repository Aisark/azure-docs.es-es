---
title: Depuración de contenedores de Windows con Service Fabric y VS | Microsoft Docs
description: Obtenga información acerca de cómo depurar los contenedores de Windows en Azure Service Fabric mediante Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo, mikhegn
ms.openlocfilehash: 9fe66e40376d9098244a1268fe9884cd416a36c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482650"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Procedimientos para: Depuración de contenedores de Windows en Azure Service Fabric mediante Visual Studio 2017

Con Visual Studio 2017 Update 7 (15.7), puede depurar aplicaciones .NET en contenedores como servicios de Service Fabric. En este artículo se muestra cómo configurar el entorno y, a continuación, depurar una aplicación .NET en un contenedor que se ejecuta en un clúster de Service Fabric local.

## <a name="prerequisites"></a>Requisitos previos

* En Windows 10, siga este inicio rápido para [configurar Windows 10 para ejecutar contenedores de Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10).
* En Windows Server 2016, siga este inicio rápido para [configurar Windows 2016 para ejecutar contenedores de Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server).
* Configure el entorno de Service Fabric local con el procedimiento descrito en [Preparación del entorno de desarrollo en Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configuración del entorno de desarrollo para depurar contenedores

1. Asegúrese de que el Docker de servicio de Windows se esté ejecutando antes de continuar con el paso siguiente.

1. Para admitir la resolución DNS entre contenedores, tendrá que configurar el clúster de desarrollo local, usando para ello el nombre de la máquina. Estos pasos también son necesarios si desea ofrecer los servicios mediante el proxy inverso.
   1. Abra PowerShell como administrador.
   2. Vaya a la carpeta de instalación del clúster del SDK, que suele ser `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Ejecución del script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Puede usar `-CreateOneNodeCluster` para configurar un clúster de un nodo. El valor predeterminado creará un clúster de cinco nodos local.
      >

      Para más información sobre el servicio DNS en Service Fabric, consulte [Servicio DNS en Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Para aprender más sobre el uso del proxy inverso de Service Fabric en servicios que se ejecutan en un contenedor, consulte [Tratamiento especial del proxy inverso para servicios que se ejecutan en contenedores](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitaciones conocidas al depurar contenedores en Service Fabric

A continuación, se muestra una lista de limitaciones conocidas al depurar contenedores en Service Fabric y las posibles soluciones:

* El uso de localhost para ClusterFQDNorIP no admitirá la resolución DNS en contenedores.
    * Resolución: Configure el clúster local usando el nombre de la máquina (véase más arriba).
* Al ejecutar Windows 10 en una máquina virtual, no se obtendrá la respuesta de DNS en el contenedor.
    * Resolución: Deshabilite la descarga de suma de comprobación de UDP para IPv4 en la NIC de máquinas virtuales.
    * Tenga en cuenta que esto disminuirá el rendimiento de red en la máquina.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* La resolución de servicios en la misma aplicación con nombre del servicio DNS no funciona en Windows 10 si la aplicación se implementó mediante Docker Compose.
    * Resolución: Use servicename.applicationname para resolver los puntos de conexión de servicio.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Si usa la dirección IP para ClusterFQDNorIP, al cambiar la dirección IP principal en el host se inhabilitará la funcionalidad DNS.
    * Resolución: Vuelva a crear el clúster con la nueva dirección IP principal en el host o use el nombre de la máquina. Esto es así por diseño.
* Si el FQDN con el que se creó el clúster no se puede resolver en la red, se producirá un error de DNS.
    * Resolución: Vuelva a crear el clúster local con la dirección IP principal del host. Esto es así por diseño.
* Cuando se depura un contenedor, los registros de Docker solo estarán disponibles en la ventana de salida de Visual Studio, no mediante las API de Service Fabric, incluido Service Fabric Explorer.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Depuración de una aplicación .NET que se ejecuta en contenedores de Docker en Service Fabric

1. Ejecute Visual Studio como administrador.

1. Abra una aplicación .NET existente o cree una nueva.

1. Haga clic con el botón derecho en el proyecto y seleccione **Agregar -> Compatibilidad con el orquestador de contenedores -> Service Fabric**.

1. Presione **F5** para iniciar la depuración de la aplicación.

    Visual Studio admite la consola y los tipos de proyecto ASP.NET para .NET y .NET Core.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las funcionalidades de Service Fabric y los contenedores, siga este vínculo: [Información general sobre los contenedores de Service Fabric](service-fabric-containers-overview.md).
