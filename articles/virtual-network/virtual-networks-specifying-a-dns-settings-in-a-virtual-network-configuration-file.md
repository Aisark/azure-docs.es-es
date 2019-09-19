---
title: Especificación de una configuración DNS en un archivo de configuración de red virtual | Microsoft Docs
description: Cómo cambiar la configuración del servidor DNS en una red virtual con un archivo de configuración de red virtual en el modelo de implementación clásico
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: c15d73673c19383deabe15ef30026990dfd138b9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059088"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificación de una configuración DNS en un archivo de configuración de red virtual
Un archivo de configuración de red tiene dos elementos que puede usar para especificar la configuración del sistema de nombres de dominio (DNS): **DnsServers** y **DnsServerRef**. Puede agregar una lista de los servidores DNS especificando sus direcciones IP y nombres de referencia en el elemento **DnsServers** . Asimismo, puede usar un elemento **DnsServerRef** para especificar qué entradas del servidor DNS desde el elemento DnsServers se usan en diferentes sitios de red de la red virtual.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación clásico.

El archivo de configuración de red puede contener los siguientes elementos. El título de cada elemento se vincula a una página que proporciona información adicional acerca de la configuración del valor de elemento.

> [!IMPORTANT]
> Para obtener más información sobre cómo configurar el archivo de configuración de red, consulte [Configuración de una red virtual mediante un archivo de configuración de red](virtual-networks-using-network-configuration-file.md). Para obtener más información acerca de cada elemento de un archivo de configuración de red, consulte [Esquema de configuración de redes virtuales de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> El atributo **name** en el elemento **DnsServer** solo se usa como referencia del elemento **DnsServerRef**. No representa el nombre de host del servidor DNS. Cada valor del atributo **DnsServer** debe ser único en toda la suscripción de Microsoft Azure
> 
> 

[Elemento Sitios de red virtual](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Para especificar la configuración del elemento Sitios de red virtual, debe definirse previamente en el elemento DNS. El atributo *name* DnsServerRef del elemento Sitios de red virtual tiene que hacer referencia a un valor de nombre especificado en el elemento DNS del atributo *name* DnsServer.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Entienda el [esquema de configuración de la instancia de Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093).
* Entienda el [esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configuración de una red virtual mediante un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

