---
title: Administración de puntos de conexión privados en Azure
description: Obtenga información sobre cómo crear un punto de conexión privado mediante Azure Portal.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 95dbe2b9ccfdc0a900ba53668869e3872b6e51d5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030313"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Creación de un punto de conexión privado mediante Azure Portal

Un punto de conexión privado es el bloque de creación fundamental para el vínculo privado en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen de manera privada con recursos de vínculos privados. En este inicio rápido, obtendrá información sobre cómo crear una máquina virtual en una instancia de Azure Virtual Network, un servidor de SQL Database con un punto de conexión privado de Azure mediante Azure PowerShell. Después, puede acceder de forma segura al servidor de SQL Database desde la máquina virtual.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


> [!NOTE]
> No se permiten puntos de conexión privados junto con los puntos de conexión de servicio en la misma subred.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-vm"></a>Crear una VM
En esta sección, va a crear una red virtual y una subred para hospedar la máquina virtual que se usa para acceder al recurso de Private Link (un servidor SQL en Azure en este ejemplo).

### <a name="create-the-virtual-network"></a>Crear la red virtual


En esta sección, va a crear una red virtual y una subred para hospedar la máquina virtual que se usa para acceder al recurso de Private Link.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso** > **Redes** > **Red virtual**.
1. En **Creación de una red virtual**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | NOMBRE | Escriba *MyVirtualNetwork*. |
    | Espacio de direcciones | Escriba *10.1.0.0/16*. |
    | Subscription | Seleccione su suscripción.|
    | Resource group | Seleccione **Crear nuevo**, escriba *myResourceGroup* y, después, seleccione **Aceptar**. |
    | Location | Seleccione **WestCentralUS**.|
    | Subred: nombre | Escriba *mySubnet*. |
    | Subred: intervalo de direcciones | Escriba *10.1.0.0/24*. |
    |||
1. Deje el resto tal como está y seleccione **Crear**.


### <a name="create-virtual-machine"></a>Creación de la máquina virtual

1. En la parte superior izquierda de Azure Portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual**.

1. En **Creación de una máquina virtual: conceptos básicos**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | **DETALLES DEL PROYECTO** | |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. Lo creó en la sección anterior.  |
    | **DETALLES DE INSTANCIA** |  |
    | Nombre de la máquina virtual | Escriba *myVm*. |
    | Region | Seleccione **WestCentralUS**. |
    | Opciones de disponibilidad | Deje el valor predeterminado **No se requiere redundancia de la infraestructura**. |
    | Imagen | Seleccione **Windows Server 2019 Datacenter**. |
    | Size | Deje el valor predeterminado **Estándar DS1 v2**. |
    | **CUENTA DE ADMINISTRADOR** |  |
    | Nombre de usuario | Escriba un nombre de usuario de su elección. |
    | Contraseña | Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Vuelva a escribir la contraseña. |
    | **REGLAS DE PUERTO DE ENTRADA** |  |
    | Puertos de entrada públicos | Deje el valor predeterminado **Ninguno**. |
    | **AHORRE DINERO** |  |
    | ¿Ya tiene una licencia de Windows? | Deje el valor predeterminado **No**. |
    |||

1. Seleccione **Siguiente: Discos**.

1. En **Creación de una máquina virtual: Discos**, deje los valores predeterminados y seleccione **Siguiente: Redes**.

1. En **Creación de una máquina virtual: Redes**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | Virtual network | Deje el valor predeterminado **MyVirtualNetwork**.  |
    | Espacio de direcciones | Deje el valor predeterminado **10.1.0.0/24**.|
    | Subnet | Deje el valor predeterminado **mySubnet (10.1.0.0/24)** .|
    | Dirección IP pública | Deje el valor predeterminado **(new) myVm-ip**. |
    | Puertos de entrada públicos | Seleccione **Permitir los puertos seleccionados**. |
    | Selección de puertos de entrada | Seleccione **HTTP** y **RDP**.|
    |||


1. Seleccione **Revisar + crear**. Se le remitirá a la página **Revisar y crear**, donde Azure validará la configuración.

1. Cuando reciba el mensaje **Validación superada**, seleccione **Crear**.

## <a name="create-a-sql-database-server"></a>Creación de un servidor de base de datos SQL
En esta sección, va a crear un servidor de SQL Database en Azure. 

1. En la parte superior izquierda de la pantalla en Azure Portal, seleccione **Crear un recurso** > **Bases de datos** > **SQL Database**.

1. En **Crear SQL Database: Conceptos básicos**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | **Detalles de la base de datos** | |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. Lo creó en la sección anterior.|
    | **DETALLES DE INSTANCIA** |  |
    | Nombre de la base de datos  | Escriba *miBaseDeDatos*. Si el nombre ya existe, cree uno único. |
    |||
5. En  **Servidor**, seleccione **Crear nuevo**. 
6. En  **Nuevo servidor**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    |Nombre de servidor  | Escriba *miServidor*. Si el nombre ya existe, cree uno único.|
    | Inicio de sesión de administrador de servidor| Escriba el nombre de administrador que prefiera. |
    | Contraseña | Escriba una contraseña de su elección. La contraseña debe tener al menos ocho caracteres y cumplir con los requisitos definidos. |
    | Location | Seleccione la región de Azure en la que desea que se encuentre la instancia de SQL Server. |
    
7. Seleccione **Aceptar**. 
8. Seleccione  **Revisar y crear**. Se le remite a la página **Revisar y crear** , donde Azure valida la configuración. 
9. Cuando reciba el mensaje Validación superada, seleccione  **Crear**. 
10. Cuando reciba el mensaje Validación superada, seleccione Crear. 

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

En esta sección, creará un servidor SQL Server y le agregará un punto de conexión privado. 

1. En la parte superior izquierda de la pantalla en Azure Portal, seleccione **Crear un recurso** > **Redes** > **Private Link Center (versión preliminar)** .
2. En **Centro de vínculos privados: Información general**, en la opción**Build a private connection to a service** (Crear una conexión privada a un servicio), seleccione **Start** (Iniciar).
1. En **Create a private endpoint (Preview) - Basics** (Crear un punto de conexión privado [versión preliminar]: Conceptos básicos), escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | **Detalles del proyecto** | |
    | Subscription | Seleccione su suscripción. |
    | Resource group | Seleccione **myResourceGroup**. Lo creó en la sección anterior.|
    | **DETALLES DE INSTANCIA** |  |
    | NOMBRE | Escriba * miPuntoDeConexiónPrivado*. Si el nombre ya existe, cree uno único. |
    |Region|Seleccione **WestCentralUS**.|
    |||
5. Seleccione **Next: Resource** (Siguiente: Recurso).
6. En **Create a private endpoint - Resource** (Crear un punto de conexión privado: Recurso), escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    |Método de conexión  | Seleccione Connect to an Azure resource in my directory (Conectarse a un recurso de Azure en mi directorio).|
    | Subscription| Seleccione su suscripción. |
    | Tipo de recurso | Seleccione **Microsoft.Sql/servers**. |
    | Resource |Seleccione *miServidor*.|
    |Recurso secundario de destino |Seleccione *sqlServer*.|
    |||
7. Seleccione **Next: Configuration** (Siguiente: Configuración).
8. En **Create a private endpoint (Preview) - Configuración** (Crear un punto de conexión privado [versión preliminar]: Configuración), escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    |**REDES**| |
    | Virtual network| Seleccione *MyVirtualNetwork*. |
    | Subnet | Seleccione  *mySubnet*. |
    |**INTEGRACIÓN DE DNS PRIVADO**||
    |Integración con una zona DNS privada |Seleccione **Sí**. |
    |Zona DNS privada |Seleccione *(New)privatelink.database.windows.net* |
    |||

1. Seleccione **Revisar y crear**. Se le remite a la página  **Revisar y crear** , donde Azure valida la configuración. 
2. Cuando reciba el mensaje **Validación superada** , seleccione  **Crear**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Conéctese a una máquina virtual mediante Escritorio remoto (RDP)


Después de crear **myVm*, conéctese a ella desde Internet como se indica a continuación: 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Acceso al servidor de SQL Database de forma privada desde la máquina virtual

1. En el Escritorio remoto de *myVm*, abra PowerShell.

2. Escriba `nslookup myserver.database.windows.net`. 

    Recibirá un mensaje similar a este:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instale [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. En  **Conectar con el servidor**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | Tipo de servidor| Seleccione **Motor de base de datos**.|
    | Nombre de servidor| Seleccione *myserver.database.windows.net*. |
    | Nombre de usuario | Escriba una contraseña proporcionada durante la creación del servidor SQL Server. |
    |Contraseña |Escriba una contraseña proporcionada durante la creación del servidor SQL Server. |
    |Recordar contraseña|Seleccione **Sí**.|
    |||
1. Seleccione  **Conectar**.
2. Examine las bases de datos en el menú izquierdo.
3. (Opcionalmente) Cree o consulte la información de mydatabase.
4. Cierre la conexión de Escritorio remoto a  *myVm*. 

## <a name="clean-up-resources"></a>Limpieza de recursos 
Cuando haya terminado mediante el punto de conexión privado, el servidor SQL y la máquina virtual, elimine el grupo de recursos y todos los recursos que contiene: 
1. Escriba *myResourceGroup* en el cuadro  **Buscar**  de la parte superior del portal y seleccione  *myResourceGroup* en los resultados de búsqueda. 
2. Seleccione  **Eliminar grupo de recursos**. 
3. Escriba myResourceGroup para  **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una máquina virtual en una red virtual, un servidor de SQL Database y un punto de conexión privado para el acceso privado. Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma segura al servidor de SQL Database mediante el vínculo privado. Para más información sobre los puntos de conexión privados, consulte [¿Qué es un punto de conexión privado de Azure? ](private-endpoint-overview.md).

