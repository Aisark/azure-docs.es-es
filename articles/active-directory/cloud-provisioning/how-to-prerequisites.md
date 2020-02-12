---
title: Requisitos previos del aprovisionamiento en la nube de Azure AD Connect en Azure AD
description: En este artículo se describen los requisitos previos y los requisitos de hardware que necesita para el aprovisionamiento en la nube.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e5c58d29f01cadcc3ea2e8ec48ae67e58c4180
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909047"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Requisitos previos del aprovisionamiento en la nube de Azure AD Connect
En este artículo se proporcionan instrucciones sobre cómo elegir y usar el aprovisionamiento en la nube de Azure Active Directory (Azure AD) Connect como solución de identidad.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos del agente de aprovisionamiento en la nube
Se necesita lo siguiente para usar el aprovisionamiento en la nube de Azure AD Connect:
    
- Una cuenta de administrador global para el inquilino de Azure AD.
- Un servidor local para el agente de aprovisionamiento con Windows 2012 R2 o posterior.
- Configuraciones de firewall locales.

>[!NOTE]
>Actualmente, el agente de aprovisionamiento solo se puede instalar en servidores con el idioma inglés. La instalación de un paquete de idioma en inglés en un servidor con un idioma distinto del inglés no es una opción válida y producirá un error en la instalación del agente. 

En el resto del documento se proporcionan instrucciones paso a paso para estos requisitos previos.

### <a name="in-the-azure-active-directory-admin-center"></a>En el Centro de administración de Azure Active Directory

1. Cree una cuenta de administrador global solo en la nube en el inquilino de Azure AD. De esta manera, puede administrar la configuración del inquilino en caso de que los servicios locales fallen o no estén disponibles. Información acerca de cómo [agregar una cuenta de administrador global que está solo en la nube](../active-directory-users-create-azure-portal.md). La finalización de este paso es esencial para garantizar que no queda bloqueado fuera de su inquilino.
1. Agregue uno o varios [nombres de dominio personalizados](../active-directory-domains-add-azure-portal.md) al inquilino de Azure AD. Los usuarios pueden iniciar sesión con uno de estos nombres de dominio.

### <a name="in-your-on-premises-environment"></a>En el entorno local

1. Identifique un servidor host unido a un dominio en el que se ejecuta Windows Server 2012 R2 o superior con un mínimo de 4 GB de RAM y un entorno de ejecución .NET 4.7.1 o posterior.

1. Si hay un firewall entre los servidores y Azure AD, configure los elementos siguientes:
   - Asegúrese de que los agentes pueden realizar solicitudes *de salida* a Azure AD a través de los puertos siguientes:

        | Número de puerto | Cómo se usa |
        | --- | --- |
        | **80** | Descarga las listas de revocación de certificados (CRL) al validar el certificado SSL.  |
        | **443** | Controla toda la comunicación saliente con el servicio. |
        | **8080** (opcional) | Si el puerto 443 no está disponible, los agentes notifican su estado cada 10 minutos en el puerto 8080. Este estado se muestra en el portal de Azure AD. |
     
   - Si el firewall fuerza las reglas según los usuarios que las originan, abra estos puertos para el tráfico de servicios de Windows que se ejecutan como un servicio de red.
   - Si el firewall o proxy le permite configurar sufijos seguros, agregue conexiones a \*.msappproxy.net y \*.servicebus.windows.net. En caso contrario, permita el acceso a los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653), que se actualizan cada semana.
   - Los agentes necesitan acceder a login.windows.net y login.microsoftonline.com para el registro inicial. Abra el firewall también para esas direcciones URL.
   - Para la validación de certificados, desbloquee las siguientes direcciones URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 y www\.microsoft.com:80. Estas direcciones URL se utilizan para la validación de certificados con otros productos de Microsoft, por lo que es posible que estas direcciones URL estén bloqueadas.

### <a name="verify-the-port"></a>Comprobación del puerto
Para comprobar que Azure está escuchando en el puerto 443 y que el agente puede comunicarse con él, utilice la siguiente dirección URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

En esta prueba se comprobará que los agentes pueden comunicarse con Azure a través del puerto 443. Abra un explorador y vaya a la dirección URL anterior desde el servidor en el que está instalado el agente.

![Comprobación de disponibilidad de puertos](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisitos adicionales
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

>[!NOTE]
>La seguridad de la capa de transporte (TLS) es un protocolo que proporciona comunicaciones seguras. Si se cambia la configuración de TLS, todo el bosque se verá afectado. Para más información, consulte [Actualización para habilitar TLS 1.1 y TLS 1.2 como protocolos seguros predeterminados de WinHTTP en Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

El servidor de Windows que hospeda el agente de aprovisionamiento en la nube de Azure AD Connect debe tener TLS 1.2 habilitado antes de instalarlo.

Para habilitar TLS 1.2, siga estos pasos.

1. Establezca las siguientes claves del Registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie el servidor.


## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es el aprovisionamiento en la nube de Azure AD Connect?](what-is-cloud-provisioning.md)

