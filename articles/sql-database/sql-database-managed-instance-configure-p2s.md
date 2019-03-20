---
title: 'Configuración de P2S: Instancia administrada de Azure SQL Database| Microsoft Docs'
description: Conexión a una Instancia administrada de Azure SQL Database mediante SQL Server Management Studio con una conexión de punto a sitio desde un equipo cliente local.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 5830885a9502e716164f771771d88fb5d7e23047
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "57840416"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Inicio rápido: Configuración de una conexión de punto a sitio a una Instancia administrada de Azure SQL Database desde el entorno local

Esta guía de inicia rápido demuestra cómo conectarse a una Instancia administrada de Azure SQL Database mediante [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) desde un equipo cliente local con una conexión de punto a sitio. Para más información acerca de las conexiones de punto a sitio, consulte [Acerca de las conexiones VPN de punto a sitio](../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido:

- se usan los recursos creados en [Creación de una Instancia administrada](sql-database-managed-instance-get-started.md) como punto de partida.
- Se requiere PowerShell 5.1 y AZ PowerShell 1.4.0 o posterior en el equipo de cliente en el entorno local. Si es necesario, consulte las instrucciones para la [instalación del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Requiere la versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) en el equipo cliente local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Asociación de una puerta de enlace VPN a la red virtual de Instancia administrada

1. Abra PowerShell en el equipo de cliente en el entorno local.

2. Copie este script de PowerShell. Este script asocia una puerta de enlace VPN Gateway a la red virtual de Instancia administrada que creó en la guía de inicio rápido [Creación de una instancia administrada de Azure SQL](sql-database-managed-instance-get-started.md). El script hace lo siguiente:

   - Crea e instala certificados en la máquina cliente
   - Calcula el intervalo futuro de IP de subred de puerta de enlace VPN Gateway
   - Crea el elemento GatewaySubnet
   - Implementa la plantilla de Azure Resource Manager que asocia la puerta de enlace VPN Gateway a la subred VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Para usar el módulo de PowerShell de Azure Resource Manager en lugar de con el módulo de Az, use el siguiente cmdlet: `attachVPNGateway.ps1` en lugar de `attachVPNGatewayAz.ps1` cmdlet.

3. Pegue el script en la ventana de PowerShell y proporcione los parámetros requeridos. Los valores de `<subscriptionId>`, `<resourceGroup>` y `<virtualNetworkName>` deben coincidir con los que se usan en la guía de inicio rápido [Creación de una Instancia administrada](sql-database-managed-instance-get-started.md). El valor de `<certificateNamePrefix>` puede ser la cadena que prefiera.

4. Ejecute el script de PowerShell.

> [!IMPORTANT]
> No continúe hasta que se complete el script de PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Creación de una conexión VPN a la Instancia administrada

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Abra el grupo de recursos en el que creó la puerta de enlace de red virtual y, a continuación, abra el recurso de puerta de enlace de red virtual.
3. Seleccione **Configuración de punto a sitio** y, después, **Descargar cliente VPN**.

    ![Descarga del cliente VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. En el equipo cliente local, extraiga los archivos del archivo zip y, a continuación, abra la carpeta con los archivos extraídos.
5. Abra el "**WindowsAmd64** carpeta y abra el **VpnClientSetupAmd64.exe** archivo.
6. Si recibe un mensaje que indica que **Windows protegió su PC**, haga clic en **Más información** y, a continuación, haga clic en **Ejecutar de todas formas**.

    ![Instalación del cliente VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. En el cuadro de diálogo Control de cuentas de usuario, haga clic en **Sí** para continuar.
8. En el cuadro de diálogo que hacen referencia a la red virtual, seleccione **Sí** para instalar el cliente de VPN para la red virtual.

## <a name="connect-to-the-vpn-connection"></a>Conexión a VPN

1. Vaya a **VPN** en **red e Internet** en el equipo de cliente en el entorno local y seleccione la red virtual de instancia administrada para establecer una conexión a esta red virtual. En la siguiente imagen, la red virtual se denomina **MyNewVNet**.

    ![Conexión VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Seleccione **Conectar**.
3. En el cuadro de diálogo, seleccione **Conectar**.

    ![Conexión VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Cuando se le indique que Connection Manager necesita privilegios elevados para actualizar la tabla de rutas, elija **Continuar**.
5. Seleccione **Sí** en el cuadro de diálogo Control de cuentas de usuario para continuar.

   Ha establecido una conexión VPN a la red virtual de la Instancia administrada.

    ![Conexión VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Uso de SSMS para conectarse a la Instancia administrada

1. En el equipo cliente local, abra SQL Server Management Studio (SSMS).
2. En el cuadro de diálogo **Conectar a un servidor**, escriba el **nombre de host** completo para su Instancia administrada en el cuadro **Nombre del servidor**.
3. Seleccione **Autenticación de SQL Server**, proporcione su nombre de usuario y contraseña y, a continuación, seleccione **Conectar**.

    ![conexión ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Después de conectarse, podrá ver las bases de datos del usuario y del sistema en el nodo Bases de datos. También podrá ver varios objetos en los nodos Seguridad, Objetos del servidor, Replicación, administración, Agente SQL Server y XEvent Profiler.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una guía de inicio rápido que muestra cómo conectarse desde una máquina virtual de Azure, consulte [Configuración de una conexión de punto a sitio](sql-database-managed-instance-configure-p2s.md).
- Para obtener información general de las opciones de conexión para las aplicaciones, vea [Conexión de la aplicación a Instancia administrada de Azure SQL Database](sql-database-managed-instance-connect-app.md).
- Para restaurar una base de datos SQL Server existente desde el entorno local en una Instancia administrada, puede usar [Azure Database Migration Service (DMS) para la migración](../dms/tutorial-sql-server-to-managed-instance.md), o bien el [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar desde un archivo de copia de seguridad de base de datos.
