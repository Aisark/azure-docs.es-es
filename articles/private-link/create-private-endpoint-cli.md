---
title: Creación de un punto de conexión privado de Azure mediante la CLI de Azure | Microsoft Docs
description: Obtenga información sobre el punto de conexión privado de Azure.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30994133b19c4f59ae9e8be26caffe14348638f6
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219372"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Creación de un punto de conexión privado mediante la CLI de Azure
Un punto de conexión privado es el bloque de creación fundamental para Private Link en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen de manera privada con recursos de Private Link. En este inicio rápido, obtendrá información sobre cómo crear una máquina virtual en una red virtual, un servidor de SQL Database con un punto de conexión privado mediante la CLI de Azure. A continuación, puede acceder a la máquina virtual y acceder de forma segura al recurso de vínculo privado (un servidor de Azure SQL Database privado en este ejemplo). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si, en su lugar, decide instalar y usar la CLI de Azure en un entorno local, para esta guía de inicio rápido se necesita la versión 2.0.28 de la CLI de Azure o una versión posterior. Ejecute `az --version` para buscar la versión instalada. Consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli) para obtener información sobre la instalación o actualización.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para poder crear un recurso, debe crear un grupo de recursos que hospede la red virtual. Cree un grupo de recursos con [az group create](/cli/azure/group). En el ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *westcentralus*:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Creación de una red virtual
Cree la red virtual con [az network vnet create](/cli/azure/network/vnet). En este ejemplo se crea una red virtual predeterminada denominada *myVirtualNetwork* con una subred denominada *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Deshabilitación de las directivas de punto de conexión privado 
Azure implementa los recursos en una subred de una red virtual, por lo que debe crear o actualizar la subred para deshabilitar las directivas de red del punto de conexión privado. Actualice una configuración de subred denominada *mySubnet** con [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creación de la máquina virtual 
Cree la máquina virtual con az vm create. Cuando se le solicite, proporcione una contraseña que se usará como credenciales de inicio de sesión para la VM. En este ejemplo se crea una máquina virtual llamada  *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Anote la dirección IP pública de la máquina virtual. Usará esta dirección para conectarse a la máquina virtual desde Internet en el paso siguiente.

## <a name="create-a-sql-database-server"></a>Creación de un servidor de SQL Database 
Cree un servidor de SQL Database con el comando az sql server create. Recuerde que el nombre de la instancia de SQL Server debe ser único en Azure, así que reemplace el valor de marcador de posición entre corchetes por su propio valor único: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Tenga en cuenta que el identificador de SQL Server es similar a  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.```. Utilizará el identificador de SQL Server en el paso siguiente. 

## <a name="create-the-private-endpoint"></a>Creación del punto de conexión privado 
Cree un punto de conexión privado para el servidor de SQL Database en la red virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configuración de la zona DNS privada 
Cree una zona DNS privada para el dominio del servidor de SQL Database y cree un vínculo de asociación con la red virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Conexión a una máquina virtual desde Internet

Conéctese a la máquina virtual *myVm* desde Internet de la siguiente manera:

1. En la barra de búsqueda del portal, escriba *myVm*.

1. Seleccione el botón **Conectar**. Después de seleccionar el botón **Conectar**, se abre **Conectar a máquina virtual**.

1. Seleccione **Descargar archivo RDP**. Azure crea un archivo de Protocolo de Escritorio remoto ( *.rdp*) y lo descarga en su equipo.

1. Abra el archivo downloaded.rdp*.

    1. Cuando se le pida, seleccione **Conectar**.

    1. Escriba el nombre de usuario y la contraseña que especificó al crear la VM.

        > [!NOTE]
        > Es posible que tenga que seleccionar **Más opciones** > **Usar otra cuenta** para especificar las credenciales que escribió al crear la máquina virtual.

1. Seleccione **Aceptar**.

1. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Si recibe una advertencia de certificado, seleccione **Sí** o **Continuar**.

1. Una vez que aparezca el escritorio de la máquina virtual, minimícelo para volver a su escritorio local.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Acceso al servidor de SQL Database de forma privada desde la máquina virtual

En esta sección, se conectará al servidor de SQL Database desde la máquina virtual mediante el punto de conexión privado.

 1. En el Escritorio remoto de  *myVm*, abra PowerShell.
 2. Escriba nslookup myserver.database.windows.net  Recibirá un mensaje similar al siguiente: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Instalación de SQL Server Management Studio 
 4. En Conectar con el servidor, escriba o seleccione esta información: Tipo de servidor: Seleccione Motor de base de datos.
 Nombre del servidor: Seleccione myserver.database.windows.net Nombre de usuario: escriba un nombre de usuario proporcionado durante la creación.
 Contraseña: escriba una contraseña proporcionada durante la creación.
 Recordar contraseña: seleccione Sí.
 
 5. Seleccione  **Conectar**.
 6. Examine las **Bases de datos** en el menú izquierdo.
 7. (Opcionalmente) Cree o consulte información de *mydatabase*.
 8. Cierre la conexión de Escritorio remoto a *myVm*.

## <a name="clean-up-resources"></a>Limpieza de recursos 
Cuando ya no se necesite, puede utilizar az group delete para quitar el grupo de recursos y todos los recursos que contiene: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Azure Private Link](private-link-overview.md).
 
