---
title: 'Azure VMware Solutions (AVS): puertas de enlace de VPN'
description: Obtenga información acerca de los conceptos de VPN de sitio a sitio y de VPN de punto a sitio de AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024864"
---
# <a name="vpn-gateways-overview"></a>Información general sobre puertas de enlace de VPN

Una puerta de enlace de VPN se usa para enviar tráfico cifrado entre la red de una región de AVS en una ubicación local o en un equipo a través de Internet pública. Cada región puede tener una puerta de enlace de VPN, que puede admitir varias conexiones. Al crear varias conexiones a la misma instancia de VPN Gateway, todos los túneles VPN comparten el ancho de banda disponible de la puerta de enlace.

AVS proporciona dos tipos de puerta de enlace de VPN:

* Puerta de enlace de VPN de sitio a sitio
* Puerta de enlace de VPN de punto a sitio

## <a name="site-to-site-vpn-gateway"></a>Puerta de enlace de VPN de sitio a sitio

Una puerta de enlace de VPN de sitio a sitio se usa para enviar tráfico cifrado entre la red de una región de AVS y un centro de datos local. Use esta conexión para definir las subredes o el intervalo de CIDR para el tráfico de red entre su red local y la red de la región de AVS.

La puerta de enlace VPN permite consumir los servicios desde el entorno local en la nube privada de AVS, y los servicios en la nube privada de AVS, desde la red local. AVS proporciona un servidor VPN basado en directivas para establecer la conexión desde su red local.

Casos de uso de VPN de sitio a sitio:

* Accesibilidad de su vCenter de nube privada de AVS desde cualquier estación de trabajo en la red local.
* Uso de Active Directory local como origen de identidad de vCenter.
* Transferencia cómoda de plantillas de máquina virtual, imágenes ISO y otros archivos de los recursos locales a vCenter de la nube privada de AVS.
* Accesibilidad de las cargas de trabajo que se ejecutan en la nube privada de AVS desde la red local.

![Topología de conexiones VPN de sitio a sitio](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parámetros criptográficos

Una conexión VPN de sitio a sitio usa los siguientes parámetros criptográficos predeterminados para establecer una conexión segura. Cuando crea una conexión desde un dispositivo VPN local, use cualquiera de los siguientes parámetros admitidos por la puerta de enlace de VPN local.

#### <a name="phase-1-proposals"></a>Propuestas de la fase 1

| Parámetro | Propuesta 1 | Propuesta 2 | Propuesta 3 |
|-----------|------------|------------|------------|
| Versión de IKE | IKEv1 | IKEv1 | IKEv1 |
| Cifrado | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (grupo DH) | 2 | 2 | 2 |
| Tiempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamaño de datos | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propuestas de la fase 2

| Parámetro | Propuesta 1 | Propuesta 2 | Propuesta 3 |
|-----------|------------|------------|------------|
| Cifrado | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de confidencialidad directa total (grupo PFS) | None | None | None |
| Tiempo de vida | 1800 segundos | 1800 segundos | 1800 segundos |
| Tamaño de datos | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Establezca la fijación de MSS de TCP en 1200 en el dispositivo VPN. O, si los dispositivos de VPN no admiten la fijación de MSS, también puede establecer el MTU en la interfaz de túnel en 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Puerta de enlace de VPN de punto a sitio

Una VPN de punto a sitio se usa para enviar tráfico cifrado entre la red de una región de AVS y un equipo cliente. La VPN de punto a sitio es la manera más fácil de acceder a la red de la nube privada de AVS, incluido vCenter y las VM de carga de trabajo de su nube privada de AVS. Use la conectividad de VPN de punto a sitio si se conecta a la nube privada de AVS de forma remota.

## <a name="next-steps"></a>Pasos siguientes

* [Configurar puerta de enlace de VPN](vpn-gateway.md)
