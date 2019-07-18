---
title: ¿Qué es Azure Load Balancer?
titlesuffix: Azure Load Balancer
description: Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Aprenda cómo funciona el equilibrador de carga y aproveche sus ventajas en la nube.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: allensu
ms.openlocfilehash: fb7c0c31ad91bfdb6ea360c1909a216f0779ebde
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274615"
---
# <a name="what-is-azure-load-balancer"></a>¿Qué es Azure Load Balancer?

Azure Load Balancer permite escalar las aplicaciones y crear alta disponibilidad para sus servicios. Load Balancer admite escenarios de entrada y de salida, proporciona baja latencia y alto rendimiento, y puede escalar hasta millones de flujos para todas las aplicaciones TCP y UDP.  

Load Balancer distribuye los nuevos flujos de entrada que llegan a las instancias del grupo de servidores front-end a back-end del equilibrador de carga, según reglas y sondeos de estado. 

Además, un equilibrador de carga público puede traducir direcciones IP privadas en direcciones IP públicas para proporcionar conexiones de salida para máquinas virtuales dentro de la red virtual.

Azure Load Balancer está disponible en dos SKU: Básico y Estándar. Hay diferencias en la escala, las características y los precios. Cualquier escenario posible con Load Balancer Básico puede crearse con Load Balancer Estándar, aunque los enfoques pueden ser ligeramente diferentes. Para conocer Load Balancer, es importante familiarizarse con los conceptos básicos y las diferencias específicas de cada SKU.

## <a name="why-use-load-balancer"></a>¿Por qué usar Load Balancer? 

Azure Load Balancer sirve para:

* Equilibrar la carga del tráfico entrante en las máquinas virtuales. Esta configuración se conoce como [equilibrador de carga público](#publicloadbalancer).
* Equilibra la carga del tráfico entre máquinas virtuales de una red virtual. También puede acceder a un servidor front-end de Load Balancer desde una red local en un escenario híbrido. Ambos escenarios utilizan una configuración que se conoce como [equilibrador de carga interno](#internalloadbalancer).
* Reenviar el tráfico a un puerto específico de máquinas virtuales determinadas con reglas de traducción de direcciones de red (NAT) de entrada.
* Proporcionar [conectividad de salida](load-balancer-outbound-connections.md) para máquinas virtuales dentro de la red virtual mediante un equilibrador de carga público.


>[!NOTE]
> Azure ofrece un conjunto de soluciones de equilibrio de carga completamente administradas para sus escenarios. Si desea finalización con el protocolo de seguridad de la capa de transporte (TLS) ("descarga SSL") procesamiento de niveles de aplicación por solicitud HTTP/HTTPS, revise [Application Gateway](../application-gateway/application-gateway-introduction.md). Si desea equilibrio de carga de DNS global, consulte [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Sus escenarios integrales pueden beneficiarse de la combinación de estas soluciones según sea necesario.

## <a name="what-are-load-balancer-resources"></a>¿Qué son los recursos de Load Balancer?

Un recurso de Load Balancer puede existir como un equilibrador de carga público o un equilibrador de carga interno. Las funciones de un recurso de Load Balancer se expresan como un front-end, una regla, un sondeo de estado y una definición de grupo de servidores back-end. El grupo de servidores back-end se especifica en la máquina virtual para colocarla en él.

Los recursos de Load Balancer son objetos en los cuales se puede expresar cómo debe Azure programar su infraestructura multiinquilino para lograr el escenario que se desea crear. No hay ninguna relación directa entre los recursos de Load Balancer y la infraestructura real. Al crear un equilibrador de carga no se crea una instancia y siempre hay capacidad disponible. 

## <a name="fundamental-load-balancer-features"></a>Características fundamentales de Load Balancer

Load Balancer proporciona las siguientes funcionalidades básicas para las aplicaciones TCP y UDP:

* **Equilibrio de carga**

    Con Azure Load Balancer puede crear una regla de equilibrio de carga para distribuir el tráfico que llega a un front-end a las instancias del grupo de servidores back-end. Load Balancer usa un algoritmo hash para la distribución de los flujos de entrada y reescribe los encabezados de los flujos en las instancias del grupo de servidores back-end. Un servidor está disponible para recibir nuevos flujos cuando el sondeo de estado indica un punto de conexión de back-end correcto.
    
    De forma predeterminada, Load Balancer usa un hash de tupla 5 compuesto por IP de origen, puerto de origen, IP de destino, puerto de destino y número de protocolo IP para asignar los flujos a los servidores disponibles. Puede elegir crear afinidad con una dirección IP de origen específica; para ello, elija un hash de tupla 2 o 3 para una regla determinada. Todos los paquetes del mismo flujo de paquetes llegan a la misma instancia subyacente al servidor front-end con equilibrio de carga. Cuando el cliente inicia un nuevo flujo desde la misma dirección IP de origen, el puerto de origen cambia. Como resultado, la tupla 5 resultante puede hacer que el tráfico vaya a un punto de conexión de back-end diferente.

    Para más información, consulte [Modo de distribución de Load Balancer](load-balancer-distribution-mode.md). En la siguiente imagen se muestra la distribución basada en hash:

    ![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Ilustración: distribución basada en hash*

* **Reenvío de puertos**

    Con Load Balancer puede crear una regla NAT de entrada para reenviar el tráfico desde un puerto específico de la dirección IP de un servidor front-end específico a un puerto específico de una instancia de back-end específica dentro de la red virtual. Esto también se realiza con la misma distribución basada en hash que se usa para el equilibrio de carga. Los escenarios comunes para esta funcionalidad son sesiones de Protocolo de Escritorio remoto (RDP) o Secure Shell (SSH) en instancias de máquina virtual individuales dentro de la red virtual de Azure. Puede asignar varios puntos de conexión internos a los diferentes puertos de la misma dirección IP de front-end. Puede usar las direcciones IP de front-end para administrar las máquinas virtuales de forma remota a través de Internet sin necesidad de un JumpBox adicional.

* **Independiente de la aplicación y transparente**

    Load Balancer no interactúa directamente con TCP, UDP ni el nivel de aplicación, y admite cualquier escenario de aplicación TCP o UDP.  Load Balancer no finaliza ni origina flujos, interactúa con la carga del flujo, no proporciona función de puerta de enlace al nivel de aplicación, y el protocolo de enlace se realiza siempre directamente entre el cliente y la instancia del grupo de servidores back-end.  Una respuesta a un flujo de entrada siempre es una respuesta de una máquina virtual.  Cuando llega el flujo a la máquina virtual, también se conserva la dirección IP de origen original.  Un par de ejemplos para mostrar más detalladamente la transparencia:
    - A cada punto de conexión solo le responde una máquina virtual.  Por ejemplo, un protocolo de enlace TCP se realiza siempre entre el cliente y la máquina virtual de back-end seleccionada.  Una respuesta a una solicitud a un front-end es una respuesta generada por una máquina virtual de back-end. Cuando se asegura correctamente la conectividad con un front-end, se asegura la conectividad de extremo a extremo con al menos una máquina virtual de back-end.
    - Las cargas de aplicaciones son transparentes para Load Balancer y todas las aplicaciones UDP o TCP son compatibles. Para las cargas de trabajo que requieren procesamiento por solicitud HTTP o manipulación de cargas de nivel de aplicación (por ejemplo, análisis de direcciones URL de HTTP), debe usar un equilibrador de carga de 7 niveles como [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Dado que Load Balancer es independiente de la carga TCP y no se proporciona descarga TLS ("SSL"), puede crear escenarios cifrados de extremo a extremo mediante Load Balancer y obtener un amplio escalado horizontal para las aplicaciones TLS, ya que la conexión TLS se termina en la propia máquina virtual.  Por ejemplo, la capacidad para claves de sesión TLS solo está limitada por el tipo y número de máquinas virtuales que se agregan al grupo de servidores back-end.  Si necesita "descarga SSL", tratamiento del nivel de aplicación o desea delegar la administración de certificados en Azure, debe utilizar el equilibrador de carga de Azure de 7 niveles [Application Gateway](https://azure.microsoft.com/services/application-gateway) en su lugar.
        

* **Reconfiguración automática**

    Load Balancer se reconfigura inmediatamente al escalar o reducir instancias verticalmente. Al agregar o quitar máquinas virtuales del grupo de servidores back-end, el equilibrador de carga se vuelve a configurar sin necesidad de operaciones adicionales en el recurso del equilibrador de carga.

* **Sondeos de mantenimiento**

    Load Balancer usa los sondeos de mantenimiento que el usuario define para determinar el mantenimiento de las instancias en el grupo de servidores back-end. Cuando un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a las instancias incorrectas. Las conexiones existentes no se ven afectadas y continúan hasta que la aplicación finalice el flujo, se produzca un tiempo de inactividad o se apague la máquina virtual.
     
    Load Balancer proporciona [distintos tipos de sondeo de mantenimiento](load-balancer-custom-probe-overview.md#types) para puntos de conexión TCP, HTTP y HTTPS.

    Además, cuando se usa para los servicios en la nube clásicos, se permite un tipo adicional:  [agente invitado](load-balancer-custom-probe-overview.md#guestagent).  Esta solución debe considerarse un sondeo de mantenimiento de último recurso y no se recomienda cuando otras opciones son viables.
    
* **Conexiones de salida (SNAT)**

    Todos los flujos de salida desde direcciones IP privadas dentro de la red virtual hasta direcciones IP públicas en Internet se pueden traducir a una dirección IP de front-end del equilibrador de carga. Cuando un front-end público está asociado a una máquina virtual de back-end por una regla de equilibrio de carga, Azure programa conexiones de salida que se traducen automáticamente a la dirección IP del front-end público.

  * Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que el front-end puede asignarse dinámicamente a otra instancia del servicio.
  * Facilita la administración de la lista de control de acceso (ACL). Las ACL que se expresan en términos de IP de front-end no cambian cuando los servicios se escalan o se reducen verticalmente, o se vuelven a implementar.  Traducir las conexiones de salida a un menor número de direcciones IP que de máquinas puede reducir la carga de creación de listas de permitidos.

    Para más información, consulte el artículo de [conexiones de salida](load-balancer-outbound-connections.md).

Load Balancer Estándar tiene funcionalidades específicas de SKU además de estos aspectos básicos. Consulte el resto de este artículo para obtener más información.

## <a name="skus"></a> Comparación de las SKU de Load Balancer

Load Balancer admite las SKU Básico y Estándar, cuyos precios, características y escalas difieren. Cualquier escenario posible con Load Balancer Básico puede crearse con Load Balancer Estándar también. De hecho, las API para ambas SKU son similares y se invocan especificando una SKU. La API que admite las SKU de Load Balancer y la dirección IP pública es la versión 2017-08-01 y posteriores. Ambas SKU tiene la misma API general y estructura.

Sin embargo, según la SKU que se elija, la configuración del escenario completo puede ser ligeramente diferente. La documentación de Load Balancer indica si un artículo se aplica únicamente a una SKU específica. Para comparar y comprender las diferencias, consulte la siguiente tabla. Para más información, consulte [Introducción a Load Balancer Estándar](load-balancer-standard-overview.md).

>[!NOTE]
> Los nuevos diseños deben adoptar Load Balancer Estándar. 

Las máquinas virtuales independientes, los conjuntos de disponibilidad y los conjuntos de escalado de máquinas virtuales solo se pueden conectar a una SKU, nunca a ambas. Cuando se usan con direcciones IP públicas, las SKU de Load Balancer y de la dirección IP pública deben coincidir. Las SKU de Load Balancer y de la dirección IP pública no se pueden intercambiar.

_El procedimiento recomendado es especificar las SKU de forma explícita, aunque aún no es obligatorio._  En este momento, se están realizando los cambios necesarios mínimos. Si no se especifica una SKU, se interpreta que la intención es usar la versión de API 2017-08-01 de la SKU Básico.

>[!IMPORTANT]
>Load Balancer Estándar es un nuevo producto de Load Balancer y, en gran medida, un superconjunto de Load Balancer Básico. Existen diferencias importantes y deliberadas entre ambos productos. Cualquier escenario posible con Load Balancer Básico puede crearse también con Load Balancer Estándar. Si ya está acostumbrado a Load Balancer Básico, debe familiarizarse con Load Balancer Estándar para comprender los cambios recientes en el comportamiento de ambos y su impacto. Consulte detenidamente esta sección.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para más información, consulte los [límites de servicio de Load Balancer](https://aka.ms/lblimits). Para más información de Load Balancer Estándar, consulte los artículos de [introducción](load-balancer-standard-overview.md), [precios](https://aka.ms/lbpricing) y [Acuerdo de Nivel de Servicio](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceptos

### <a name = "publicloadbalancer"></a>Equilibrador de carga público

El equilibrador de carga público asigna la dirección IP pública y el número de puerto del tráfico de entrada a la dirección IP privada y el número de puerto de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual. Al aplicar reglas de equilibrio de carga, puede distribuir tipos específicos de tráfico en varias máquinas virtuales o servicios. Por ejemplo, puede distribuir la carga del tráfico de solicitudes web entre varios servidores web.

En la siguiente ilustración, se muestra un punto de conexión de carga equilibrada para el tráfico web que se comparte entre tres máquinas virtuales en el puerto TCP 80 y público. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![Ejemplo de equilibrador de carga público](./media/load-balancer-overview/IC727496.png)

*Ilustración: equilibrio de carga del tráfico web mediante un equilibrador de carga público*

Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública de una aplicación web en el puerto TCP 80, Azure Load Balancer distribuye las solicitudes entre las tres máquinas virtuales del conjunto con equilibrio de carga. Para más información sobre los algoritmos de Load Balancer, consulte la sección de [Características de Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features) de este artículo.

De forma predeterminada, Azure Load Balancer distribuye el tráfico de red equitativamente entre varias instancias de máquina virtual. También puede configurar la afinidad de la sesión. Para más información, consulte [Modo de distribución de Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Equilibrador de carga interno

Un equilibrador de carga interno solo dirige el tráfico a los recursos que están dentro de una red virtual o que usan una VPN para acceder a la infraestructura de Azure. En este sentido, un equilibrador de carga interno difiere de un equilibrador de carga público. La infraestructura de Azure restringe el acceso a las direcciones IP de front-end con equilibrio de carga de una red virtual. Las direcciones IP de front-end y las redes virtuales no se exponen nunca directamente a un punto de conexión de Internet. Las aplicaciones de línea de negocio internas se ejecutan en Azure y se accede a ellas desde Azure o desde recursos locales.

Un equilibrador de carga interno permite los siguientes tipos de equilibrio de carga:

* **Dentro de una red virtual**: equilibrio de carga entre las máquinas virtuales de la red virtual y un conjunto de máquinas virtuales que residen en la misma red virtual.
* **En una red virtual entre entornos locales**: equilibrio de carga entre los equipos locales y un conjunto de máquinas virtuales que residen en la misma red virtual. 
* **En aplicaciones de niveles múltiples**: equilibrio de carga para aplicaciones de niveles múltiples accesibles desde Internet, a cuyos niveles de back-end no se puede acceder desde Internet. Los niveles de back-end requieren un equilibrio de carga del tráfico desde el nivel accesible desde Internet (consulte la figura siguiente).
* **En aplicaciones de línea de negocio**: equilibrio de carga para las aplicaciones de línea de negocio hospedadas en Azure sin requerir hardware ni software adicional de equilibrador de carga. Este escenario incluye servidores locales que se encuentran en el conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

![Ejemplo de equilibrador de carga interno](./media/load-balancer-overview/IC744147.png)

*Ilustración: equilibrio de carga de aplicaciones de niveles múltiples mediante equilibradores de carga público e interno*

## <a name="pricing"></a>Precios

Se cobra el uso de Standard Load Balancer.

- Número de reglas de salida y equilibrio de carga configurados (las reglas nat de entrada no cuentan para el número total de reglas)
- Cantidad de datos procesados entrantes y salientes con independencia de la regla. 

Para más información sobre los precios de Load Balancer Estándar, vaya la página [Precios de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer Básico se ofrece sin cargo.

## <a name="sla"></a>Contrato de nivel de servicio

Para información sobre el Acuerdo de Nivel de Servicio de Load Balancer Estándar, vaya la página [Acuerdo de Nivel de Servicio de Load Balancer](https://aka.ms/lbsla). 

## <a name="limitations"></a>Limitaciones

- Load Balancer es un producto TCP o UDP de equilibrio de carga y enrutamiento de puerto para estos protocolos IP específicos.  Las reglas de equilibrio de carga y las reglas de traducción de direcciones de red de entrada son compatibles con TCP y UDP y no con otros protocolos IP, como ICMP. Load Balancer no termina la carga ni responde a ella, ni interactúa con la carga de los flujos UDP o TCP. No es un servidor proxy. La validación correcta de la conectividad con un front-end debe realizarse en banda con el mismo protocolo de equilibrado de carga o la misma regla de traducción de direcciones de red de entrada (TCP o UDP) _y_, para ver una respuesta desde el front-end, al menos una de las máquinas virtuales debe generar una respuesta para un cliente.  Si no se recibe una respuesta en banda desde el front-end de Load Balancer, significa que ninguna máquina virtual puede responder.  No es posible interactuar con el front-end de Load Balancer sin una máquina virtual que pueda responder.  Esto también se aplica a las conexiones salientes donde el [enmascaramiento del puerto SNAT](load-balancer-outbound-connections.md#snat) es solo es compatible con TCP y UDP; los demás protocolos IP (como ICMP) también producirán errores.  Asigne una dirección IP pública en el nivel de instancia para la mitigación.
- A diferencia de los equilibradores de carga públicos que proporcionan [conexiones de salida](load-balancer-outbound-connections.md) al realizar la transición de direcciones IP privadas dentro de la red virtual a direcciones IP públicas, los internos no traducen las conexiones de salida originales al front-end de un equilibrador de carga interno, ya que ambos se encuentran en el espacio de direcciones IP privadas.  Esto evita la posibilidad de agotamiento del puerto SNAT dentro de un único espacio de direcciones IP internas donde no se necesita traducción.  El inconveniente es que si un flujo de salida de una máquina virtual del grupo de servidores back-end intenta un flujo al front-end del equilibrador de carga interno donde reside el grupo _y_ se asigna a sí mismo, los lados del flujo no coinciden y se produce un error en el flujo.  Si el flujo no se asignó a la misma máquina virtual en el grupo de servidores back-end que creó el flujo para el front-end, se realizará correctamente.   Cuando se asigna el flujo a sí mismo, el flujo de salida parece originarse en la máquina virtual hacia el front-end; y el flujo de entrada correspondiente, en la propia máquina virtual hacia sí misma. Desde el punto de vista de los sistemas operativos invitados, las partes de entrada y salidas del mismo flujo no coinciden en la máquina virtual. La pila TCP no reconocerá estas mitades como parte del mismo flujo, ya que el origen y el destino no coinciden.  Cuando el flujo se asigna a cualquier otra máquina virtual del grupo de servidores back-end, sus mitades coinciden y la máquina virtual responde correctamente al flujo.  El síntoma de este escenario es tiempos inactividad de conexión intermitentes cuando finaliza el flujo para el mismo back-end en que se originó el flujo. Hay varias soluciones comunes para lograr este escenario de forma confiable (flujos que se originan desde un grupo de servidores back-end hasta el respectivo front-end del equilibrador de carga interno de los grupos de servidores back-end), como la inserción de una capa de proxy detrás del Load Balancer interno o el [uso de reglas de estilo DSR](load-balancer-multivip-overview.md).  Los clientes pueden combinar un Load Balancer interno con cualquier proxy de terceros o sustituir el [Application Gateway](../application-gateway/application-gateway-introduction.md) interno para los escenarios proxy limitados a HTTP/HTTPS. Aunque podría utilizar un equilibrador de carga público para la mitigación, el escenario resultante es propenso al [agotamiento de SNAT](load-balancer-outbound-connections.md#snat) y debe evitarse, a menos que se administre con cuidado.

## <a name="next-steps"></a>Pasos siguientes

Ahora tiene una introducción a Azure Load Balancer. Para empezar a trabajar con un equilibrador de carga, cree uno, cree máquinas virtuales con una extensión personalizada de IIS instalada y equilibre la carga de la aplicación web en las máquinas virtuales. Para información sobre cómo hacerlo, consulte la guía de inicio rápido de [creación de una instancia de Load Balancer Básico](quickstart-create-basic-load-balancer-portal.md).
