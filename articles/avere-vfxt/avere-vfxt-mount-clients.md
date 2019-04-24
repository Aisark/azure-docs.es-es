---
title: Montaje del clúster de Avere vFXT - Azure
description: Cómo montar los clientes con Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409387"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montaje del clúster de Avere de vFXT  

Siga estos pasos para conectar equipos cliente a su clúster de vFXT.

1. Decida cómo equilibrar la carga del tráfico del cliente entre los nodos de su clúster. Lea la sección [Equilibrar la carga de cliente](#balance-client-load) que tiene a continuación para obtener más información. 
1. Identifique la dirección IP y la ruta de acceso de unión que se van a montar.
1. Ejecute el [comando de montaje](#mount-command-arguments) con los argumentos apropiados.

## <a name="balance-client-load"></a>Equilibrar la carga de cliente

Para equilibrar las solicitudes de cliente entre todos los nodos del clúster, debe montar a los clientes en el rango completo de direcciones IP orientadas al cliente. Existen varias formas sencillas de automatizar esta tarea.

> [!TIP] 
> Otros métodos para equilibrar la carga pueden ser apropiados para sistemas grandes o complicados; [abra una incidencia de soporte técnico](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para obtener ayuda.
> 
> Si prefiere usar un servidor DNS para el equilibrio de carga automático del lado servidor, debe configurar y administrar su propio servidor DNS en Azure. En ese caso, puede configurar el DNS round robin para el clúster de vFXT según este documento: [Configuración de DNS del clúster de Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Script de ejemplo para el montaje de cliente equilibrado

Este ejemplo de código usa las direcciones IP de los clientes como un elemento aleatorio para distribuir clientes a todas las direcciones IP disponibles en el clúster de vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

La función anterior forma parte del ejemplo de Batch que está disponible en el sitio de [ejemplos de Avere vFXT](https://github.com/Azure/Avere#tutorials).

## <a name="create-the-mount-command"></a>Crear el comando de montaje 

> [!NOTE]
> Si no creó un nuevo contenedor de blobs al crear el clúster de Avere vFXT, siga los pasos descritos en la [configuración del almacenamiento](avere-vfxt-add-storage.md) antes de intentar montar los clientes.

Desde el cliente, el comando ``mount`` asigna el servidor virtual (vserver) en el clúster de vFXT a una ruta de acceso en el sistema de archivos local. El formato es ``mount <vFXT path> <local path> {options}``.

Hay tres elementos para el comando de montaje: 

* Ruta de acceso de vFXT: es una combinación de la dirección IP y la ruta de acceso de la unión del espacio de nombres descrita a continuación.
* Ruta de acceso local: es la ruta en el cliente. 
* Opciones del comando de montaje: están enumeradas en los [argumentos del comando de montaje](#mount-command-arguments).

### <a name="junction-and-ip"></a>Unión e IP

La ruta de acceso del servidor virtual es una combinación de su *dirección IP* y la ruta de acceso a una *unión del espacio de nombres*. La unión del espacio de nombres es una ruta de acceso virtual que se definió cuando se agregó el sistema de almacenamiento.

Si el clúster se creó con Blob Storage, la ruta de acceso del espacio de nombres es `/msazure`.

Ejemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Si agregó el almacenamiento después de crear el clúster, la ruta de acceso de unión del espacio de nombres corresponde al valor que se estableció en la **ruta de acceso del espacio de nombres** al crear la unión. Por ejemplo, si usó ``/avere/files`` como ruta de acceso del espacio de nombres, los clientes usarán *IP_address*:/avere/files en su punto de montaje local.

![Cuadro de diálogo "Agregar nueva unión" con/avere/files en el campo de la ruta de acceso del espacio de nombres](media/avere-vfxt-create-junction-example.png)


La dirección IP es una de las direcciones IP orientadas al cliente que se definen para el servidor virtual (vserver). Puede encontrar el rango de direcciones IP orientadas al cliente en dos lugares del panel de control de Avere:

* Tabla **VServers** (pestaña de panel) - 

  ![Pestaña "Panel" del panel de control de Avere con la pestaña del servidor virtual seleccionada en la tabla de datos que está debajo del gráfico, y con la sección de dirección IP indicada](media/avere-vfxt-ip-addresses-dashboard.png)

* Página de configuración de **red orientada al cliente** - 

  ![Configuración> Servidor virtual > Página de configuración de red orientada al cliente en la que se indica la sección Rango de direcciones de la tabla de un servidor virtual en particular](media/avere-vfxt-ip-addresses-settings.png)

Además de las rutas de acceso, incluya los [argumentos del comando de montaje](#mount-command-arguments) que se describen a continuación cuando monte cada cliente.

### <a name="mount-command-arguments"></a>Argumentos del comando de montaje

Para garantizar un montaje de cliente sin problemas, use estos valores y argumentos en el comando de montaje: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Configuración requerida | |
--- | --- 
``hard`` | Los montajes leves en el clúster de vFXT están asociados con errores de la aplicación y la posible pérdida de datos. 
``proto=netid`` | Esta opción es compatible con el control adecuado de los errores de la red NFS.
``mountproto=netid`` | Esta opción es compatible con el control adecuado de errores de red para las operaciones de montaje.
``retry=n`` | Configure ``retry=30`` para evitar errores de montaje transitorios. (Se recomienda usar un valor diferente en los montajes de primer plano).

| Configuración preferida  | |
--- | --- 
``nointr``            | La opción "nointr" es la opción preferida para los clientes con kernels heredados (antes de abril de 2008) que admitan esta opción. Tenga en cuenta que la opción "intr" es el valor predeterminado.


## <a name="next-steps"></a>Pasos siguientes

Después de que montar los clientes, puede usarlos para rellenar el almacenamiento de datos de back-end (archivador principal). Consulte estos documentos para obtener más información sobre las tareas de configuración adicionales:

* [Mover datos al archivador principal del clúster](avere-vfxt-data-ingest.md): cómo usar varios clientes y subprocesos para cargar los datos de forma eficaz.
* [Personalizar el ajuste del clúster](avere-vfxt-tuning.md): personalice la configuración del clúster para que se adapte a la carga de trabajo.
* [Administrar el clúster](avere-vfxt-manage-cluster.md): cómo iniciar o detener el clúster y administrar nodos.
