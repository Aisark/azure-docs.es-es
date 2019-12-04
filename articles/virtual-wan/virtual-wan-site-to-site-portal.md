---
title: 'Azure Virtual WAN: creación de conexiones de sitio a sitio'
description: En este tutorial, aprenda a usar Azure Virtual WAN para crear una conexión VPN de sitio a sitio a Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e17205af1ede845ea77b04f6f2b4c6babf3bc450
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482143"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Creación de una conexión de sitio a sitio mediante Azure Virtual WAN

Este tutorial muestra cómo usar Virtual WAN para conectarse a los recursos de Azure a través de una conexión VPN de IPsec/IKE (IKEv1 e IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada. Para más información sobre Virtual WAN, consulte la [Introducción a Virtual WAN](virtual-wan-about.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una instancia de Virtual WAN
> * Crear un concentrador
> * Crear un sitio
> * Conectar un sitio a un centro de conectividad
> * Conectar un sitio VPN a un centro de conectividad
> * Conectar una red virtual a un concentrador
> * Descarga de un archivo de configuración
> * Visualizar la instancia de Virtual WAN

> [!NOTE]
> Si tiene muchos sitios, normalmente usará un [asociado de Virtual WAN](https://aka.ms/virtualwan) para crear esta configuración. De todas formas, puede crear esta configuración usted mismo si está familiarizado con las redes y tiene experiencia en la configuración de su propio dispositivo VPN.
>

![Diagrama de Virtual WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

* Tiene una red virtual a la que quiere conectarse. Compruebe que ninguna de las subredes de sus redes locales se superpone a las redes virtuales a las que quiere conectarse. Para crear una red virtual en Azure Portal consulte este [Inicio rápido](../virtual-network/quick-create-portal.md).

* Su red virtual no tiene ninguna puerta de enlace de red virtual. Si la red virtual tiene alguna puerta de enlace (ya sea VPN o ExpressRoute), tiene que quitarla. Esta configuración requiere que las redes virtuales estén conectadas a la puerta de enlace del centro de conectividad de Virtual WAN.

* Obtenga un intervalo de direcciones IP para la región del concentrador. El centro de conectividad es una red virtual que Virtual WAN crea y usa. El intervalo de direcciones que especifique para el centro de conectividad no se puede superponer a ninguna de las redes virtuales existentes a las que ya esté conectado. Igualmente no se puede superponer a los intervalos de direcciones con las que esté conectadas en el entorno local. Si no está familiarizado con los intervalos de direcciones IP ubicados en la configuración de la red local, póngase de acuerdo con alguien que pueda proporcionarle estos detalles.

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Creación de una instancia de Virtual WAN

Desde un explorador, vaya a Azure Portal e inicie sesión con su cuenta de Azure.

1. Vaya a la página de Virtual WAN. En el portal, haga clic en **+Crear un recurso**. Escriba **Virtual WAN** en el cuadro de búsqueda y seleccione ENTRAR.
2. Seleccione **Virtual WAN** en los resultados. En la página Virtual WAN, haga clic en **Crear** para abrir la página Crear una red WAN.
3. Dentro de la página **Crear una red WAN**, en la pestaña **Aspectos básicos**, rellene los campos siguientes:

   ![Red WAN virtual](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Suscripción**: seleccione la suscripción que quiere usar.
   * **Grupo de recursos**: cree uno nuevo o utilice uno ya existente.
   * **Ubicación del grupo de recursos**: elija una ubicación para los recursos en la lista desplegable. Una red WAN es un recurso global y no reside en una región determinada. De todas formas, tiene que seleccionar una región con el fin de administrar y ubicar más fácilmente el recurso WAN que cree.
   * **Nombre**: escriba el nombre que desea dar a la WAN.
   * **Tipo:** Básico o Estándar. Si crea una WAN básica, solo puede crear un centro de conectividad básico. Los centros de conectividad básicos solo pueden tener conectividad VPN de sitio a sitio.
4. Cuando termine de rellenar los campos, haga clic en **Revisión y creación**.
5. Una vez que se haya superado la validación, seleccione **Crear** para crear la WAN virtual.

## <a name="hub"></a>Creación de un centro de conectividad

Un centro de conectividad es una red virtual que puede contener puertas de enlace para las funcionalidades de sitio a sitio, ExpressRoute o de punto a sitio. Cuando se crea el concentrador, se le cobrará por él, aunque no esté asociado a ningún sitio. La creación de la puerta de enlace de VPN de sitio a sitio en el centro de conectividad virtual tarda 30 minutos.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>Creación de un sitio

Ahora está listo para crear los sitios correspondientes a sus ubicaciones físicas. Cree tantos sitios como necesite que se correspondan con sus ubicaciones físicas. Por ejemplo, si tiene una sucursal en Nueva York, otra en Londres y otra en Los Ángeles, crearía tres sitios independientes. Estos sitios contienen los puntos de conexión de dispositivo VPN local. Puede crear hasta 1000 sitios por cada centro de conectividad virtual en una instancia de Virtual WAN. Si tiene varios centros de conectividad, puede crear 1000 sitios en cada uno de ellos. Si tiene un dispositivo CPE de asociado de Virtual WAN (link insert), consulte con su asociado para obtener información sobre su automatización de Azure. Normalmente, la automatización implica simplemente hacer clic para exportar información de una rama a gran escala en Azure y configurar la conectividad desde el CPE a la puerta de enlace de VPN de Azure Virtual WAN (Here is a link to automation guidance from Azure to CPE partners).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>Conexión del sitio de VPN con el centro de conectividad

En este paso, conectará el sitio VPN al centro de conectividad.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>Conexión de la red virtual al centro de conectividad

En este paso, creará la conexión entre una red virtual y el centro de conectividad. Repita estos pasos para cada red virtual que desee conectar.

1. En la página de la red WAN virtual, haga clic en **Conexiones de red virtual**.
2. En la página de conexión de red virtual, haga clic en **+ Agregar conexión**.
3. En la página **Agregar conexión**, rellene los campos siguientes:

    * **Nombre de la conexión**: asigne un nombre a la conexión.
    * **Centros**: seleccione el concentrador que desea asociar a esta conexión.
    * **Suscripción**: compruebe la suscripción.
    * **Red virtual**: seleccione la red virtual que quiere conectar con este concentrador. La red virtual no puede tener una puerta de enlace de red virtual ya existente.
4. Haga clic en **Aceptar** para crear la conexión de red virtual.

## <a name="device"></a>Descarga de la configuración de VPN

Use la configuración del dispositivo VPN para configurar el dispositivo VPN local.

1. En la página de la red virtual WAN, haga clic en **Introducción**.
2. En la parte superior de la página **Centro de conectividad ->VPNSite**, haga clic en **Descargar la configuración de VPN**. Azure crea una cuenta de almacenamiento en el grupo de recursos "microsoft - network-[ubicación]", donde ubicación es la ubicación de la red WAN. Una vez que haya aplicado la configuración a los dispositivos VPN, puede eliminar esta cuenta de almacenamiento.
3. Una vez el archivo se haya terminado de crear, puede hacer clic en el vínculo para descargarlo.
4. Aplique la configuración al dispositivo VPN local.

### <a name="understanding-the-vpn-device-configuration-file"></a>Información sobre el archivo de configuración del dispositivo VPN

El archivo de configuración de dispositivo contiene la configuración que se debe usar al configurar el dispositivo VPN local. Cuando visualice este archivo, tenga en cuenta la siguiente información:

* **vpnSiteConfiguration**: en esta sección se indica la configuración de los detalles del dispositivo como un sitio de conexión a la red virtual WAN. Incluye el nombre y la dirección IP pública del dispositivo de rama.
* **vpnSiteConnections**: en esta sección se proporciona información sobre la siguiente configuración:

    * **Espacio de direcciones** de la red virtual de concentradores virtuales<br>Ejemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espacio de direcciones** de las redes virtuales que están conectadas al concentrador<br>Ejemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Direcciones IP** de vpngateway del concentrador virtual. Dado que cada conexión del elemento vpngateway consta de dos túneles con una configuración activo-activo, verá ambas direcciones IP en este archivo. En este ejemplo puede ver "Instance0" e "Instance1" para cada sitio.<br>Ejemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalles de configuración de conexión de Vpngateway** como BGP, clave precompartida, etc. La PSK es la clave precompartida que se genera automáticamente para usted. Puede modificar la conexión cuando quiera en la página de información general para una PSK personalizada.
  
### <a name="example-device-configuration-file"></a>Archivo de configuración de dispositivo de ejemplo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configuración del dispositivo VPN

>[!NOTE]
> Si trabaja con una solución de asociado de WAN virtual, se produce automáticamente la configuración de los dispositivos de la VPN. El controlador de dispositivos obtiene el archivo de configuración de Azure y lo aplica al dispositivo para configurar la conexión con Azure. Esto significa que no es necesario saber cómo configurar manualmente el dispositivo VPN.
>

Si necesita instrucciones para configurar el dispositivo, puede utilizar las que se proporcionan en la [página de scripts de configuración de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con las siguientes advertencias:

* Las instrucciones que aparecen en la página de dispositivos VPN no están escritas para Virtual WAN, pero puede usar los valores de Virtual WAN desde el archivo de configuración para configurar manualmente el dispositivo VPN. 
* Los scripts descargables de configuración de dispositivo que son para VPN Gateway no funcionan para Virtual WAN, ya que la configuración es diferente.
* Las nuevas instancias de Virtual WAN admiten IKEv1 e IKEv2.
* Virtual WAN puede usar dispositivos VPN e instrucciones de dispositivo basados en rutas y basados en directivas.

## <a name="viewwan"></a>Visualización de la instancia de Virtual WAN

1. Vaya a la instancia de Virtual WAN.
2. En la página **Información general**, cada punto del mapa representa un centro de conectividad. Mantenga el puntero sobre cualquier punto para ver el resumen de estado, el estado de la conexión y los bytes de entrada y salida del centro de conectividad.
3. En la sección de centros de conectividad y conexiones, puede ver el estado del centro de conectividad, los sitios VPN, etc. También puede hacer clic en el nombre de un centro de conectividad e ir al sitio VPN para ver detalles adicionales.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Virtual WAN, consulte la página [Introducción a Virtual WAN](virtual-wan-about.md).
