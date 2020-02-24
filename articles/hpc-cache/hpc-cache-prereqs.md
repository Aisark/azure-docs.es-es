---
title: Requisitos previos de Azure HPC Cache
description: Requisitos previos para usar Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rohogue
ms.openlocfilehash: 135c231f84d95ea2418fab4647d715473378e41c
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251964"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Requisitos previos para Azure HPC Cache

Antes de usar Azure Portal para crear una instancia de Azure HPC Cache, asegúrese de que su entorno cumple estos requisitos.

## <a name="azure-subscription"></a>Suscripción de Azure

Se recomienda una suscripción de pago.

> [!NOTE]
> Durante los primeros meses de la versión de disponibilidad general, el equipo de Azure HPC Cache debe agregar su suscripción a la lista de acceso antes de poder usarla para crear una instancia de caché. Este procedimiento ayuda a garantizar que cada cliente obtiene una capacidad de respuesta de alta calidad de sus cachés. Rellene [este formulario](https://aka.ms/onboard-hpc-cache) para solicitar acceso.

## <a name="network-infrastructure"></a>Infraestructura de red

Para poder usar la caché, primero es necesario configurar dos opciones relacionadas con la red:

* Una subred dedicada para la instancia de Azure HPC Cache
* Compatibilidad con DNS para que la caché pueda acceder al almacenamiento y otros recursos

### <a name="cache-subnet"></a>Subred de caché

Azure HPC Cache necesita una subred dedicada con estas cualidades:

* La subred debe tener al menos 64 direcciones IP disponibles.
* La subred no puede hospedar ninguna otra máquina virtual, ni siquiera para servicios relacionados, como máquinas cliente.
* Si usa varias instancias de Azure HPC Cache, cada una necesita su propia subred.

El procedimiento recomendado es crear una subred para cada caché. Puede crear una red virtual y una subred como parte de la creación de la caché.

### <a name="dns-access"></a>Acceso DNS

La caché necesita DNS para acceder a los recursos que están fuera de su red virtual. En función de los recursos que use, puede que tenga que configurar un servidor DNS personalizado y el reenvío entre ese servidor y los servidores de Azure DNS:

* Para acceder a los puntos de conexión de Azure Blob Storage y otros recursos internos, necesita el servidor DNS basado en Azure.
* Para acceder al almacenamiento local, debe configurar un servidor DNS personalizado que pueda resolver los nombres de host de almacenamiento.

Si solo necesita acceder a Blob Storage, puede usar el servidor DNS predeterminado proporcionado por Azure para la caché. Sin embargo, si necesita acceder a otros recursos, debe crear un servidor DNS personalizado y configurarlo para reenviar las solicitudes de resolución específicas de Azure al servidor de Azure DNS.

También se puede usar un servidor DNS sencillo para equilibrar la carga de las conexiones de cliente entre todos los puntos de montaje de caché disponibles.

Más información sobre las configuraciones de servidor DNS y redes virtuales de Azure en [Resolución de nombres de recursos en redes virtuales de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Permisos

Antes de empezar a crear la caché, compruebe estos requisitos previos relacionados con los permisos.

* La instancia de caché necesita poder crear interfaces de red virtual (NIC). El usuario que crea la caché debe tener privilegios suficientes en la suscripción para crear las NIC.

* Si usa Blob Storage, Azure HPC Cache necesita autorización para acceder a la cuenta de almacenamiento. Use el control de acceso basado en rol (RBAC) para conceder a la caché acceso al almacenamiento de blobs. Se requieren dos roles: Colaborador de la cuenta de almacenamiento y Colaborador de datos de Storage Blob.

  Siga las instrucciones que se indican en [Incorporación de destinos de almacenamiento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para agregar los roles.

## <a name="storage-infrastructure"></a>Administración del almacenamiento

La caché admite contenedores de blobs de Azure o exportaciones de almacenamiento de hardware NFS. Agregar destino de almacenamiento después de crear la memoria caché.

Cada tipo de almacenamiento tiene unos requisitos previos específicos.

### <a name="blob-storage-requirements"></a>Requisitos de Blob Storage

Si quiere usar Azure Blob Storage con su instancia de caché, necesita una cuenta de almacenamiento compatible y un contenedor de blobs vacío o un contenedor rellenado con datos con formato de Azure HPC Cache, como se describe en [Traslado de datos a Azure Blob Storage](hpc-cache-ingest.md).

Cree la cuenta antes de intentar agregar un destino de almacenamiento. Puede crear un contenedor al agregar el destino.

Para crear una cuenta de almacenamiento compatible, use esta configuración:

* Rendimiento: **Estándar**
* Tipo de cuenta: **StorageV2 (de uso general v2)**
* Replicación: **Almacenamiento con redundancia local (LRS)**
* Nivel de acceso (predeterminado): **Acceso frecuente**

Se recomienda usar una cuenta de almacenamiento que esté en la misma ubicación que la caché.
<!-- clarify location - same region or same resource group or same virtual network? -->

También debe proporcionar a la aplicación de caché acceso a su cuenta de almacenamiento de Azure, como se mencionó en [Permisos](#permissions) anteriormente. Siga el procedimiento descrito en [Incorporación de destinos de almacenamiento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para proporcionar a la caché los roles de acceso necesarios. Si no es el propietario de la cuenta de almacenamiento, pida a este que realice este paso.

### <a name="nfs-storage-requirements"></a>Requisitos de almacenamiento de NFS

Si usa un sistema de almacenamiento NFS (por ejemplo, un sistema NAS de hardware local), asegúrese de que cumpla estos requisitos. Es posible que deba trabajar con los administradores de red o los administradores de firewall para su sistema de almacenamiento (o centro de datos) para verificar esta configuración.

> [!NOTE]
> Se producirá un error en la creación del destino de almacenamiento si la memoria caché no tiene acceso suficiente al sistema de almacenamiento NFS.

* **Conectividad de red:** Azure HPC Cache necesita un acceso de red de alto ancho de banda entre la subred de la memoria caché y el centro de datos del sistema NFS. Se recomienda el acceso [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) o similar. Si usa una VPN, es posible que deba configurarla para fijar TCP MSS en 1350 para asegurarse de que no se bloqueen los paquetes grandes.

* **Acceso a puertos:** La memoria caché necesita acceso a determinados puertos TCP/UDP en el sistema de almacenamiento. Los distintos tipos de almacenamiento tienen distintos requisitos de puertos.

  Para comprobar la configuración del sistema de almacenamiento, siga este procedimiento.

  * Emita un comando `rpcinfo` al sistema de almacenamiento para comprobar los puertos necesarios. El comando siguiente muestra los puertos y presenta los resultados pertinentes en una tabla. (Use la dirección IP de su sistema en lugar del término *<storage_IP>* ).

    Puede emitir este comando desde cualquier cliente Linux que tenga instalada infraestructura de NFS. Si usa un cliente dentro de la subred del clúster, también puede ayudar a verificar la conectividad entre la subred y el sistema de almacenamiento.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  * Además de los puertos devueltos por el comando `rpcinfo`, asegúrese de que estos puertos usados comúnmente permitan el tráfico entrante y saliente:

    | Protocolo | Port  | Servicio  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

  * Compruebe la configuración del firewall para asegurarse de que permite el tráfico en todos estos puertos necesarios. Asegúrese de comprobar los firewalls que se usan en Azure, así como los firewalls locales de su centro de datos.

* **Acceso a directorios:** Habilite el comando `showmount` en el sistema de almacenamiento. Azure HPC Cache usa este comando para comprobar que la configuración del destino de almacenamiento apunta a una exportación válida, y también para asegurarse de que varios montajes no tengan acceso a los mismos subdirectorios (lo que supone un riesgo de colisiones de archivos).

  > [!NOTE]
  > Si el sistema de almacenamiento NFS usa el sistema operativo ONTAP 9.2 de NetApp, **no habilite `showmount`** . [Póngase en contacto con los servicios y el soporte técnico de Microsoft](hpc-cache-support-ticket.md) para obtener ayuda.

* **Acceso a la raíz:** La memoria caché se conecta al sistema back-end como el ID. de usuario 0. Compruebe esta configuración en el sistema de almacenamiento:
  
  * Habilite `no_root_squash`. Esta opción garantiza que el usuario raíz remoto pueda tener acceso a los archivos que pertenecen a la raíz.

  * Consulte las directivas de exportación para asegurarse de que no incluyan restricciones al acceso a la raíz desde la subred de la memoria caché.

* El almacenamiento de back-end de NFS debe ser una plataforma de hardware o software compatible. Póngase en contacto con el equipo de Azure HPC Cache para más información.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una instancia de Azure HPC Cache](hpc-cache-create.md) desde Azure Portal
