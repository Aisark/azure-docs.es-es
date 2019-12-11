---
title: Controlar el tráfico de entrada en v1
description: Aprenda a controlar el tráfico de entrada a una instancia de App Service Environment. Este documento solo se proporciona para los clientes que usan App Service Environment v1 heredado.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: aa43d44a691fa9151959e8817596bdfc9bba65f0
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687388"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Cómo controlar el tráfico de entrada a un entorno de App Service
## <a name="overview"></a>Información general
Se puede crear una instancia de App Service Environment **o bien** en una red virtual de Azure Resource Manager **o** en una [red virtual][virtualnetwork] del modelo de implementación clásica.  Una nueva red virtual y una nueva subred pueden definirse en el momento en que se crea un entorno de App Service.  También puede crearse un entorno de App Service en una red virtual y subred preexistentes.  Tras el cambio realizado en junio de 2016, los entornos ASE también se pueden implementar en redes virtuales que usen intervalos de direcciones públicas o espacios de direcciones de RFC1918 (es decir, direcciones privadas).  Para más información sobre la creación de una instancia de App Service Environment, consulte [Creación de una instancia de Azure App Service Environment][HowToCreateAnAppServiceEnvironment].

Siempre debe crearse un entorno de App Service dentro de una subred al proporcionar esta un límite de red que puede utilizarse para bloquear el tráfico entrante tras dispositivos y servicios ascendentes de forma que solo se acepta el tráfico HTTP y HTTPS de determinadas direcciones IP ascendentes.

El tráfico de red entrante y saliente de una subred se controla mediante un [grupo de seguridad de red][NetworkSecurityGroups]. Para controlar el tráfico entrante, se requiere la creación de reglas de seguridad de red en un grupo de seguridad de red y, a continuación, la asignación a este de la subred que contiene el entorno de App Service.

Una vez que un grupo de seguridad de red se asigna a una subred, el tráfico entrante a aplicaciones en el entorno de App Service se permite o bloquea según las reglas de permiso y denegación definidas en el grupo de seguridad de red.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Puertos de red usados en un entorno de App Service
Antes de bloquear el tráfico de red entrante con un grupo de seguridad de red, es importante conocer el conjunto de puertos de red necesarios y opcionales utilizados por un entorno de App Service.  El bloqueo accidental del tráfico a algunos puertos puede producir la pérdida de funcionalidad en un entorno de App Service.

A continuación se muestra una lista de puertos utilizados por un entorno de App Service. Todos los puertos son **TCP**, a menos que indique claramente:

* 454:  **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener instancias de App Service Environment a través de SSL.  No bloquee el tráfico a este puerto.  Este puerto siempre está enlazado a la dirección VIP pública de un ASE.
* 455:  **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener instancias de App Service Environment a través de SSL.  No bloquee el tráfico a este puerto.  Este puerto siempre está enlazado a la dirección VIP pública de un ASE.
* 80:  el puerto predeterminado para el tráfico HTTP entrante a aplicaciones que se ejecutan en planes de App Service en una instancia de App Service Environment.  En un ASE con un ILB, este puerto está enlazado a la dirección del ILB del ASE.
* 443: el puerto predeterminado para el tráfico SSL entrante a aplicaciones que se ejecutan en planes de App Service en una instancia de App Service Environment.  En un ASE con un ILB, este puerto está enlazado a la dirección del ILB del ASE.
* 21:  canal de control para FTP.  Este puerto se puede bloquear de forma segura si no se utiliza FTP.  En un ASE con un ILB, este puerto puede enlazarse a la dirección del ILB de un ASE.
* 990:  canal de control para FTPS.  Este puerto se puede bloquear de forma segura si no se utiliza FTPS.  En un ASE con un ILB, este puerto puede enlazarse a la dirección del ILB de un ASE.
* 10001-10020: canales de datos para FTP.  Al igual que con el canal de control, estos puertos pueden bloquear de forma segura si no se utiliza FTP.  En un ASE con un ILB, este puerto puede enlazarse a la dirección del ILB de un ASE.
* 4016: usado para la depuración remota con Visual Studio 2012.  Este puerto se puede bloquear de forma segura si no se utiliza la característica.  En un ASE con un ILB, este puerto está enlazado a la dirección del ILB del ASE.
* 4018: usado para la depuración remota con Visual Studio 2013.  Este puerto se puede bloquear de forma segura si no se utiliza la característica.  En un ASE con un ILB, este puerto está enlazado a la dirección del ILB del ASE.
* 4020: usado para la depuración remota con Visual Studio 2015.  Este puerto se puede bloquear de forma segura si no se utiliza la característica.  En un ASE con un ILB, este puerto está enlazado a la dirección del ILB del ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS y conectividad saliente
Para que un entorno de App Service funcione correctamente, necesita acceso de salida a varios puntos de conexión. Una lista completa de los puntos de conexión externos utilizados por un ASE en la sección "Conectividad de red necesaria" del artículo [Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Los Entornos de App Service requieren una infraestructura DNS válida configurada para la red virtual.  Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno de App Service, los desarrolladores pueden forzar a un entorno de App Service para recoger la nueva configuración de DNS.  Si se desencadena un reinicio gradual del entorno mediante el icono "Reiniciar", ubicado en la parte superior de la hoja de administración de App Service Environment en [Azure Portal][NewPortal], el entorno recogerá la nueva configuración de DNS.

También se recomienda configurar de antemano los servidores DNS personalizados de la red virtual antes de crear un entorno de App Service.  Si se cambia la configuración de DNS de una red virtual al crear un entorno de App Service, se generará un error en el proceso de creación de dicho entorno.  De manera similar, si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace de VPN y el servidor DNS es inaccesible o no está disponible, el proceso de creación del entorno de App Service también producirá un error.

## <a name="creating-a-network-security-group"></a>Creación de un grupo de seguridad de red
Para más información sobre cómo funcionan los grupos de seguridad de red, consulte la siguiente [información][NetworkSecurityGroups].  El ejemplo siguiente de Azure Service Management hace referencia a los puntos destacados de los grupos de seguridad de red, centrándose en la configuración y aplicación de un grupo de seguridad de red a una subred que contiene un entorno de App Service.

**Nota:** Los grupos de seguridad de red se pueden configurar gráficamente con [Azure Portal](https://portal.azure.com) o mediante Azure PowerShell.

Los grupos de seguridad de red se crean por primera vez como una entidad independiente asociada a una suscripción. Puesto que los grupos de seguridad de red se crean en una región de Azure, asegúrese de que el grupo de seguridad de red se crea en la misma región que el entorno de App Service.

Lo siguiente muestra la creación de un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Una vez creado un grupo de seguridad de red, una o varias reglas de seguridad de red se agregan a él.  Dado que el conjunto de reglas puede cambiar con el tiempo, se recomienda espaciar el esquema de numeración usado para las prioridades de reglas para facilitar la inserción de reglas adicionales con el tiempo.

En el ejemplo siguiente se muestra una regla que concede acceso de forma explícita a los puertos de administración necesarios para que la infraestructura de Azure administre y mantenga un entorno de App Service.  Tenga en cuenta que todo el tráfico de administración fluye a través de SSL y se protege mediante certificados de cliente, por lo que aunque estén abiertos los puertos, son inaccesibles para cualquier entidad que no sea la infraestructura de administración de Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Al bloquear el acceso a los puertos 80 y 443 para "ocultar" un entorno de App Service tras dispositivos o servicios ascendentes, tendrá que conocer la dirección IP ascendente.  Por ejemplo, si usa un firewall de aplicaciones web (WAF), este tendrá su propia dirección (o direcciones) IP, que usa al transmitir el tráfico a un entorno de App Service de nivel auxiliar.  Deberá usar esta dirección IP en el parámetro *SourceAddressPrefix* de una regla de seguridad de red.

En el ejemplo siguiente, se permite explícitamente el tráfico entrante de una dirección IP ascendente específica.  La dirección *1.2.3.4* se usa como marcador de posición para la dirección IP de un WAF ascendente.  Cambie el valor para que coincida con la dirección usada por su dispositivo o servicio ascendente.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Si se desea compatibilidad con FTP, las reglas siguientes pueden utilizarse como plantilla para conceder acceso al puerto de control FTP y los puertos del canal de datos.  Puesto que FTP es un protocolo con estado, es posible que no pueda enrutar el tráfico FTP a través de un dispositivo de firewall o proxy HTTP/HTTPS.  En este caso deberá establecer *SourceAddressPrefix* en un valor diferente: por ejemplo, el intervalo de direcciones IP del desarrollador o los equipos de implementación en los que se ejecutan los clientes FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** puede cambiar el intervalo de puertos del canal de datos durante el período de vista previa).

Si se usa la depuración remota con Visual Studio, las reglas siguientes muestran cómo conceder acceso.  Puesto que cada versión usa un puerto diferente para la depuración remota, hay una regla distinta para cada versión compatible de Visual Studio.  Al igual que ocurre con el acceso FTP, es posible que el tráfico de depuración remoto no fluya correctamente a través de un dispositivo de proxy o WAF tradicional.  En su lugar, *SourceAddressPrefix* se puede establecer en el intervalo de direcciones IP de los equipos del desarrollador que ejecutan Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Asignación de un grupo de seguridad de red a una subred
Un grupo de seguridad de red tiene una regla de seguridad predeterminada que deniega el acceso a todo el tráfico externo.  El resultado de la combinación de las reglas de seguridad de red descritas anteriormente y la regla de seguridad predeterminada que bloquea el tráfico entrante es que solo el tráfico de intervalos de direcciones de origen asociado a una acción *Permitir* podrá enviar tráfico a aplicaciones que se ejecutan en un entorno de App Service.

Después de rellenarse un grupo de seguridad de red con las reglas de seguridad, debe asignarse a la subred que contiene el entorno de App Service.  El comando de asignación hace referencia tanto al nombre de la red virtual donde reside el entorno de App Service como al nombre de la subred donde se creó este.  

En el ejemplo siguiente se muestra la asignación de un grupo de seguridad de red a una subred y una red virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Cuando se realice correctamente la asignación del grupo de seguridad de red (la asignación es una operación de ejecución prolongada y puede tardar unos minutos en completarse), solo el tráfico entrante que coincida con las reglas de *permisión* llegará correctamente a las aplicaciones del entorno de App Service.

Por razones de integridad, en el ejemplo siguiente se muestra cómo quitar y, por tanto, desasociar el grupo de seguridad de red de la subred:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Consideraciones especiales para IP-SSL explícito
Si una aplicación está configurada con una dirección IP-SSL explícita (aplicable *solo* a los ASE que tienen una dirección VIP pública) en lugar de la dirección IP predeterminada del entorno de App Service, tanto el tráfico HTTP como el tráfico HTTPS fluyen a la subred a través de un conjunto de puertos distintos de los puertos 80 y 443.

El par de puertos que utiliza cada dirección IP-SSL individual puede encontrarse en la interfaz de usuario del portal, en la hoja de experiencia de usuario de los detalles del entorno de App Service.  Seleccione Toda la configuración > Direcciones IP.  La hoja Direcciones IP muestra una tabla de todas las direcciones IP-SSL configuradas expresamente para el entorno de App Service, junto con el par de puertos especiales que se utilizan para enrutar el tráfico HTTP y HTTPS asociado con cada dirección IP-SSL.  Es este par de puertos el que debe utilizarse para los parámetros de DestinationPortRange al configurar las reglas de un grupo de seguridad de red.

Cuando una aplicación de un ASE está configurada para usar IP-SSL, los clientes externos no verán la asignación del par de puertos especial.  El tráfico a las aplicaciones fluirá con normalidad a la dirección IP-SSL configurada.  La traslación del par de puertos especial se realiza de manera automática e interna durante el tramo final del enrutamiento del tráfico en la subred que contiene el ASE. 

## <a name="getting-started"></a>Introducción
Para empezar a trabajar con los entornos de App Service, consulte [Introducción al entorno de App Service][IntroToAppServiceEnvironment]

Para más información sobre las aplicaciones que se conectan de forma segura al recurso de back-end desde App Service Environment, consulte [Conexión segura a los recursos de back-end desde una instancia de App Service Environment][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

