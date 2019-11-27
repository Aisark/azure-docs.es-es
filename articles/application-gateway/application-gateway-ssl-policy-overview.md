---
title: Introducción a la directiva SSL para Azure Application Gateway
description: Obtenga información sobre cómo configurar la directiva SSL para Azure Application Gateway y reducir la sobrecarga de cifrado y descifrado de una granja de servidores back-end.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 6b3d52f1f6bd0390ab6ccafa80b2979cb0e498fd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130411"
---
# <a name="application-gateway-ssl-policy-overview"></a>Introducción a la directiva SSL de Application Gateway

Puede utilizar Azure Application Gateway para centralizar la administración de certificados SSL y reducir la sobrecarga de cifrado y descifrado de una granja de servidores back-end. Este control centralizado de SSL también le permite especificar una directiva SSL central que se adapte a los requisitos de seguridad de la organización. Esto le ayuda a cumplir los requisitos de cumplimiento normativo, así como las directrices de seguridad y los procedimientos recomendados.

La directiva SSL incluye el control de la versión del protocolo SSL, así como de los conjuntos de cifrado y el orden en el que se usan los cifrados durante un protocolo de enlace SSL. Application Gateway proporciona dos mecanismos para controlar la directiva SSL. Puede usar una directiva predefinida o una directiva personalizada.

## <a name="predefined-ssl-policy"></a>Directiva SSL predefinida

Application Gateway tiene tres directivas de seguridad predefinidas. Puede configurar la puerta de enlace con cualquiera de estas directivas para conseguir el nivel adecuado de seguridad. Los nombres de directiva se anotan según el año y mes en el que se configuraron. Cada directiva ofrece diferentes versiones de protocolos SSL y conjuntos de cifrado. Se recomienda usar las directivas SSL más recientes para garantizar la máxima seguridad SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propiedad  |Valor  |
|---|---|
|NOMBRE     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Valor predeterminado| True (si no se ha especificado ninguna directiva predefinida) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propiedad  |Valor  |
|   ---      |  ---       |
|NOMBRE     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Valor predeterminado| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propiedad  |Valor  |
|---|---|
|NOMBRE     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Valor predeterminado| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Directiva SSL personalizada

Si una directiva SSL predefinida se debe configurar de acuerdo con sus requisitos, debe definir su propia directiva SSL personalizada. Con una directiva SSL personalizada, tiene un control completo sobre la versión mínima compatible del protocolo SSL, así como de los conjuntos de cifrado compatibles y su orden de prioridad.
 
### <a name="ssl-protocol-versions"></a>Versiones del protocolo SSL

* SSL 2.0 y 3.0 están deshabilitados de forma predeterminada en todas las instancias de Application Gateway. Estas versiones de protocolo no son configurables.
* Una directiva SSL personalizada le ofrece la opción de seleccionar cualquiera de los siguientes tres protocolos como la versión mínima de protocolo SSL para la puerta de enlace: TLSv1_0, TLSv1_1 y TLSv1_2.
* Si no se define ninguna directiva SSL, los tres protocolos (TLSv1_0, TLSv1_1 y TLSv1_2) están habilitados.

### <a name="cipher-suites"></a>Conjuntos de cifrado

Application Gateway admite los siguientes conjuntos de cifrado desde los que puede elegir la directiva personalizada. El orden de los conjuntos de cifrado determina el orden de prioridad durante la negociación SSL.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Los conjuntos de cifrado SSL que se usan para la conexión también se basan en el tipo de certificado que se utiliza. En las conexiones de cliente a Application Gateway, los conjuntos de cifrado usados se basan en el tipo de certificados de servidor en el agente de escucha de Application Gateway. En las conexiones de Application Gateway a grupo de back-end, los conjuntos de cifrado usados se basan en el tipo de certificados de servidor en los servidores de grupo de back-end.

## <a name="next-steps"></a>Pasos siguientes

Si desea aprender a configurar la descarga de SSL, consulte [Configuración de una directiva SSL en Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
