---
title: Conocer los términos de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Conozca los términos de SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481044"
---
# <a name="know-the-terms"></a>Conocer los términos

En la Guía de implementación técnica y arquitectura, se utilizan con frecuencia varias definiciones habituales. Tenga en cuenta los siguientes términos y sus significados:

- **IaaS**: infraestructura como servicio.
- **PaaS**: plataforma como servicio.
- **SaaS**: software como servicio.
- **Componente de SAP**: una aplicación de SAP individual, por ejemplo, ERP Central Component (ECC), Business Warehouse (BW), Solution Manager o Enterprise Portal (EP). Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
- **Entorno de SAP**: uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
- **Infraestructura de SAP**: hace referencia a todos los recursos de SAP de su infraestructura de TI. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
- **Sistema SAP**: la combinación de la capa de DBMS y el nivel de aplicación de, por ejemplo, un sistema de desarrollo de SAP ERP, un sistema de prueba de SAP BW y un sistema de producción de SAP CRM. Las implementaciones de Azure no admiten la división de estas dos capas entre la infraestructura local y la de Azure. Un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Los diferentes sistemas de una infraestructura de SAP pueden implementarse en Azure o de forma local. Por ejemplo, puede implementar los sistemas de prueba y desarrollo de SAP CRM en Azure, mientras que el sistema de producción de SAP CRM se implementa de manera local. En el caso de SAP HANA en Azure (instancias grandes), se supone que hospedará el nivel de aplicación de SAP de los sistemas SAP en máquinas virtuales y la instancia de SAP HANA relacionada en una unidad de la demarcación de SAP HANA en Azure (instancias grandes).
- **Demarcación de instancias grandes**: una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en demarcaciones de instancias grandes en diferentes regiones de Azure. El término relacionado *HANA (instancias grandes)* es la versión abreviada de *SAP HANA en Azure (instancias grandes)* y se usa con frecuencia en esta guía de implementación técnica.
- **Entre locales**: describe un escenario donde se implementan VM en una suscripción de Azure con conexión de sitio a sitio, entre varios sitios o de Azure ExpressRoute entre los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". La razón de la conexión es extender los dominios locales, Azure Active Directory, OpenLDAP local y DNS local en Azure. La infraestructura local se extiende a los recursos de Azure de las suscripciones de Azure. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. 

   Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Se trata del escenario típico en que se implementan la mayoría de los recursos de SAP. Para obtener más información, consulte [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual con una conexión de sitio a sitio mediante Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: un cliente implementado en la demarcación de HANA (instancias grandes) se aísla en un *inquilino*. Un inquilino se aísla en el nivel de redes, almacenamiento y proceso de otros inquilinos. Las unidades de almacenamiento y proceso asignadas a los distintos inquilinos no pueden verse ni comunicarse entre sí en el nivel de la demarcación de HANA (instancias grandes). Un cliente puede elegir que las implementaciones se realicen en diferentes inquilinos. Aún así, no hay ninguna comunicación entre los inquilinos a nivel de la marca de la instancia grande HANA.
- **Categoría de SKU**: para HANA (instancias grandes), se ofrecen las siguientes dos categorías de SKU:
    - **Clase de tipo I**: S72, S72m, S96, S144, S144m, S192, S192m y S192xm
    - **Clase de tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm y S960m


Hay una serie de recursos adicionales sobre cómo implementar una carga de trabajo SAP en la nube. Si planea ejecutar una implementación de SAP HANA en Azure, debe poseer conocimientos y experiencia con los principios de IaaS de Azure y la implementación de cargas de trabajo de SAP en IaaS de Azure. Antes de continuar, consulte [Uso de soluciones de SAP enAzure Virtual Machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información. 

**Pasos siguientes**
- Consulte [Certificación de HLI](hana-certification.md).