---
title: 'Azure VMware Solutions (AVS): componentes de VMware de nube privada de AVS'
description: Se describe la instalación de componentes de VMware en nubes privadas de VMware.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016789"
---
# <a name="avs-private-cloud-vmware-components"></a>Componentes de VMware de nube privada de AVS

Una nube privada de AVS es un entorno de pila de VMware aislado (hosts ESXi, vCenter, vSAN y NSX) que administra un servidor vCenter en un dominio de administración. El servicio AVS permite implementar de forma nativa VMware en una infraestructura de Azure sin sistema operativo en ubicaciones de Azure. Las nubes privadas de AVS se integran con el resto de la nube de Azure. Una nube privada de AVS se implementa con los siguientes componentes de la pila de VMware:

* **VMware ESXi:** hipervisor en nodos dedicados de Azure
* **VMware vCenter:** dispositivo para la administración centralizada del entorno de vSphere de la nube privada de AVS
* **vSAN de VMware:** solución de infraestructura hiperconvergida
* **NSX Data Center de VMware:** virtualización de redes y software de seguridad  

## <a name="vmware-component-versions"></a>Versiones de componentes de VMware

Una pila de VMware de nube privada de AVS se implementa con la versión de software siguiente.

| Componente | Versión | Versión con licencia |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter estándar |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | Avanzado |

## <a name="esxi"></a>ESXi

VMware ESXi se instala en los nodos de AVS aprovisionados al crear una nube privada de AVS. ESXi proporciona el hipervisor para implementar máquinas virtuales (VM) de carga de trabajo. Los nodos proporcionan infraestructuras hiperconvergidas (proceso y almacenamiento) en la nube privada de AVS. Los nodos forman parte del clúster de vSphere en la nube privada de AVS. Cada nodo tiene cuatro interfaces de red físicas conectadas a la red subyacente. Se usan dos interfaces de red físicas para crear un **conmutador distribuido de vSphere (VDS)** en vCenter y dos para crear un **conmutador virtual distribuido administrado por NSX (N-VDS)** . Las interfaces de red se configuran en modo activa/activa para la alta disponibilidad.

Más información sobre VMware ESXi

## <a name="vcenter-server-appliance"></a>Dispositivo de servidor de vCenter

El dispositivo de servidor de vCenter (VCSA) proporciona las funciones de autenticación, administración y orquestación para VMware Solutions (AVS). Al crear la nube privada de AVS, se implementa VCSA con el controlador Platform Services Controller (PSC) insertado. VCSA se implementa en el clúster de vSphere que se crea al implementar la nube privada de AVS. Cada nube privada de AVS tiene su propio VCSA. La expansión de una nube privada de AVS agrega los nodos al VCSA en la nube privada de AVS.

### <a name="vcenter-single-sign-on"></a>Inicio de sesión único de vCenter

El Controlador de servicios de plataforma insertado en VCSA está asociado con un **dominio de inicio de sesión único de vCenter**. El nombre de dominio es **AVS.local**. Se crea un usuario predeterminado **CloudOwner@AVS.com** para que acceda a vCenter. Puede agregar los [orígenes de identidad para vCenter](set-vcenter-identity.md) locales o de Azure Active Directory.

## <a name="vsan-storage"></a>Almacenamiento de vSAN

Las nubes privadas de AVS se crean con almacenamiento de vSAN all-flash totalmente configurado, de forma local en el clúster. Se necesita un mínimo de tres nodos de la misma SKU para crear un clúster de vSphere con el almacén de datos de vSAN. En el almacén de datos de vSAN la desduplicación y la compresión están habilitadas de forma predeterminada. Se crean dos grupos de discos en cada nodo del clúster de vSphere. Cada grupo de discos contiene un disco de caché y tres discos de capacidad.

Una directiva de almacenamiento de vSAN predeterminada se crea en el clúster de vSphere y se aplica al almacén de datos de vSAN. Esta directiva determina cómo se aprovisionan los objetos de almacenamiento de máquina virtual y se asignan en el almacén de datos para garantizar el nivel de servicio requerido. La directiva de almacenamiento define los **errores tolerables (FTT)** y el **método de tolerancia a errores**. Puede crear directivas de almacenamiento y aplicarlas a las máquinas virtuales. Para mantener el SLA, se debe mantener el 25 % de la capacidad de reserva en el almacén de datos de vSAN. 

### <a name="default-vsan-storage-policy"></a>Directiva de almacenamiento de vSAN predeterminada

En la tabla siguiente se muestran los parámetros de la directiva de almacenamiento de vSAN predeterminada.

| Número de nodos en el clúster de vSphere | FTT | Método de tolerancia a errores |
|------------------------------------|-----|--------------------------|
| 3 y 4 nodos | 1 | RAID 1 (creación de reflejo): crea 2 copias |
| 5 a 16 nodos | 2 | RAID 1 (creación de reflejo): crea 3 copias |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center proporciona funciones de virtualización de red, microsegmentación y seguridad de red en la nube privada de AVS. Puede configurar todos los servicios compatibles con NSX Data Center en la nube privada de AVS a través de NSX. Al crear una nube privada de AVS, se instalan y configuran los siguientes componentes de NSX.

* NSXT Manager (Administrador de NSXT)
* Zonas de transporte
* Perfil de vínculo superior de host y Edge
* Conmutador lógico para Transporte perimetral, Ext1 y Ext2
* Grupo de IP para nodos de transporte de ESXi
* Grupo de IP para nodos de transporte perimetral
* Nodos perimetrales
* Regla de antiafinidad de DRS para el controlador y las máquinas virtuales de Edge
* Enrutador de nivel 0
* Habilitar BGP en el enrutador de nivel 0

## <a name="vsphere-cluster"></a>Clúster de vSphere

Los hosts de ESXi se configuran como un clúster para garantizar la alta disponibilidad de la nube privada de AVS. Al crear una nube privada de AVS, los componentes de administración de vSphere se implementan en el primer clúster. Se crea un grupo de recursos para los componentes de administración y todas las máquinas virtuales de administración se implementan en este grupo de recursos. No se puede eliminar el primer clúster para reducir la nube privada de AVS. El clúster de vSphere proporciona alta disponibilidad para máquinas virtuales mediante **vSphere HA**. Los errores tolerables se basan en el número de nodos disponibles en el clúster. Puede usar la fórmula ```Number of nodes = 2N+1```, donde ```N``` es el número de errores tolerables.

### <a name="vsphere-cluster-limits"></a>Límites del clúster de vSphere

| Resource | Límite |
|----------|-------|
| Número mínimo de nodos para crear una nube privada de AVS (primer clúster de vSphere) | 3 |
| Número máximo de nodos en un clúster de vSphere en una nube privada de AVS | 16 |
| Número máximo de nodos en una nube privada de AVS | 64 |
| Número máximo de clústeres de vSphere en una nube privada de AVS | 21 |
| Número mínimo de nodos en un clúster de vSphere nuevo | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Mantenimiento de la infraestructura de VMware

En ocasiones, es necesario realizar cambios en la configuración de la infraestructura de VMware. En la actualidad, estos intervalos se pueden producir cada 1 o 2 meses, pero se espera que la frecuencia se reduzca con el tiempo. Normalmente, este tipo de mantenimiento se puede realizar sin interrumpir el consumo normal de los servicios de AVS. Durante un intervalo de mantenimiento de VMware, los servicios siguientes seguirán funcionando sin ningún impacto:

* Las aplicaciones y el plano de administración de VMware
* El acceso a vCenter
* Todas las redes y el almacenamiento
* Todo el tráfico de Azure

## <a name="updates-and-upgrades"></a>Actualizaciones

AVS es responsable de la administración del ciclo de vida del software de VMware (ESXi, vCenter, PSC y NSX) en la nube privada de AVS.

Entre las actualizaciones de software se incluyen las siguientes:

* **Revisiones**. Revisiones de seguridad o correcciones de errores publicadas por VMware.
* **Actualizaciones**. Cambio de versión secundaria de un componente de la pila de VMware.
* **Actualizaciones**. Cambio de versión principal de un componente de la pila de VMware.

AVS prueba una revisión de seguridad crítica en cuanto está disponible desde VMware. Según el SLA, AVS implementa la revisión de seguridad en los entornos de nube privada de AVS en el plazo de una semana.

AVS proporciona actualizaciones de mantenimiento trimestrales para los componentes de software de VMware. Cuando hay una nueva versión principal disponible del software de VMware, AVS trabaja con los clientes para coordinar una ventana de mantenimiento adecuada para la actualización. 

## <a name="next-steps"></a>Pasos siguientes

* [Mantenimiento y actualizaciones de AVS](cloudsimple-maintenance-updates.md)
