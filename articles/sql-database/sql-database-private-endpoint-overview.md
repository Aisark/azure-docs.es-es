---
title: Private Link
description: Información general de la característica de punto de conexión privado
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: 6cc8282a5c56f8f45e8d9e5ee452089a74f0d4ed
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045628"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Private Link para Azure SQL Database y Data Warehouse (versión preliminar)

Private Link permite conectarse a varios servicios PaaS en Azure mediante un **punto de conexión privado**. Para obtener una lista de los servicios PaaS que admiten la funcionalidad Private Link, vaya a la página de la [documentación de Private Link](../private-link/index.yml). Un punto de conexión privado es una dirección IP privada dentro de una [red virtual](../virtual-network/virtual-networks-overview.md) y una subred específicas. 

> [!IMPORTANT]
> Este artículo se aplica a Azure SQL Server y tanto a las bases de datos de SQL Database como a SQL Data Warehouse que se crean en el servidor de Azure SQL. Para simplificar, SQL Database se utiliza cuando se hace referencia tanto a SQL Database como a SQL Data Warehouse. Este artículo *no* es válido para la implementación de **instancias administradas** en Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Prevención de la filtración de datos

La filtración de datos en Azure SQL Database tiene lugar cuando un usuario autorizado, como un administrador de base de datos, puede extraer datos de un sistema y moverlos a una ubicación o sistema que está fuera de la organización. Por ejemplo, el usuario mueve los datos a una cuenta de almacenamiento propiedad de un tercero.

Piense en un escenario en el que un usuario ejecuta SQL Server Management Studio (SSMS) dentro de una máquina virtual de Azure que se conecta a una base de datos SQL. Esta base de datos SQL se encuentra en el centro de datos de Oeste de EE. UU. En el ejemplo siguiente se muestra cómo limitar el acceso con puntos de conexión públicos en la base de datos SQL mediante controles de acceso a la red.

1. Deshabilite todo el tráfico de los servicios de Azure a la base de datos SQL mediante el punto de conexión público, para lo que debe seleccionar **OFF** (Desactivado) en Allow Azure Services (Permitir servicios de Azure). Asegúrese de que no se permiten direcciones IP en el servidor y en las reglas de firewall en el nivel de base de datos. Para más información, consulte [Controles de acceso a la red para Azure SQL Database y Data Warehouse](sql-database-networkaccess-overview.md).
1. Solo se permite el tráfico a SQL Database mediante la dirección IP privada de la máquina virtual. Para más información, consulte los artículos acerca del [punto de conexión de servicio](sql-database-vnet-service-endpoint-rule-overview.md) y de las [reglas de firewall de la red virtual](sql-database-firewall-configure.md).
1. En la máquina virtual de Azure, restrinja el ámbito de la conexión saliente mediante el uso de [grupos de seguridad de red (NSG)](../virtual-network/manage-network-security-group.md) y etiquetas de servicio, como se indica a continuación
    - Especifique una regla de NSG para permitir el tráfico en Service Tag = SQL.WestUs (solo se permite la conexión a una base de datos SQL en Oeste de EE. UU.)
    - Especifique una regla de NSG (con una **prioridad mayor**) para denegar el tráfico a Service Tag = SQL (se deniegan las conexiones a una base de datos SQL en todas las regiones)

Al final de esta configuración, la máquina virtual de Azure solo puede conectarse a las bases de datos SQL de la región Oeste de EE. UU. Sin embargo, la conectividad no está restringida a una sola base de datos SQL. La máquina virtual puede conectarse a todas las bases de datos SQL de la región Oeste de EE. UU., incluidas las bases de datos que no forman parte de la suscripción. Aunque en el escenario anterior se ha reducido el ámbito de la filtración de datos a una región concreta, no se ha eliminado por completo.

Con Private Link, los clientes pueden configurar controles de acceso a la red como grupos de seguridad de red para restringir el acceso al punto de conexión privado. Los recursos PaaS de Azure individuales se asignan a puntos de conexión privados concretos. Un usuario interno malintencionado solo puede acceder al recurso PaaS asignado (por ejemplo, una base de datos SQL), pero no a los restantes recursos. 

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividad local a través del emparejamiento privado

Cuando los clientes se conectan al punto de conexión público desde equipos locales, es necesario agregar su dirección IP al firewall basado en IP mediante una [regla de firewall de nivel de servidor](sql-database-server-level-firewall-rule.md). Aunque este modelo funciona bien para permitir el acceso a equipos individuales para las cargas de trabajo de desarrollo o de prueba, es difícil de administrar en los entornos de producción.

Con Private Link, los clientes pueden habilitar el acceso entre locales al punto de conexión privado mediante [ExpressRoute](../expressroute/expressroute-introduction.md), el emparejamiento privado o la tunelización de VPN. Luego, los clientes pueden deshabilitar todo el acceso a través del punto de conexión público y no usar el firewall basado en IP para permitir direcciones IP.

Con Private Link, los clientes pueden habilitar el acceso entre locales al punto de conexión privado mediante ExpressRoute (ER), el emparejamiento privado o la tunelización de VPN. Posteriormente, pueden deshabilitar todo el acceso a través de puntos de conexión privados y no usar el firewall basado en IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Configuración de Private Link para Azure SQL Database 

### <a name="creation-process"></a>Proceso de creación
Los puntos de conexión privados se pueden crear mediante el portal, PowerShell o la CLI de Azure:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Proceso de aprobación
Una vez que el administrador de red crea el punto de conexión privado (PE), el administrador de SQL puede administrar la conexión de punto de conexión privado (PEC) a la base de datos SQL.

1. Vaya al recurso de SQL Server en Azure Portal según los pasos que se muestran en la captura de pantalla siguiente.

    - (1) Seleccione las conexiones del punto de conexión privado en el panel izquierdo
    - (2) Muestra una lista de todas las conexiones del punto de conexión privado (PEC)
    - (3) Punto de conexión privado (PE) correspondiente creado ![Captura de pantalla de todos los PEC][3]

1. Seleccione una conexión del punto de conexión privado en la lista.
![Captura de pantalla del PEC seleccionado][6]

1. El administrador de SQL puede elegir aprobar o rechazar cualquier conexión de punto de conexión privado y, además, tiene la opción de agregar una respuesta con un texto breve.
![Captura de pantalla de aprobación de conexión de punto de conexión privado][4]

1. Después de la aprobación o el rechazo, la lista reflejará el estado apropiado, junto con el texto de respuesta.
![Captura de pantalla de todas las conexiones del punto de conexión privado después de la aprobación][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Casos de uso de Private Link para Azure SQL Database 

Los clientes se pueden conectar al punto de conexión privado desde la misma red virtual, desde una red virtual emparejada de la misma región o a través de una conexión entre redes virtuales de distintas regiones. Además, los clientes pueden conectarse de forma local mediante ExpressRoute, emparejamiento privado o tunelización de VPN. A continuación, puede ver un diagrama simplificado que muestra los casos de uso habituales.

 ![Diagrama de las opciones de conectividad][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Prueba de la conectividad con SQL Database desde una máquina virtual de Azure que se encuentre en la misma red virtual

Para este escenario, suponga que ha creado una máquina virtual de Azure en la que se ejecuta Windows Server 2016. 

1. [Inicie una sesión de Escritorio remoto (RDP) y conéctese a la máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Después, puede realizar algunas comprobaciones de conectividad básicas para asegurarse de que la máquina virtual se conecta a SQL Database a través del punto de conexión privado. Para ello, utilice estas herramientas:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Comprobación de la conectividad mediante Telnet

El [cliente Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) es una característica de Windows que se puede usar para probar la conectividad. En función de la versión del sistema operativo Windows, es posible que tenga que habilitar esta característica de forma explícita. 

Tras instalar Telnet, abra una ventana del símbolo del sistema. Ejecute el comando de Telnet y especifique la dirección IP y el punto de conexión privado de la base de datos SQL.

```
>telnet 10.1.1.5 1433
```

Cuando Telnet se conecte correctamente, verá una pantalla en blanco en la ventana de comandos como la de la siguiente imagen:

 ![Diagrama de Telnet][2]

### <a name="check-connectivity-using-psping"></a>Comprobación de la conectividad mediante Psping

[Psping](/sysinternals/downloads/psping) se puede usar como se indica a continuación para comprobar que la conexión de punto de conexión privado (PEC) escucha conexiones en el puerto 1433.

Ejecute psping como se indica a continuación y especifique el nombre de dominio completo del servidor de SQL Database y el puerto 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

La salida muestra que Psping puede hacer ping a la dirección IP privada asociada al PEC.

### <a name="check-connectivity-using-nmap"></a>Comprobación de la conectividad mediante Nmap

Nmap (asignador de red) es una herramienta gratuita de código abierto que se usa para la detección de redes y las auditorías de seguridad. Para más información y para obtener el vínculo de descarga, visite https://nmap.org. Esta herramienta se puede usar para asegurarse de que el punto de conexión privado escucha conexiones en el puerto 1433.

Ejecute Nmap como se indica a continuación y especifique el intervalo de direcciones de la subred que hospeda el punto de conexión privado.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

El resultado muestra que una dirección IP está activa, la cual corresponde a la dirección IP del punto de conexión privado.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Comprobación de la conectividad mediante SQL Server Management Studio (SSMS)
> [!NOTE]
>Use el **nombre de dominio completo (FQDN)** del servidor en cadenas de conexión para los clientes. Cualquier intento de inicio de sesión realizado directamente en la dirección IP generará un error por diseño.

Siga los pasos para usar [SSMS para conectarse a SQL Database](sql-database-connect-query-ssms.md). Tras conectarse a SQL Database mediante SSMS, compruebe que ha establecido la conexión desde la dirección IP de la máquina virtual de Azure, para lo que debe ejecutar la siguiente consulta:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> En la versión preliminar, las conexiones a un punto de conexión privado solo admiten**Proxy** como [directiva de conexión](sql-database-connectivity-architecture.md#connection-policy).


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Conexión desde una máquina virtual de Azure en una red virtual emparejada (VNet) 

Configure el [emparejamiento de VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para establecer la conectividad con SQL Database desde una máquina virtual de Azure en una red virtual emparejada.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Conexión desde una máquina virtual de Azure en un entorno de red virtual a red virtual

Configure la [conexión de la puerta de enlace de VPN entre redes virtuales](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para establecer una conectividad con SQL Database desde una máquina virtual de Azure de otra región o suscripción.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Conexión desde un entorno local a través de VPN

Para establecer la conectividad desde un entorno local a SQL Database, elija e implemente una de las siguientes opciones:
- [Conexión de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Conexión VPN de sitio a sitio](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito de ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Conexión desde Azure SQL Data Warehouse a Azure Storage mediante Polybase

PolyBase se suele usar para cargar datos en Azure SQL Data Warehouse desde cuentas de Azure Storage. Si la cuenta de Azure Storage desde la que se cargan los datos limita el acceso a solo un conjunto de subredes de red virtual mediante puntos de conexión privados, puntos de conexión de servicio o firewalls basados en IP, se interrumpirá la conectividad entre PolyBase y la cuenta. Para habilitar los escenarios de importación y exportación de PolyBase con la conexión de Azure SQL Data Warehouse a Azure Storage protegido para la red virtual, siga los pasos que se indican [aquí](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 



## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre la seguridad de Azure SQL Database, vea [Protección de bases de datos SQL](sql-database-security-overview.md).
- Para obtener información general sobre la conectividad de Azure SQL Database, consulte [Arquitectura de conectividad de Azure SQL](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
