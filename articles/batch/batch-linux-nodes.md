---
title: Ejecución de Linux en nodos de proceso de máquinas virtuales - Azure Batch | Microsoft Docs
description: Obtenga información acerca de cómo procesar las cargas de trabajo de proceso paralelas en grupos de máquinas virtuales de Linux en el servicio Azure Batch.
services: batch
documentationcenter: python
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 080310d5884ca82a3ff02ff0474777ea3a71997e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547615"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Aprovisionamiento de nodos de proceso de Linux en grupos de Batch

Puede usar el servicio Azure Batch para ejecutar cargas de trabajo de proceso paralelas en máquinas virtuales Linux y Windows. Este artículo detalla cómo crear grupos de nodos de proceso de Linux en el servicio Batch mediante las bibliotecas de cliente de [Python de Batch][py_batch_package] y [.NET de Batch][api_net].

> [!NOTE]
> Los paquetes de aplicaciones se admiten en todos los grupos de Batch creados después del 5 de julio de 2017. Se admiten en los grupos de Batch creados entre el 10 de marzo de 2016 y el 5 de julio de 2017 únicamente si el grupo se creó mediante una configuración de Cloud Services. Los grupos de Batch creados antes del 10 de marzo de 2016 no admiten los paquetes de aplicaciones. Para más información sobre el uso de los paquetes de aplicación para implementar las aplicaciones en los nodos de Batch, consulte [Implementación de aplicaciones en nodos de proceso con paquetes de aplicaciones de Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuración de la máquina virtual
Cuando crea un grupo de nodos de proceso en el servicio Batch, tiene dos opciones para seleccionar el sistema operativo y el tamaño de nodo: configuración de Cloud Services y configuración de Virtual Machine.

**Configuración de Cloud Services** proporciona *solo* nodos de proceso de Windows. Los tamaños de nodos de proceso disponibles se muestran en [Tamaño Cloud Services](../cloud-services/cloud-services-sizes-specs.md) y los sistemas operativos disponibles se enumeran en [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Al crear un grupo que contiene nodos de Azure Cloud Services, debe especificar el tamaño del nodo y la familia del SO, que se describen en los artículos mencionados anteriormente. Para los grupos de nodos de proceso de Windows, la mayoría de las veces se utilizan Cloud Services.

**Configuración de la máquina virtual** proporciona imágenes de Linux y Windows para los nodos de proceso. Los tamaños de nodos de proceso disponibles se muestran en [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Tamaños de las máquinas virtuales Windows en Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cuando se crea un grupo que contiene los nodos de configuración de la máquina virtual, debe especificar su tamaño, la referencia de la imagen de máquina virtual y el SKU del agente de nodo del servicio Batch que desea instalar en los nodos.

### <a name="virtual-machine-image-reference"></a>Referencia de imagen de máquina virtual

El servicio Batch usa [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para proporcionar nodos de ejecución en la configuración de la máquina virtual. Puede especificar una imagen de [Azure Marketplace][vm_marketplace] o proporcionar una imagen personalizada que haya preparado. Para más detalles sobre las imágenes personalizadas, consulte [Creación de un grupo con Shared Image Gallery](batch-sig-images.md).

Al configurar una referencia de la imagen de máquina virtual, especifique las propiedades de la imagen de máquina virtual. Las propiedades siguientes son necesarias cuando se crea una referencia de la imagen de máquina virtual:

| **Propiedades de la referencia de la imagen** | **Ejemplo** |
| --- | --- |
| Publicador |Canonical |
| Oferta |UbuntuServer |
| SKU |14.04.4-LTS |
| Versión |latest |

> [!TIP]
> Puede obtener más información sobre estas propiedades y cómo mostrar imágenes de Marketplace en [Navegación y selección de las imágenes de máquina virtual Linux en Azure con la CLI o PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tenga en cuenta que no todas las imágenes de Marketplace son actualmente compatibles con el servicio Batch. Para más información, consulte [SKU del agente de nodo](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU del agente de nodo
El agente de nodo del servicio Batch es un programa que se ejecuta en cada nodo del grupo y proporciona la interfaz de comandos y control entre el nodo y el servicio. Hay diferentes implementaciones del agente de nodo, conocidas como SKU, para distintos sistemas operativos. Básicamente, al crear una configuración de la máquina virtual, debe especificar primero la referencia de la imagen de máquina virtual y, después, el agente de nodo que se va a instalar en la imagen. Normalmente, cada SKU del agente de nodo es compatible con varias imágenes de máquina virtual. Estos son algunos ejemplos de SKU del agente de nodo:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> No todas las imágenes de máquinas virtuales que están disponibles en Marketplace son compatibles con los agentes de nodo de Batch disponibles actualmente. Use los SDK de Batch para mostrar las SKU del agente de nodo disponibles y las imágenes de máquina virtual con las que son compatibles. Consulte la [lista de imágenes de máquinas virtuales](#list-of-virtual-machine-images), que se detalla más adelante en este artículo, para obtener más información y ejemplos sobre cómo recuperar una lista de imágenes válidas en tiempo de ejecución.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Crear un grupo de Linux: Python de Batch
El fragmento de código siguiente muestra un ejemplo de cómo usar la [biblioteca cliente de Microsoft Azure Batch para Python][py_batch_package] a fin de crear un grupo de nodos de proceso del servidor de Ubuntu. Puede encontrar la documentación de referencia del módulo de Batch para Python en el [paquete azure.batch][py_batch_docs] (sección Lea los documentos).

En este fragmento de código, se crea una [ImageReference][py_imagereference] explícitamente y se especifica cada una de sus propiedades (publicador, oferta, SKU, versión). No obstante, en el código de producción recomendamos usar el método [list_node_agent_skus][py_list_skus] para determinar y seleccionar entre las combinaciones disponibles de SKU del agente de nodo y las imágenes en tiempo de ejecución.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Tal y como se mencionó antes, es recomendable que, en lugar de crear una [ImageReference][py_imagereference] explícitamente, utilice el método [list_node_agent_skus][py_list_skus] para seleccionar de forma dinámica entre las combinaciones de imágenes de Marketplace y los agentes de nodo admitidas actualmente. El siguiente fragmento de código de Python muestra cómo usar este método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Crear un grupo de Linux: .NET de Batch
El fragmento de código siguiente muestra un ejemplo de cómo utilizar la biblioteca cliente [.NET de Batch][nuget_batch_net] para crear un grupo de nodos de proceso del servidor de Ubuntu. Puede encontrar la [documentación de referencia de .NET de Batch][api_net] en docs.microsoft.com.

El fragmento de código siguiente utiliza el método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para seleccionar en la lista de combinaciones de imágenes de Marketplace y SKU del agente de nodo admitidas actualmente. Esta técnica es deseable porque la lista de combinaciones admitidas puede cambiar de vez en cuando. Normalmente, se agregan las combinaciones admitidas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Aunque el fragmento de código anterior usa el método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para mostrar y seleccionar dinámicamente entre las combinaciones admitidas de imágenes y SKU del agente de nodo (recomendadas), también puede configurar una [ImageReference][net_imagereference] explícitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imágenes de máquinas virtuales
En la tabla siguiente se enumeran las imágenes de máquinas virtuales de Marketplace que son compatibles con los agentes de nodo de Batch disponibles cuando se actualizó por última vez este artículo. Es importante tener en cuenta que esta lista no es definitiva, ya que se pueden agregar o quitar imágenes y agentes de nodo en cualquier momento. Se recomienda que los servicios y aplicaciones de Batch utilicen siempre los métodos [list_node_agent_skus][py_list_skus] (Python) o [ListNodeAgentSkus][net_list_skus] (NET de Batch) para determinar y seleccionar entre las SKU disponibles.

> [!WARNING]
> La siguiente lista se puede cambiar en cualquier momento. Use siempre los métodos **list_node_agent_SKU** disponibles en las API de Batch para mostrar los SKU de máquina virtual y del agente de nodo disponibles al ejecutar los trabajos de Batch.
>
>

| **Publicador** | **Oferta** | **SKU de imagen** | **Versión** | **Identificador de SKU del agente de nodo** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| proceso por lotes | rendering-centos73 | rendering | latest | batch.node.centos 7 |
| proceso por lotes | rendering-windows2016 | rendering | latest | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| Microsoft Windows Server | Windows Server | 2016-Datacenter | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| Microsoft Windows Server | Windows Server | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Conexión a los nodos de Linux mediante SSH
Durante el desarrollo o solución de problemas, es posible que necesite iniciar sesión en los nodos del grupo. A diferencia de los nodos de proceso de Windows, no puede utilizar el protocolo de escritorio remoto (RDP) para conectarse a los nodos de Linux. En su lugar, el servicio Batch habilita el acceso SSH en cada nodo para la conexión remota.

El siguiente fragmento de código de Python crea un usuario en cada nodo de un grupo, que es necesario para la conexión remota. A continuación, imprime la información de conexión de SSH para cada nodo.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Este es un ejemplo de resultado del código anterior para un grupo que contiene cuatro nodos de Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

En lugar de una contraseña, puede especificar una clave pública SSH al crear un usuario en un nodo. En el SDK de Python, use el parámetro **ssh_public_key** en [ComputeNodeUser][py_computenodeuser]. En .NET, use la propiedad [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## <a name="pricing"></a>Precios
El servicio Azure Batch se basa en la tecnología de Azure Cloud Services y Azure Virtual Machines. El propio servicio Batch se ofrece sin costo, lo que significa que solo se cobrará por los recursos de proceso utilizados por las soluciones del servicio. Al elegir **Configuración de Cloud Services**, se le cobrará según la estructura de [precios de Cloud Services][cloud_services_pricing]. Al elegir **Configuración de la máquina virtual**, se le cobrará según la estructura de [precios de máquinas virtuales][vm_pricing]. 

Si implementa aplicaciones en los nodos de Batch mediante [paquetes de aplicaciones](batch-application-packages.md), también se le cobrarán los recursos de Azure Storage que consumen los paquetes de aplicaciones. En general, los costos de Azure Storage son mínimos. 

## <a name="next-steps"></a>Pasos siguientes

Los [ejemplos de código de Python][github_samples_py] del repositorio [azure-batch-samples][github_samples] de GitHub contienen scripts que muestran cómo llevar a cabo operaciones comunes de Batch, como crear grupos, trabajos y tareas. El archivo [LÉAME][github_py_readme] que acompaña a los ejemplos de Python incluye detalles sobre cómo instalar los paquetes necesarios.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
