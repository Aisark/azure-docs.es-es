---
title: 'Consideraciones de red con una instancia de App Service Environment: Azure'
description: Explica el tráfico de red de ASE y cómo establecer los NSG y las UDR con el ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 73175b326c25d5d9a78155d0d9d888b655da1bfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124140"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Consideraciones de red para una instancia de App Service Environment #

## <a name="overview"></a>Información general ##

 [Azure App Service Environment][Intro] es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet). Hay dos tipos de implementación de una instancia de App Service Environment (ASE):

- **ASE externo**: Expone las aplicaciones hospedadas en ASE en una dirección IP accesible a través de Internet. Para más información, consulte [Creación de una instancia externa de App Service Environment][MakeExternalASE].
- **ASE de ILB**: Expone las aplicaciones hospedadas en ASE en una dirección IP dentro de su red virtual. El punto de conexión interno es un equilibrador de carga interno (ILB), y esta es la razón por la que se denomina ASE de ILB. Para más información, consulte [Creación y uso de un ASE de ILB][MakeILBASE].

Hay dos versiones de App Service Environment: ASEv1 y ASEv2. Para información sobre ASEv1, consulte [Introducción a App Service Environment v1][ASEv1Intro]. Una instancia de ASEv1 se puede implementar en una red virtual clásica o en una de Resource Manager. En el caso de ASEv2, la implementación solo se puede realizar en una red virtual de Resource Manager.

Todas las llamadas que vayan a Internet desde una instancia de ASE salen de la red virtual a través de una IP virtual asignada para el ASE. La dirección IP pública de esta IP virtual es la dirección IP de origen para todas las llamadas desde el ASE que vayan a Internet. Si las aplicaciones en la instancia de ASE realizan llamadas a los recursos de la red virtual o a través de una VPN, la IP de origen es una de las de la subred usada por su ASE. Dado que el ASE está dentro de la red virtual, también puede tener acceso a los recursos dentro de la red virtual sin ninguna configuración adicional. Si la red virtual está conectada a la red local, las aplicaciones en su ASE también tienen acceso a los recursos allí sin configuración adicional.

![ASE externo][1] 

Si tiene un ASE externo, la VIP pública es también el punto de conexión que las aplicaciones ASE usan para:

* HTTP/S. 
* FTP/S. 
* Implementación web.
* Depuración remota.

![ASE de ILB][2]

Si tiene un ASE de ILB, la dirección del ILB es el punto de conexión para HTTP/S, FTP/S, implementación web y depuración remota.

Los puertos de acceso de aplicación normales son:

| Uso | De | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurable por el usuario |  80, 443 |
|  FTP/FTPS    | Configurable por el usuario |  21, 990, 10001-10020 |
|  Depuración remota en Visual Studio  |  Configurable por el usuario |  4020, 4022, 4024 |
|  Implementar servicio Web | Configurable por el usuario | 8172 |

Es así tanto si está en un ASE externo como en un ASE de ILB. Si se encuentra en un ASE externo, llega a esos puertos en la VIP pública. Si se encuentra en un ASE de ILB, llega a esos puertos en el ILB. Si bloquea el puerto 443, puede afectar a algunas características que se exponen en el portal. Para más información, consulte [Dependencias del portal](#portaldep).

## <a name="ase-subnet-size"></a>Tamaño de la subred de ASE ##

El tamaño de la subred que se utiliza para hospedar una instancia de ASE no se puede modificar una vez que dicha instancia de ASE se ha implementado.  La instancia de ASE utiliza una dirección para cada rol de la infraestructura, así como para cada instancia del plan de App Service en entorno aislado.  Además, hay cinco direcciones que Redes de Azure utiliza para cada una de las subredes generadas.  Las instancias de ASE que no tengan ningún plan de App Service utilizarán 12 direcciones antes de que se cree una aplicación.  Si se trata de un ASE de ILB, utilizará 13 direcciones antes de que se cree una aplicación en dicho ASE. A medida que escale horizontalmente el ASE, los roles de infraestructura se agregan a cada múltiplo de 15 y 20 de las instancias del plan de App Service.

   > [!NOTE]
   > Puede no haber nada más en la subred excepto el ASE. Asegúrese de elegir un espacio de direcciones que pueda crecer en el futuro. No puede cambiar esta configuración posteriormente. Se recomienda un tamaño de `/24` con doscientas cincuenta y seis direcciones.

Al escalar o reducir verticalmente, se agregan nuevos roles del tamaño adecuado y, a continuación, las cargas de trabajo se migran del tamaño actual al tamaño de destino. Solo después de migran las aplicaciones se quitan las máquinas virtuales originales. Esto significa que si tuviera un ASE con 100 instancias ASP, habría un período en el que se necesitaría el doble del número de máquinas virtuales.  Es por esta razón que se recomienda el uso de un "/24" para acomodar cualquier cambio que pueda necesitar.  

## <a name="ase-dependencies"></a>Dependencias de ASE ##

### <a name="ase-inbound-dependencies"></a>Dependencias de entrada de ASE ###

Las dependencias de acceso de entrada de ASE son:

| Uso | De | Para |
|-----|------|----|
| Administración | Direcciones de administración de App Service | Subred de ASE: 454, 455 |
|  Comunicación interna ASE | Subred de ASE: Todos los puertos | Subred de ASE: Todos los puertos
|  Permitir entrada de Azure Load Balancer | Azure Load Balancer | Subred de ASE: Todos los puertos
|  Direcciones IP asignadas a las aplicaciones | Direcciones asignadas a las aplicaciones | Subred de ASE: Todos los puertos

El tráfico de administración entrante proporciona el comando y control del ASE además de supervisión del sistema. Las direcciones de origen para este tráfico se incluyen en el documento [Direcciones de administración de App Service Environment][ASEManagement]. La configuración de seguridad de red tiene que permitir el acceso desde todas las direcciones IP en los puertos 454 y 455. Si se bloquea el acceso desde esas direcciones, ASE se volverá incorrecto y se suspenderá.

Dentro de la subred de ASE hay muchos puertos usados para la comunicación de componentes interna y pueden cambiar.  Esto requiere que todos los puertos de la subred de ASE sean accesibles desde la subred de ASE. 

Para la comunicación entre el equilibrador de carga de Azure y la subred de ASE, los puertos mínimos que deben abrirse son 454, 455 y 16001. El puerto 16001 se usa para mantener activo el tráfico entre el equilibrador de carga y el ASE. Si usa un ASE de ILB, puede bloquear el tráfico solo en los puertos 454, 455 y 16001.  Si usa un ASE externo, debe tener en cuenta los puertos de acceso a las aplicaciones normales.  Si usa direcciones asignadas a las aplicaciones, debe abrirlo en todos los puertos.  Cuando se asigna una dirección a una aplicación específica, el equilibrador de carga usará puertos no conocidos de antemano para enviar tráfico HTTP y HTTPS al ASE.

Si usa direcciones IP asignadas a las aplicaciones, debe permitir el tráfico de las direcciones IP asignadas a sus aplicaciones en la subred de ASE.

El tráfico TCP que entra por los puertos 454 y 455 debe salir de la misma VIP o tendrá un problema de enrutamiento asimétrico. 

### <a name="ase-outbound-dependencies"></a>Dependencias de salida de ASE ###

Para el acceso de salida, un ASE depende de varios sistemas externos. Muchas de estas dependencias del sistema se definen con nombres DNS y no se asignan a un conjunto fijo de direcciones IP. Por tanto, el ASE requiere acceso de salida desde la subred de ASE a todas las direcciones IP externas en diversos puertos. 

La lista completa de las dependencias de salida se enumeran en el documento que describe el [bloqueo del tráfico de salida de App Service Environment](./firewall-integration.md). Si el ASE pierde el acceso a sus dependencias, deja de funcionar. Cuando esto ocurre durante un tiempo suficientemente prolongado, el ASE se suspende. 

### <a name="customer-dns"></a>DNS del cliente ###

Si la red virtual se configura con un servidor DNS definido por el cliente, las cargas de trabajo del inquilino lo utilizan. El ASE todavía tiene que comunicarse con Azure DNS para fines de administración. 

Si la red virtual se configura con un cliente DNS en el otro lado de una VPN, el servidor DNS tiene que ser accesible desde la subred que contiene el ASE.

Para probar la resolución de la aplicación web, puede usar el comando de consola *nameresolver*. Vaya a la ventana de depuración en el sitio de scm para su aplicación o vaya a la aplicación en el portal y seleccione la consola. Desde el símbolo del shell puede enviar el comando *nameresolver* junto con la dirección que desea buscar. El resultado que se obtiene es el mismo que el que obtendría la aplicación al hacer la misma búsqueda. Si se usa nslookup, se hará una búsqueda con Azure DNS en su lugar.

Si cambia la configuración de DNS de la red virtual en la que se encuentra el ASE, tendrá que reiniciar el ASE. Para evitar tener que reiniciar la instancia de ASE, se recomienda configurar el valor de DNS para la red virtual antes de crear el ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependencias del portal ##

Además de las dependencias funcionales de un ASE, hay algunos elementos adicionales relacionados con la experiencia del portal. Algunas de las funcionalidades en Azure Portal dependen de un acceso directo al _sitio SCM_. Para cada aplicación en Azure App Service, hay dos direcciones URL. La primera dirección URL es para acceder a la aplicación. La segunda dirección URL es para el acceso al sitio SCM, que también se denomina el _consola Kudu_. Algunas de las características que usan el sitio SCM incluyen:

-   Trabajos web
-   Functions
-   Secuencias de registro
-   Kudu
-   Extensiones
-   Explorador de procesos
-   Consola

Cuando se usa una ASE de ILB, el sitio SCM no está accesible en Internet desde fuera de la red virtual. Cuando la aplicación se hospeda en un ASE de ILB, algunas funcionalidades no se podrán usar desde el portal.  

Muchas de estas funcionalidades que dependen del sitio SCM también están disponibles directamente en la consola Kudu. Puede conectarse directamente, en lugar de utilizar el portal. Si la aplicación se hospeda en un ASE de ILB, use las credenciales de publicación para iniciar sesión. La dirección URL para acceder al sitio SCM de una aplicación hospedada en un ASE de ILB tiene el formato siguiente: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Si su ASE ILB es el nombre de dominio *contoso.net* y el nombre de la aplicación es *aplicacionprueba*, la aplicación está accesible en *aplicacionprueba.contoso.net*. El sitio SCM que lo acompaña está accesible en *aplicacionprueba.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Trabajos web y de funciones ##

Tanto los trabajos de funciones como los trabajos web dependen del sitio de SCM, pero se pueden usar en el portal, incluso si las aplicaciones se encuentran en un ASE de ILB, siempre y cuando el explorador pueda llegar al sitio de SCM.  Si usa un certificado autofirmado con su ASE de ILB, debe habilitar el explorador para que confíe en ese certificado.  En el caso de IE y Microsoft Edge, esto significa que el certificado debe estar en el almacén de confianza del equipo.  Si usa Chrome, esto significa que aceptó el certificado en el explorador anteriormente al pulsar directamente en el sitio de SCM.  La mejor solución es usar un certificado comercial que se encuentre en la cadena de confianza del explorador.  

## <a name="ase-ip-addresses"></a>Direcciones IP de ASE ##

Un ASE tiene algunas direcciones IP que es necesario tener en cuenta. Son las siguientes:

- **Dirección IP pública de entrada**: Se usa para el tráfico de la aplicación en una instancia de ASE externa y para el tráfico de administración tanto en una instancia de ASE externa como en una instancia de ASE de ILB.
- **Dirección IP pública de salida**: Se usa como dirección IP "desde" para las conexiones de salida desde la instancia de ASE que dejan la red virtual y que no se enrutan hacia una VPN.
- **Dirección IP del ILB**: Si usa una instancia de ASE de ILB.
- **Direcciones SSL basadas en IP asignadas a la aplicación**: Solo son posibles con una instancia de ASE externa y cuando hay configurada una SSL basada en IP.

Todas estas direcciones IP son fácilmente visibles en un ASEv2 en Azure Portal desde la interfaz de usuario de ASE. Si tiene un ASE de ILB, aparece la dirección IP para el ILB.

   > [!NOTE]
   > Estas direcciones IP no cambiarán mientras el ASE se mantenga en funcionamiento.  Si el ASE se suspende y se restaura, cambiarán las direcciones usadas por él. La causa normal de la suspensión del ASE es si se bloquea el acceso de administración de entrada o se bloquea el acceso a una dependencia del ASE. 

![Direcciones IP][3]

### <a name="app-assigned-ip-addresses"></a>Direcciones IP asignadas a la aplicación ###

Con un ASE externo, puede asignar direcciones IP a las aplicaciones individuales. No puede hacer eso con un ASE de ILB. Para más información acerca de cómo configurar la aplicación para que tenga su propia dirección IP, consulte [Enlace de un certificado SSL personalizado a Azure App Service](../app-service-web-tutorial-custom-ssl.md).

Cuando una aplicación tiene su propia dirección SSL basada en IP, el ASE reserva dos puertos para asignar a esa dirección IP. Un puerto es para el tráfico HTTP y el otro es para HTTPS. Estos puertos se muestran en la interfaz de usuario de ASE en la sección de direcciones IP. El tráfico tiene que poder conectar con esos puertos desde la dirección IP virtual o las aplicaciones no serán accesibles. Es importante recordar este requisito al configurar grupos de seguridad de red (NSG).

## <a name="network-security-groups"></a>Grupos de seguridad de red ##

Los [grupos de seguridad de red][NSGs] permiten controlar el acceso a la red dentro de una red virtual. Cuando usa el portal, hay una regla de denegación implícita con la prioridad más baja que deniega todo. Lo que crea son las reglas de permiso.

En un ASE, no tiene acceso a las máquinas virtuales que se utilizan para hospedar el propio ASE. Están en una suscripción administrada por Microsoft. Si desea limitar el acceso a las aplicaciones en el ASE, tiene que establecer los NSG en la subred de ASE. Al hacerlo, tiene que prestar mucha atención a las dependencias de ASE. Si se bloquea alguna de las dependencias, el ASE deja de funcionar.

Los NSG pueden configurarse mediante Azure Portal o a través de PowerShell. Esta información muestra Azure Portal. Puede crear y administrar los NSG en el portal como un recurso de nivel superior en **Redes**.

Cuando los requisitos de entrada y salida se tienen en cuenta, los NSG deben ser similares a lo que se muestra en este ejemplo. El intervalo de direcciones de VNet es _192.168.250.0/23_ y la subred en la que está ASE es _192.168.251.128/25_.

Los dos primeros requisitos de entrada para que el ASE funcione están en la parte superior de la lista en este ejemplo. Estos habilitan la administración del ASE y permiten que el ASE se comunique consigo mismo. Las demás entradas son todas configurables por inquilino y pueden controlar el acceso por red a las aplicaciones hospedadas en el ASE. 

![Reglas de seguridad de entrada][4]

Una regla predeterminada permite que las direcciones IP en la red virtual se comuniquen con la subred de ASE. Otra regla predeterminada permite que el equilibrador de carga, también conocido como VIP pública, se comunique con el ASE. Para ver las reglas predeterminadas, seleccione **Reglas predeterminadas** junto al icono **Agregar**. Si coloca una regla para denegar todo lo demás después de las reglas de NSG que se muestran, evita el tráfico entre la IP virtual y el ASE. Para impedir el tráfico procedente de dentro de la red virtual, agregue su propia regla para permitir la entrada. Use un origen igual que AzureLoadBalancer con **cualquier** destino y un intervalo de puertos **\***. Puesto que la regla NSG se aplica a la subred de ASE, no es necesario que sea específico en el destino.

Si ha asignado una dirección IP a la aplicación, asegúrese de mantener los puertos abiertos. Para ver los puertos, seleccione **App Service Environment** > **Direcciones IP**.  

Se necesitan todos los elementos que se muestran en las siguientes reglas de salida, excepto el último. Estos elementos habilitan el acceso de red a las dependencias de ASE que se han indicado anteriormente en este artículo. Si bloquea alguna de ellas, el ASE deja de funcionar. El último elemento de la lista habilita al ASE para que se comunique con otros recursos de la red virtual.

![Reglas de seguridad de entrada][5]

Una vez que haya definido los NSG, asígnelos a la subred en la que se encuentra el ASE. Si no recuerda la red virtual o la subred de ASE, puede verlo desde la página del portal de ASE. Para asignar el NSG a la subred, vaya a la interfaz de usuario de la subred y seleccione el NSG.

## <a name="routes"></a>Rutas ##

La tunelización forzada se produce cuando establece las rutas de la red virtual para que el tráfico saliente no vaya directamente a Internet, sino a otro lugar como una puerta de enlace de ExpressRoute o un dispositivo virtual.  Si necesita configurar su ASE de esta manera, lea el documento en [Configuración de App Service Environment con tunelización forzada][forcedtunnel].  Este documento le indicará las opciones disponibles para trabajar con ExpressRoute y la tunelización forzada.

Cuando se crea un ASE en el portal, se crea también un conjunto de tablas de rutas en la subred que se crea con el ASE.  Esas rutas sencillamente indican que se envíe el tráfico saliente directamente a Internet.  
Para crear manualmente las mismas rutas, siga estos pasos:

1. Vaya a Azure Portal. Seleccione **Redes** > **Tablas de rutas**.

2. Cree una nueva tabla de rutas en la misma región que la red virtual.

3. En la interfaz de usuario de la tabla de rutas, seleccione **Rutas** > **Agregar**.

4. Establezca **Tipo del próximo salto** en **Internet** y **Prefijo de dirección** en **0.0.0.0/0**. Seleccione **Guardar**.

    Verá algo parecido a lo siguiente:

    ![Rutas funcionales][6]

5. Después de crear la nueva tabla de rutas, vaya a la subred que contiene el ASE. Seleccione la tabla de rutas de la lista en el portal. Después de guardar el cambio, debería ver los NSG y las rutas anotadas con la subred.

    ![NSG y rutas][7]

## <a name="service-endpoints"></a>Puntos de conexión de servicio ##

Los puntos de conexión de servicio le permiten restringir el acceso de los servicios multiinquilino a un conjunto de subredes y redes virtuales de Azure. Puede leer más acerca de los puntos de conexión de servicio en la documentación de los [puntos de conexión de servicio de Azure Virtual Network][serviceendpoints]. 

Cuando se habilitan puntos de conexión de servicio en un recurso, hay rutas que se crean con prioridad más alta que las demás. Si usa puntos de conexión de servicio con una ASE con túnel forzado, el tráfico de administración de Azure SQL y de Azure Storage no se enruta a través de tunelización forzada. 

Cuando los puntos de conexión de servicio se habilitan en una subred con una instancia de Azure SQL, todas las instancias de Azure SQL conectadas desde esa subred deben tener habilitados también los puntos de conexión de servicio. Si desea acceder a varias instancias de Azure SQL desde la misma subred, debe habilitar los puntos de conexión de servicio en todas ellas y no solo en una. Azure Storage no se comporta igual que Azure SQL. Cuando se habilitan los puntos de conexión de servicio con Azure Storage, se bloquea el acceso a ese recurso desde la subred, pero aún se puede tener acceso a otras cuentas de Azure Storage incluso si no tienen habilitados los puntos de conexión de servicio.  

![Puntos de conexión de servicio][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
