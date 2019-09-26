---
title: Preguntas frecuentes sobre Azure Private Link
description: Obtenga información sobre Azure Private Link.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 32e570e098c9a3f95dfc5393348cccb1ebe1fb60
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104582"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Preguntas frecuentes sobre Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>¿Qué es el punto de conexión privado de Azure y el servicio Azure Private Link?

- **[Punto de conexión privado de Azure](private-endpoint-overview.md)** : un punto de conexión privado de Azure es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link. Puede usar puntos de conexión privados para conectarse a un servicio PaaS de Azure que admita Private Link o a su propio servicio Private Link.
- **[Servicio Azure Private Link](private-link-service-overview.md)** : el servicio Azure Private Link es un servicio creado por un proveedor de servicios. Actualmente, se puede asociar un servicio Private Link a la configuración de IP de front-end de una instancia de Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>¿Cómo se envía el tráfico al usar Private Link?
El tráfico se envía de forma privada mediante la red troncal de Microsoft. No recorre Internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>¿Cuál es la diferencia entre puntos de conexión de servicio y puntos de conexión privados?
- Al usar puntos de conexión privados, se concede acceso a redes a recursos específicos a través de un servicio determinado que ofrece segmentación granular. Asimismo, el tráfico puede alcanzar el recurso de servicio desde el entorno local sin usar puntos de conexión públicos.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>¿Cuál es la relación entre el servicio Private Link y el punto de conexión privado?
El punto de conexión privado proporciona acceso a varios tipos de recurso de vínculo privado, incluidos los servicios PaaS de Azure y su propio servicio Private Link. Es una relación de uno a varios. Un servicio Private Link puede recibir conexiones de varios puntos de conexión privados. Por otro lado, un punto de conexión privado solo puede conectarse a un servicio Private Link.    

## <a name="private-endpoint"></a>Punto de conexión privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>¿Puedo crear varios puntos de conexión privados en la misma red virtual? ¿Se pueden conectar a diferentes servicios? 
Sí. Puede tener varios puntos de conexión privados en la misma red virtual o subred. Pueden conectarse a diferentes servicios.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>¿Necesito una subred dedicada para los puntos de conexión privados? 
No. No necesita una subred dedicada para los puntos de conexión privados. Puede elegir una dirección IP de punto de conexión privado desde cualquier subred de la red virtual donde se implemente su servicio.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>¿Puede el punto de conexión privado conectarse al servicio Private Link a través de inquilinos de Azure Active Directory? 
Sí. Los puntos de conexión privados pueden conectarse a servicios Private Link o PaaS de Azure a través de inquilinos de AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>¿Puede el punto de conexión privado conectarse a recursos de PaaS a través de las regiones de Azure?
Sí. Los puntos de conexión privados pueden conectarse a recursos de PaaS de Azure a través de las regiones de Azure.

## <a name="private-link-service"></a>Servicio Private Link
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>¿Cuáles son los requisitos previos para crear un servicio Private Link? 
Sus back-ends de servicio deben estar en una red virtual y detrás de una instancia de Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>¿Cómo puedo escalar mi servicio Private Link? 
Puede escalar su servicio Private Link de distintas formas: 
- Agregando máquinas virtuales de back-end al grupo situado detrás de su instancia de Standard Load Balancer. 
- Agregando una dirección IP al servicio Private Link. Permitimos hasta ocho direcciones IP por servicio Private Link.  
- Agregando un nuevo servicio Private Link a Standard Load Balancer. Permitimos hasta ocho servicios Private Link por equilibrador de carga.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>¿Puedo conectar mi servicio a varios puntos de conexión privados?
Sí. Un servicio Private Link puede recibir conexiones de varios puntos de conexión privados. Sin embargo, un punto de conexión privado solo puede conectarse a un servicio Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>¿Cómo debo controlar la exposición de mi servicio Private Link?
Puede controlar la exposición mediante la configuración de visibilidad del servicio Private Link. La visibilidad admite tres configuraciones:

- **Ninguno**: solo las suscripciones con acceso RBAC pueden localizar el servicio. 
- **Restrictivo**: solo las suscripciones que están en la lista blanca y con acceso RBAC pueden localizar el servicio. 
- **Todo**: todos pueden localizar el servicio. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>¿Puedo crear un servicio Private Link con un equilibrador de carga básico? 
No. No se admite ningún servicio Private Link a través de un equilibrador de carga básico.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>¿Es necesaria una subred dedicada para el servicio Private Link? 
No. El servicio Private Link no requiere una subred dedicada. Puede elegir cualquier subred de su red virtual donde se implemente su servicio.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Soy un proveedor de servicios que usa Azure Private Link. ¿Debo asegurarme de que todos mis clientes tienen espacio de IP único y no se superpone con mi espacio de IP? 
No. Azure Private Link proporciona esta funcionalidad automáticamente. Por tanto, no se le exige tener un espacio de direcciones que no se superponga con el espacio de direcciones de su cliente. 

##  <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre [Azure Private Link](private-link-overview.md)
