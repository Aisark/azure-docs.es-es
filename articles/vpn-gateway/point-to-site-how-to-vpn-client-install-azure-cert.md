---
title: 'Azure VPN Gateway: instalación de un certificado de cliente de punto a sitio'
description: 'Instale un certificado de cliente para la autenticación de certificados de conexiones de punto a sitio: Windows, Mac y Linux.'
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902843"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalación de un certificado de cliente para conexiones de punto a sitio con autenticación de certificados

Todos los clientes que se conectan a una red virtual mediante la autenticación de certificados de Azure de conexiones de punto a sitio requieren un certificado de cliente. Este artículo le ayuda a instalar un certificado de cliente que se usa para la autenticación cuando se conecta a una red virtual mediante una conexión de punto a sitio.

## <a name="generate"></a>Adquisición de un certificado de cliente

Independientemente del sistema operativo cliente al que quiera conectarse, debe tener siempre un certificado de cliente. Puede generar un certificado de cliente desde un certificado raíz que se ha generado mediante una solución de una entidad de certificación empresarial o desde un certificado raíz autofirmado. Vea las instrucciones de [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) o [Linux](vpn-gateway-certificates-point-to-site-linux.md) para saber los pasos para generar un certificado de cliente. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Los clientes VPN de Mac son compatibles únicamente con el modelo de implementación de Resource Manager. No son compatibles con el modelo de implementación clásica.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

El certificado de cliente de Linux se instala en el cliente como parte de la configuración del cliente. Vea [Client configuration - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) (Configuración de cliente: Linux) para obtener instrucciones.

## <a name="next-steps"></a>Pasos siguientes

Continúe con los pasos de configuración de punto a sitio en [Creación e instalación de los archivos de configuración de cliente de VPN](point-to-site-vpn-client-configuration-azure-cert.md).
