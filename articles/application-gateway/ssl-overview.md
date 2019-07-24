---
title: Habilitación de SSL de extremo a extremo en Azure Application Gateway
description: En este artículo se proporciona información general sobre la compatibilidad de Application Gateway con el protocolo SSL de extremo a extremo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ee901fdcae9717cc6d03d7653bcaacc0c32518e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254316"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Introducción a la terminación SSL y a SSL de extremo a extremo con Application Gateway

Capa de sockets seguros (SSL) es la tecnología de seguridad estándar para establecer un vínculo cifrado entre un servidor web y un explorador. Este vínculo garantiza que todos los datos que se pasan entre el servidor web y los exploradores siguen siendo privados y continúan estando cifrados. Application Gateway admite tanto la terminación SSL en la puerta de enlace como el cifrado SSL de un extremo a otro.

## <a name="ssl-termination"></a>Terminación de SSL

Application Gateway es compatible con la terminación SSL en la puerta de enlace; después, el tráfico fluye normalmente sin cifrar a los servidores back-end. Llevar a cabo la terminación SSL en la puerta de enlace de aplicaciones tiene una serie de ventajas:

- **Rendimiento mejorado**: el mayor rendimiento al realizar el descifrado SSL es el protocolo de enlace inicial. Para mejorar el rendimiento, el servidor que realiza el descifrado almacena en caché los identificadores de sesión SSL y administra los vales de sesión TLS. Si esto se hace en la puerta de enlace de aplicaciones, todas las solicitudes del mismo cliente pueden usar los valores almacenados en caché. Si se hace en los servidores back-end, entonces cada vez que las solicitudes del cliente vayan a un servidor diferente, el cliente tendrá que volverse a autenticarse. El uso de tickets TLS puede ayudar a mitigar este problema, pero no todos los clientes los admiten y pueden resultar difícil de configurar y administrar.
- **Mejor utilización de los servidores back-end**: el procesamiento SSL/TLS hace un uso intensivo de CPU, y este uso cada vez es mayor a medida que aumentan los tamaños de clave. La eliminación de este trabajo de los servidores back-end les permite centrarse en aquello en lo que son más eficientes: entregar contenido.
- **Enrutamiento inteligente**: mediante el descifrado del tráfico, la puerta de enlace de aplicaciones tiene acceso al contenido de la solicitud, como encabezados, URI, etc., y puede utilizar estos datos para enrutar las solicitudes.
- **Administración de certificados**: los certificados solo se deben comprar e instalar en la puerta de enlace de aplicaciones y no en todos los servidores back-end. Esto ahorra tiempo y dinero.

Para configurar la terminación SSL, debe agregar un certificado SSL al cliente de escucha para permitir a la puerta de enlace de aplicaciones derivar una clave simétrica según la especificación del protocolo SSL. A continuación, la clave simétrica se usa para cifrar y descifrar el tráfico que se envía a la puerta de enlace. El certificado SSL debe tener el formato Personal Information Exchange (PFX). Este formato de archivo permite la exportación de la clave privada, lo que es necesario para que la puerta de enlace de aplicaciones pueda realizar el cifrado y descifrado del tráfico.

> [!NOTE] 
>
> La puerta de enlace de aplicaciones no proporciona ninguna funcionalidad para crear un nuevo certificado o enviar una solicitud de certificado a una entidad de certificación.

Para que la conexión SSL para funcione, deberá asegurarse de que el certificado SSL cumple las condiciones siguientes:

- La fecha y hora actuales están dentro del intervalo de fechas "Válido desde" y "Válido hasta" en el certificado.
- El "nombre común" del certificado coincide con el encabezado de host en la solicitud. Por ejemplo, si el cliente realiza una solicitud a `https://www.contoso.com/`, el certificado común debe ser `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificados compatibles con la terminación SSL

Application Gateway admite los siguientes tipos de certificados:

- Certificado CA (entidad de certificación): un certificado CA es un certificado digital emitido por una entidad de certificación (CA)
- Certificado EV (validación extendida): un certificado EV contiene pautas de certificado estándar del sector. Esto cambiará a verde la barra del localizador del explorador y publicará también el nombre de la compañía.
- Certificado comodín: este certificado admite cualquier número de subdominios en función de *. site.com, donde el subdominio reemplazaría el asterisco (*). Sin embargo, no admite site.com, por lo que en caso de que los usuarios accedan a su sitio web sin escribir las "www" iniciales, el certificado comodín no cubrirá eso.
- Certificados autofirmados: los exploradores de cliente no confían en estos certificados y avisarán al usuario de que el certificado del servicio virtual no forma parte de una cadena de confianza. Los certificados autofirmados son buenos para las pruebas o los entornos donde los administradores controlan a los clientes y pueden omitir de forma segura las alertas de seguridad del explorador. Las cargas de trabajo de producción nunca deben usar certificados autofirmados.

Para más información, consulte la [configuración de la terminación SSL con una puerta de enlace de aplicaciones](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamaño del certificado
Consulte la sección [Límites de Application Gateway](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) para conocer el tamaño máximo de certificado SSL admitido.

## <a name="end-to-end-ssl-encryption"></a>Cifrado SSL de un extremo a extremo

Puede que algunos clientes no deseen una comunicación sin cifrar en los servidores back-end. Esto podría deberse a los requisitos de seguridad y cumplimiento normativo o bien a la posibilidad de que la aplicación solo acepte una conexión segura. Para tales aplicaciones, Application Gateway admite el cifrado SSL de un extremo a otro.

El protocolo SSL de un extremo a otro permite transmitir de forma segura información confidencial cifrada al back-end, al mismo tiempo que se siguen aprovechando las ventajas del equilibrio de carga de capa 7 que proporciona Application Gateway. Algunas de estas características son la afinidad de la sesión basada en las cookies, el enrutamiento basado en direcciones URL, la compatibilidad para el enrutamiento basado en sitios o la capacidad para insertar encabezados X-Forwarded-*.

Cuando se configura con el modo de comunicación de SSL de un extremo a otro, Application Gateway finaliza las sesiones SSL en la puerta de enlace y descifra el tráfico de usuario. Después, aplica las reglas configuradas para seleccionar una instancia de grupo de back-end adecuada en la que enrutar el tráfico. Posteriormente, Application Gateway inicia una nueva conexión SSL al servidor back-end y vuelve a cifrar los datos mediante el certificado de clave pública del servidor back-end antes de transmitir la solicitud al back-end. Cualquier respuesta del servidor web pasa por el mismo proceso en su regreso al usuario final. El protocolo SSL de un extremo a otro se habilita estableciendo la configuración de protocolo de [Configuración de HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) en HTTPS, que luego se aplica a un grupo de back-end.

La directiva SSL se aplica tanto al tráfico de front-end como de back-end. En el front-end, Application Gateway actúa como el servidor y exige la directiva. En el back-end, Application Gateway actúa como cliente y envía la información de protocolo o cifrado como la preferencia durante el protocolo de enlace SSL.

La puerta de enlace de aplicaciones solo se comunica con las instancias de back-end que han incluido su certificado en la lista blanca con la puerta de enlace de aplicaciones o cuyos certificados están firmados por autoridades de entidad de certificación perfectamente conocidas donde el nombre común del certificado coincide con el nombre del host en la configuración del back-end HTTP. Estas incluyen los servicios de Azure de confianza, como aplicaciones web de Azure App Service y Azure API Management.

Si los certificados de los miembros del grupo de back-end no están firmados por autoridades de entidad de certificación perfectamente conocidas, cada instancia del grupo de back-end con la funcionalidad SSL de un extremo a extremo habilitada debe configurarse con un certificado para permitir una comunicación segura. Al agregar el certificado, se garantiza que la puerta de enlace de aplicaciones solo se comunique con instancias back-end conocidas. Esto protege aún más la comunicación de un extremo a otro.

> [!NOTE] 
>
> La configuración del certificado de autenticación no es necesaria para servicios de Azure de confianza, como aplicaciones web de Azure App Service y Azure API Management.

> [!NOTE] 
>
> El certificado agregado a la **configuración HTTP de back-end** para autenticar los servidores back-end puede ser el mismo que el certificado agregado al **cliente de escucha** para la terminación SSL en la puerta de enlace de aplicaciones o diferente para mayor seguridad.

![Escenario de SSL de un extremo a otro][1]

En este ejemplo, las solicitudes mediante TLS1.2 se enrutan a los servidores back-end en Pool1 con SSL de un extremo a otro.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de un extremo a otro e incorporación a listas blancas de certificados

Application Gateway solo se comunica con instancias de back-end conocidas, que tienen en la lista blanca su certificado con Application Gateway. Para habilitar la creación de listas blancas de certificados, debe cargar la clave pública de los certificados de servidor back-end en Application Gateway (no el certificado raíz). Solo se permiten conexiones en back-ends conocidos y en la lista blanca. Los back-ends restantes producen un error de puerta de enlace. Los certificados autofirmados son para fines de prueba que y no se recomiendan para cargas de trabajo de producción. Antes de poder usar estos certificados, deben estar en la lista blanca con Application Gateway, tal y como se describe en los pasos anteriores.

> [!NOTE]
> La configuración del certificado de autenticación no es necesaria para servicios de Azure de confianza, como Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de extremo a extremo con la SKU v2

Los certificados de autenticación han dejado de usarse, y se han reemplazado por certificados raíz de confianza en la SKU v2 de Application Gateway. Funcionan de forma similar a los certificados de autenticación, aunque con algunas diferencias importantes:

- Los certificados firmados por autoridades de entidad de certificación conocidas cuyo CN coincida con el nombre de host de la configuración de back-end HTTP no requieren ningún paso adicional para que el SSL de extremo a extremo funcione. 

   Por ejemplo, si los certificados de back-end están emitidos por una entidad de certificación conocida y tienen un CN de contoso.com, y también se establece el campo de host de la configuración de HTTP de back-end en contoso.com, no se requiere ningún otro paso adicional. Puede establecer el protocolo de configuración HTTP en HTTPS y tanto el sondeo de estado como la ruta de acceso de los datos estarían habilitados para SSL. Si utiliza Azure App Service u otros servicios web de Azure como back-end, estos también serían de confianza de manera implícita y no se necesitarían pasos adicionales para SSL de un extremo a extremo.
- Si el certificado está autofirmado o lo han firmado intermediarios desconocidos, tendrá que definir un certificado raíz de confianza para habilitar SSL de extremo a extremo en la SKU v2. La instancia de Application Gateway se comunicará únicamente con back-ends cuyo certificado raíz del certificado del servidor coincida con alguno de la lista de certificados raíz de confianza en la configuración HTTP de back-end asociada con el grupo.
- Además de la coincidencia del certificado raíz, Application Gateway también valida si el valor de host especificado en la configuración de HTTP del back-end coincide con el del nombre común (CN) presentado por el certificado SSL del servidor de back-end. Cuando se intenta establecer una conexión SSL con el back-end, Application Gateway establece la extensión de indicación de nombre de servidor (SNI) en el host especificado en la configuración de HTTP de back-end.
- Si se elige la opción de **elegir el nombre de host a partir de la dirección de back-end** en lugar del campo de host en la configuración de HTTP de back-end, el encabezado SNI siempre se establece en el nombre de dominio completo del grupo del back-end y el CN del certificado SSL del servidor de back-end debe coincidir con su nombre de dominio completo. Los miembros del grupo de back-end con direcciones IP no se admiten en este escenario.
- El certificado raíz es un certificado raíz codificado en base64 a partir de los certificados de servidor de back-end.

## <a name="next-steps"></a>Pasos siguientes

Después aprender acerca de SSL de un extremo a otro, consulte [Configuración de SSL de un extremo a otro con Application Gateway mediante PowerShell](application-gateway-end-to-end-ssl-powershell.md) para crear una puerta de enlace de aplicaciones utilizando SSL de un extremo a otro.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
