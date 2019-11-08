---
title: ¿Qué es Azure Private Link?
description: Aprenda a usar Azure Private Link para acceder a los servicios PaaS de Azure (por ejemplo, Azure Storage y SQL Database) y a los servicios de asociados o clientes hospedados en Azure a través de un punto de conexión privado de la red virtual.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: overview
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9e6206e6f7e09ebe03659eb6e515a9b26d3469bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475945"
---
# <a name="what-is-azure-private-link-preview"></a>¿Qué es Azure Private Link? (versión preliminar)
Azure Private Link le permite acceder a los servicios PaaS de Azure (por ejemplo, Azure Storage, Azure Cosmos DB y SQL Database) y a los servicios de asociados o clientes hospedados de cliente o asociado mediante un [punto de conexión privado](private-endpoint-overview.md) de la red virtual. El tráfico entre la red virtual y el servicio atraviesa la red troncal de Microsoft, eliminando la exposición a la red pública de Internet. También puede crear su propio [servicio Private Link](private-link-service-overview.md) en la red virtual y enviarlo de forma privada a los clientes. La experiencia de configuración y consumo con Azure Private Link es coherente en los servicios compartidos de PaaS de Azure, de propiedad del cliente y de asociados.

> [!IMPORTANT]
> Esta versión preliminar pública se proporciona sin un acuerdo de nivel de servicio y no debe usarse para cargas de trabajo de producción. Puede que algunas características no se admitan, que tengan funcionalidades limitadas o que no estén disponibles en todas las ubicaciones de Azure. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obtener información sobre las limitaciones conocidas, consulte [Punto de conexión privado](private-endpoint-overview.md#limitations) y [Servicio Private Link](private-link-service-overview.md#limitations).


![Introducción al punto de conexión privado](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Ventajas principales
Azure Private Link proporciona las ventajas siguientes:  
- **Acceso privado a servicios en la plataforma Azure**: conecte la red virtual a los servicios que se ejecutan en Azure de forma privada sin necesidad de una dirección IP pública en el origen o el destino. Los proveedores de servicios pueden representar los servicios de forma privada en su propia red virtual y los consumidores pueden acceder a esos servicios de forma privada en su red virtual local. La plataforma Private Link administrará la conectividad entre el consumidor y los servicios a través de la red troncal de Azure. 
 
- **Redes locales y emparejadas**: acceda a los servicios que se ejecutan en Azure desde el entorno local a través del emparejamiento privado de ExpressRoute y los túneles VPN (desde el entorno local) y las redes virtuales emparejadas mediante puntos de conexión privados. No es necesario configurar el emparejamiento público ni atravesar Internet para alcanzar el servicio. Esta capacidad proporciona una manera segura de migrar cargas de trabajo a Azure.
 
- **Protección contra la filtración de datos**:  con Azure Private Link, el punto de conexión privado de la red virtual se asigna a una instancia específica del recurso de PaaS del cliente, en lugar de a todo el servicio. El uso de los consumidores del punto de conexión privado solo puede conectarse al recurso específico y no a ningún otro recurso del servicio. Este mecanismo integrado proporciona protección frente a los riesgos de la filtración de datos. 
 
- **Alcance global**: conéctese de forma privada a los servicios que se ejecutan en otras regiones. Esto significa que la red virtual del consumidor podría estar en la región A y puede conectarse a los servicios detrás de Private Link en la región B.  
 
- **Ampliación de los propios servicios**: aproveche la misma experiencia y funcionalidad para presentar su propio servicio de forma privada a los consumidores de Azure. Al colocar el servicio detrás de una instancia de Standard Load Balancer, puede habilitarlo para Private Link. Después, el consumidor puede conectarse directamente al servicio mediante un punto de conexión privado en su propia red virtual. Puede administrar estas solicitudes de conexión mediante un flujo de llamadas de aprobación simple. Azure Private Link funciona también para consumidores y servicios que pertenecen a distintos inquilinos de Active Directory. 

## <a name="availability"></a>Disponibilidad 
 En la tabla siguiente se enumeran los servicios de Private Link y las regiones en las que están disponibles. 

|Escenario  |Servicios admitidos   |Regiones disponibles | Status   |
|---------|---------|---------|---------|
|Private Link para servicios propiedad del cliente|Servicios Private Link detrás de Standard Load Balancer |Centro-oeste de EE. UU., Oeste de EE. UU., Centro-sur de EE. UU., Este de EE. UU. y Centro-norte de EE. UU.  |  Vista previa  |
|Private Link para los servicios PaaS de Azure   | Azure Storage        |  Este de EE. UU., Oeste de EE. UU., Centro-oeste de EE. UU.       | Vista previa         |
|  | Azure Data Lake Storage Gen2        |  Este de EE. UU., Oeste de EE. UU., Centro-oeste de EE. UU.       | Vista previa         |
|  |  Azure SQL Database         | Centro-oeste de EE. UU., Oeste de EE. UU., Centro-sur de EE. UU., Este de EE. UU. y Centro-norte de EE. UU.      |   Vista previa      |
||Azure SQL Data Warehouse| Centro-oeste de EE. UU., Oeste de EE. UU., Centro-sur de EE. UU., Este de EE. UU. y Centro-norte de EE. UU. |Vista previa|
||Azure Cosmos DB| Centro-oeste de EE. UU., Oeste de EE. UU., Este de EE. UU., Centro-norte de EE. UU., Este de EE. UU 2; Oeste de EE. UU. 2 |Vista previa|

Para conocer las notificaciones más actualizadas, consulte la página [Actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Registro y supervisión

Azure Private Link se integra en Azure Monitor, lo que permite archivar registros en una cuenta de almacenamiento, transmitir eventos al centro de eventos o enviarlos a los registros de Azure Monitor. Puede tener acceso a la siguiente información sobre Azure Monitor: 
- **Punto de conexión privado**: datos procesados por el punto de conexión privado (IN/OUT)
 
- **Servicio Private Link**:
    - datos procesados por el servicio Private Link (IN/OUT)
    - Disponibilidad del puerto NAT  
 
## <a name="pricing"></a>Precios   
Para más información sobre los precios, consulte [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Preguntas más frecuentes  
Para ver las preguntas más frecuentes, consulte [Preguntas más frecuentes sobre Azure Private Link](private-link-faq.md).
 
## <a name="limits"></a>límites  
Para conocer los límites, consulte [Límites de Azure Private Link](../azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Pasos siguientes
- [Creación de un punto de conexión privado para el servidor de SQL Database mediante el portal](create-private-endpoint-portal.md)
- [Creación de un punto de conexión privado para el servidor de SQL Database mediante PowerShell](create-private-endpoint-powershell.md)
- [Creación de un punto de conexión privado para el servidor de SQL Database mediante la CLI](create-private-endpoint-cli.md)
- [Creación de un punto de conexión privado para la cuenta de almacenamiento mediante el portal](create-private-endpoint-storage-portal.md)
- [Creación de un punto de conexión privado para la cuenta de Azure Cosmos mediante el portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Creación del propio servicio Private Link con Azure PowerShell](create-private-link-service-powershell.md)


 
