---
title: 'Arquitectura de conectividad en Azure Database for PostgreSQL: servidor único'
description: 'Describe la arquitectura de conectividad de la instancia de Azure Database for PostgreSQL: servidor único.'
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 680908fdb2b7badcc1bbf713805b638213590877
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508091"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Arquitectura de la conectividad en Azure Database for PostgreSQL
En este artículo se explica la arquitectura de la conectividad de Azure Database for PostgreSQL y cómo se dirige el tráfico a la instancia de base de datos de Azure Database for PostgreSQL desde los clientes de dentro y de fuera de Azure.

## <a name="connectivity-architecture"></a>Arquitectura de conectividad
La conexión a la base de datos de Azure Database for PostgreSQL se establece a través de una puerta de enlace que se encarga de enrutamiento de las conexiones entrantes a la ubicación física del servidor en nuestros clústeres. En el diagrama siguiente se muestra este flujo de tráfico.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Información general de la arquitectura de conectividad":::


Cuando el cliente se conecta a la base de datos, la cadena de conexión al servidor se resuelve en la dirección IP de la puerta de enlace. La puerta de enlace escucha en la dirección IP en el puerto 5432. Dentro del clúster de bases de datos, el tráfico se reenvía a la instancia de Azure Database for PostgreSQL adecuada. Por tanto, para conectarse al servidor, como en las redes corporativas, es necesario abrir el **firewall del lado cliente para permitir que el tráfico saliente llegue a nuestras puertas de enlace**. A continuación encontrará una lista completa de las direcciones IP que usan nuestras puertas de enlace por región.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Direcciones IP de la puerta de enlace de Azure Database for PostgreSQL

El servicio de puerta de enlace se hospeda en un grupo de nodos de proceso sin estado que se encuentran detrás de una dirección IP, con la que el cliente se comunicaría primero al intentar conectarse a un servidor de Azure Database for PostgreSQL. 

Como parte del mantenimiento continuo del servicio, actualizaremos periódicamente el hardware de proceso que hospeda las puertas de enlace para garantizar que se proporcione la experiencia más segura y eficaz. Cuando se actualice el hardware de la puerta de enlace, primero se creará un nuevo anillo de los nodos de proceso. Este nuevo anillo atiende el tráfico de todos los servidores de Azure Database for PostgreSQL recién creados, y tendrá una dirección IP diferente de los anillos de puertas de enlace más antiguos de la misma región a los efectos de diferenciar el tráfico. Según los planes, una vez que el nuevo anillo esté en pleno funcionamiento, se retirará el hardware de puertas de enlace anterior que atiende a los servidores existentes. Antes de retirar el hardware de una puerta de enlace, los clientes que ejecuten sus servidores y se conecten a los anillos de puertas de enlace más antiguos recibirán una notificación por correo electrónico y en Azure Portal, tres meses antes de la retirada. La retirada de las puertas de enlace puede afectar a la conectividad a los servidores si: 

* Codifica de forma rígida las direcciones IP de las puertas de enlace en la cadena de conexión de la aplicación. **No se recomienda**. 
* No actualiza las direcciones IP de las puertas de enlace más recientes en el firewall del lado cliente para permitir que el tráfico de salida pueda comunicarse con nuestros nuevos anillos de puertas de enlace.

En la siguiente tabla se enumeran las direcciones IP de las puertas de enlace de Azure Database for PostgreSQL para todas las regiones de datos. La información más actualizada de las direcciones IP de las puertas de enlace para cada región se mantiene en la tabla siguiente. Las columnas representan lo siguiente:

* **Direcciones IP de puerta de enlace:** Esta columna muestra las direcciones IP actuales de las puertas de enlace hospedadas en la última generación de hardware. Si está aprovisionando un nuevo servidor, se recomienda que abra el firewall del lado cliente para permitir el tráfico saliente para las direcciones IP que se enumeran en esta columna.
* **Direcciones IP de puerta de enlace (en retirada):** Esta columna muestra las direcciones IP de las puertas de enlace hospedadas en una generación anterior de hardware que se está retirando en este momento. Si está aprovisionando un nuevo servidor, puede omitir estas direcciones IP. Si ya tiene un servidor, siga conservando la regla de salida para el firewall para estas direcciones IP, ya que aún no se ha retirado. Si quita las reglas de firewall para estas direcciones IP, es posible que se produzcan errores de conectividad. En su lugar, se espera que agregue de forma proactiva las nuevas direcciones IP que aparecen en la columna Direcciones IP de puerta de enlace a la regla de firewall de salida en cuanto reciba la notificación de retirada. Esto garantizará que, cuando el servidor se migre al hardware de puertas de enlace más reciente, no haya ninguna interrupción en la conectividad con el servidor.
* **Direcciones IP de puerta de enlace (retirada):** En esta columna se enumeran las direcciones IP de los anillos de puertas de enlace que se han retirado y ya no están en funcionamiento. Puede quitar con tranquilidad estas direcciones IP de la regla de firewall de salida. 


| **Nombre de la región** | **Direcciones IP de puerta de enlace** |**Direcciones IP de puerta de enlace (en retirada)** | **Direcciones IP de puerta de enlace (retirada)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Centro de Australia| 20.36.105.0  | | |
| Centro de Australia 2     | 20.36.113.0  | | |
| Este de Australia | 13.75.149.87, 40.79.161.1     |  | |
| Sudeste de Australia |191.239.192.109, 13.73.109.251   |  | |
| Sur de Brasil |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Centro de Canadá |40.85.224.249  | | |
| Este de Canadá | 40.86.226.166    | | |
| Centro de EE. UU. | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     | | |
| Este de China | 139.219.130.35    | | |
| Este de China 2 | 40.73.82.1  | | |
| Norte de China | 139.219.15.17    | | |
| Norte de China 2 | 40.73.50.0     | | |
| Este de Asia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| Este de EE. UU. |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Este de EE. UU. 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  | | |
| Centro de Francia | 40.79.137.0, 40.79.129.1  | | |
| Sur de Francia | 40.79.177.0     | | |
| Centro de Alemania | 51.4.144.100     | | |
| Nordeste de Alemania | 51.5.144.179  | | |
| India central | 104.211.96.159     | | |
| Sur de India | 104.211.224.146  | | |
| India occidental | 104.211.160.80    | | |
| Japón Oriental | 13.78.61.196, 191.237.240.43, 40.79.192.23 | | |
| Japón Occidental | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    | | |
| Centro de Corea del Sur | 52.231.32.42   | | |
| Corea del Sur | 52.231.200.86    | | |
| Centro-Norte de EE. UU | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    | | |
| Norte de Europa | 52.138.224.6, 52.138.224.7  |40.113.93.91 |191.235.193.75 |
| Norte de Sudáfrica  | 102.133.152.0    | | |
| Oeste de Sudáfrica | 102.133.24.0   | | |
| Centro-sur de EE. UU. |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Sudeste de Asia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     | | |
| Centro de Emiratos Árabes Unidos | 20.37.72.64  | | |
| Norte de Emiratos Árabes Unidos | 65.52.248.0    | | |
| Sur de Reino Unido | 51.140.184.11   | | |
| Oeste de Reino Unido | 51.141.8.11  | | |
| Centro-Oeste de EE. UU. | 13.78.145.25     | | |
| Oeste de Europa |13.69.105.208,104.40.169.187 |40.68.37.158 | 191.237.232.75|
| Oeste de EE. UU. |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Oeste de EE. UU. 2 | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Pasos siguientes

* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](./howto-manage-firewall-using-portal.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal)
* [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure)
