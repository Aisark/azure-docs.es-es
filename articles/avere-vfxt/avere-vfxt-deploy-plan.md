---
title: Planear un sistema de Avere vFXT - Azure
description: Aquí se explica el plan que se debe realizar antes de implementar Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256243"
---
# <a name="plan-your-avere-vfxt-system"></a>Planear un sistema de Avere vFXT

En este artículo se explica cómo planear una nueva instancia de Avere vFXT para el clúster de Azure que tiene la posición y el tamaño adecuados para sus necesidades. 

Antes de ir a Azure Marketplace o crear las máquinas virtuales, piense en la manera en que el clúster interactuará con otros elementos de Azure. Planifique dónde se ubicarán los recursos de clúster en su red y subredes privadas, y decida dónde estará el almacenamiento de back-end. Asegúrese de que los nodos de clúster que cree sean lo suficientemente potentes para admitir su flujo de trabajo. 

Siga leyendo para obtener más información.

## <a name="resource-group-and-network-infrastructure"></a>Infraestructura de red y grupo de recursos

Piense dónde ubicará los elementos de su implementación de Avere vFXT para Azure. En el siguiente diagrama se muestra una posible disposición para los componentes de Avere vFXT para Azure:

![Diagrama que muestra el controlador y las máquinas virtuales del clúster dentro de una subred. En torno al límite de la subred hay un límite de red virtual. Igualmente, en la red virtual hay un hexágono que representa el punto de conexión del servicio de almacenamiento; tenga en cuenta que se conecta con una flecha discontinua a una instancia de Blob Storage que está fuera de la red virtual.](media/avere-vfxt-components-option.png)

Siga estas instrucciones cuando planifique la infraestructura de red de su sistema Avere vFXT:

* Todos los elementos se deben administrar con una nueva suscripción creada para la implementación de Avere vFXT. Dicha integración aporta las siguientes ventajas: 
  * Seguimiento del costo más sencillo: puede ver y auditar todos los costos de los recursos, la infraestructura y los ciclos de proceso en una única suscripción.
  * Limpieza más sencilla: puede eliminar la suscripción completa una vez finalizado el proyecto.
  * Creación de particiones adecuada de las cuotas de recursos: proteja otras cargas de trabajo críticas de posibles limitaciones de recursos mediante el aislamiento del clúster y los clientes de Avere vFXT en una suscripción única. Esto evita conflictos al incorporar un gran número de clientes para un flujo de trabajo informática de alto rendimiento.

* Busque sus sistemas de procesamiento de clientes cerca del clúster de vFXT. El almacenamiento de back-end puede ser aún más remoto.  

* El clúster de vFXT y la máquina virtual del controlador de clúster deben estar en la misma red virtual (vnet), en el mismo grupo de recursos, y usar la misma cuenta de almacenamiento. En la mayoría de las situaciones, la plantilla de creación de clústeres automatizada se encarga de ello.

* El clúster debe estar ubicado en su propia subred para evitar conflictos de direcciones IP con los clientes o con los recursos del proceso. 

* La plantilla de creación del clúster puede crear la mayoría de los recursos de infraestructura necesarios para el clúster, incluidos los grupos de recursos, las redes virtuales, las subredes y las cuentas de almacenamiento. Si desea usar recursos que ya existen, asegúrese de que cumplen los requisitos de esta tabla. 

  | Resource | ¿Usar existente? | Requisitos |
  |----------|-----------|----------|
  | Resource group | Sí, si está vacío | Debe estar vacío| 
  | Cuenta de almacenamiento | Sí, si se conecta un contenedor de blobs existente después de la creación del clúster <br/>  No, si crea un nuevo contenedor de blobs durante la creación del clúster | El contenedor de blobs existente debe estar vacío <br/> &nbsp; |
  | Virtual network | Sí | Debe incluir un punto de conexión de servicio de almacenamiento si va a crear un nuevo contenedor de blobs de Azure. | 
  | Subnet | Sí |   |

## <a name="ip-address-requirements"></a>Requisitos de las direcciones IP 

Asegúrese de que la subred del clúster tenga un rango de direcciones IP lo suficientemente grande como para admitir el clúster. 

El clúster de Avere vFXT usa las siguientes direcciones IP:

* Una dirección IP de gestión de clúster. Esta dirección puede moverse de un nodo a otro en el clúster, pero siempre está disponible para que pueda conectarse a la herramienta de configuración del panel de control de Avere.
* Para cada nodo del clúster:
  * Al menos una dirección IP orientada al cliente. (El *servidor virtual* del clúster se encarga de administrar todas las direcciones orientadas al cliente y puede moverlas entre nodos según sea necesario).
  * Una dirección IP para la comunicación del clúster.
  * Una dirección IP de la instancia (asignada a la máquina virtual).

Si usa Azure Blob Storage, también puede requerir direcciones IP de la red virtual de su clúster:  

* Una cuenta de Azure Blob Storage requiere al menos cinco direcciones IP. Tenga en cuenta este requisito si ubica Blob Storage en la misma red virtual que su clúster.
* Si usa una instancia de Azure Blob Storage que esté fuera de la red virtual del clúster, debe crear un punto de conexión de servicio de almacenamiento en la red virtual. Tenga en cuenta que este punto de conexión no usa una dirección IP.

Asimismo, tiene la opción de ubicar los recursos de red y Blob Storage (si lo va a usar) en diferentes grupos de recursos del clúster.

## <a name="vfxt-node-size"></a>Tamaño de los nodos de vFXT

Las máquinas virtuales que sirven como nodos de clúster determinan el rendimiento de la solicitud y la capacidad de almacenamiento de la caché. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nodo de vFXT será idéntico. Es decir, si crea un clúster de tres nodos, tendrá tres máquinas virtuales del mismo tipo y tamaño. 

| Tipo de instancia | vCPU | Memoria  | Almacenamiento local de SSD  | Discos de datos máx. | Rendimiento del disco sin almacenamiento en la caché | NIC (recuento) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GB  | 32 | 51 200 IOPS <br/> 768 MBps | 16 000 MBps (8)  |

La caché de disco por nodo se puede configurar y puede oscilar entre los 1000 GB y los 8000 GB. El tamaño recomendado de la memoria caché es de 4 TB por nodo para los nodos Standard_E32s_v3.

Para obtener información adicional sobre estas máquinas virtuales, lea la documentación de Microsoft Azure:

* [Tamaños de máquina virtual optimizada para memoria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cuotas de la cuenta

Asegúrese de que su suscripción tenga la capacidad de ejecutar el clúster de Avere vFXT, así como los sistemas de informática o cliente que vaya a usar. Lea [Cuota del clúster de vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obtener más detalles.

## <a name="back-end-data-storage"></a>Almacenamiento de datos de back-end

¿Dónde debe almacenar el clúster de Avere vFXT los datos cuando no está en la memoria caché? Decida si el espacio de trabajo se almacenará a largo plazo en un nuevo contenedor de blobs o en una nube o sistema de almacenamiento de hardware ya existentes. 

Si quiere usar Azure Blob Storage para el back-end, debe crear un nuevo contenedor como parte de la creación del clúster de vFXT. Esta opción crea y configura el nuevo contenedor de modo que esté listo para su uso tan pronto como el clúster esté listo. 

Lea [Creación del clúster de Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para más información.

> [!NOTE]
> Solo los contenedores de Blob Storage vacíos se pueden usar como archivadores principales del sistema Avere vFXT. vFXT debe poder administrar su almacén de objetos sin necesidad de guardar los datos existentes. 
>
> Lea [Moving data to the vFXT cluster](avere-vfxt-data-ingest.md) (Mover datos al clúster de vFXT) para aprender a copiar los datos en el nuevo contenedor del clúster de manera eficaz mediante el uso de máquinas cliente y la caché de Avere vFXT.

Si quiere usar un sistema de almacenamiento local existente, debe agregarlo al clúster de vFXT después de crearlo. Lea [Configure storage](avere-vfxt-add-storage.md) (Configurar el almacenamiento) para obtener instrucciones detalladas sobre cómo agregar un sistema de almacenamiento existente al clúster de Avere vFXT.

## <a name="cluster-access"></a>Acceso al clúster 

El clúster de Avere vFXT for Azure se encuentra en una subred privada, y el clúster no tiene ninguna dirección IP pública. Debe tener algún método para acceder a la subred privada para la administración del clúster y las conexiones de clientes. 

Los puntos de acceso incluyen:

* Host de salto: asigne una dirección IP pública a una máquina virtual independiente dentro de la red privada y úsela para crear un túnel SSL hasta los nodos del clúster. 

  > [!TIP]
  > Si configura una dirección IP pública en el controlador del clúster, puede usarla como host de salto. Lea [Controlador de clústeres como host de salto](#cluster-controller-as-jump-host) para más información.

* Red privada virtual (VPN): configure una VPN de sitio a sitio o de punto a sitio para la red privada.

* Azure ExpressRoute: configure una conexión privada mediante un asociado de ExpressRoute. 

Para más información sobre estas opciones, vea la [documentación de Azure Virtual Network relativa a la comunicación de Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Controlador de clústeres como host de salto

Si configura una dirección IP pública en un controlador del clúster, puede usarla como un host de salto para conectarse al clúster de Avere vFXT desde fuera de la subred privada. Sin embargo, dado que el controlador tiene privilegios de acceso para modificar nodos del clúster, plantea un pequeño riesgo de seguridad.  

Para mejorar la seguridad para un controlador con una dirección IP pública, el script de implementación crea automáticamente un grupo de seguridad de red que restrinja el acceso entrante solo al puerto 22. Puede proteger aún más el sistema al bloquear el acceso al intervalo de direcciones IP de origen, es decir, solo permitir las conexiones desde las máquinas que pretende usar para el acceso al clúster.

Al crear el clúster, puede elegir si desea crear o no una dirección IP pública en el controlador del clúster. 

* Si crea una red virtual o una subred, al controlador del clúster se le asignará una dirección IP pública.
* Si selecciona una red virtual y una subred existentes, el controlador del clúster solo tendrá direcciones IP privadas. 

## <a name="vm-access-roles"></a>Roles de acceso a máquinas virtuales 

Azure usa el [control de acceso basado en rol](../role-based-access-control/index.yml) (RBAC) para autorizar que las máquinas virtuales del clúster realicen determinadas tareas. Por ejemplo, el controlador del clúster necesita autorización para crear y configurar las máquinas virtuales del nodo de clúster. Los nodos de clúster deben ser capaces de asignar o reasignar direcciones IP a otros nodos de clúster.

Se utilizan dos roles de Azure integrados para las máquinas de virtuales de Avere vFXT: 

* El controlador del clúster utiliza el rol integrado [Colaborador de Avere](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Los nodos de clúster utiliza el rol integrado [Operador de Avere](../role-based-access-control/built-in-roles.md#avere-operator).

Si necesita personalizar los roles de acceso para los componentes de vFXT Avere, debe definir su propio rol y asignarlo a las máquinas virtuales al crearlos. No se puede usar la plantilla de implementación en Azure Marketplace. Consulte al servicio de Soporte técnico y Atención al cliente de Microsoft. abriendo una incidencia en Azure Portal, como se describe en [Obtener ayuda con su sistema](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Paso siguiente: comprender el proceso de implementación

La [información general de la implementación](avere-vfxt-deploy-overview.md) le proporciona una visión general de todos los pasos necesarios para crear una instancia Avere vFXT para el sistema Azure y tenerla lista para proporcionar datos.  