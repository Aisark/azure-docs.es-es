---
title: Cómo se integra Cloud Foundry con Azure | Microsoft Docs
description: Describe cómo Cloud Foundry pueden usar los servicios de Azure para mejorar la experiencia de la empresa
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 7cbffdd40e574c7e906a9388b70ca9d32fd84649
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198976"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integración de Cloud Foundry con Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) es una plataforma PaaS que se ejecuta sobre la plataforma IaaS de los proveedores de nube. Ofrece una experiencia de implementación de aplicaciones coherente entre proveedores de nube. También puede integrar con diversos servicios de Azure de nivel empresarial de alta disponibilidad, escalabilidad y el ahorro de costos.
Hay [6 subsistemas de Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), que pueden escalarse de manera flexible en línea, incluidos: El enrutamiento, autenticación, administración del ciclo de vida de aplicaciones, administración de servicios, mensajería y supervisión. Para cada uno de los subsistemas, puede configurar Cloud Foundry para usar corresponsal servicio de Azure. 

![Arquitectura de integración de Cloud Foundry en Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Alta disponibilidad y escalabilidad
### <a name="managed-disk"></a>Disco administrado
Bosh utiliza Azure CPI (interfaz de proveedor de nube) para la creación de disco y las rutinas de eliminación. De forma predeterminada, se usan discos no administrados. Requiere que el cliente cree cuentas de almacenamiento de forma manual y, después, configure las cuentas en archivos de manifiesto de CF. Esto es debido a la limitación del número de discos por cuenta de almacenamiento.
[Disco administrado](https://azure.microsoft.com/services/managed-disks/) está ahora disponible, y ofrece almacenamiento de disco administrado seguro y confiable para las máquinas virtuales. Ya no es necesario que el cliente se ocupe de la cuenta de almacenamiento para escalabilidad y alta disponibilidad. Azure organiza los discos de forma automática. Si es una nueva o una implementación existente, la CPI Azure controlará la creación o migración del disco administrado durante una implementación de CF. Es compatible con PCF 1.11. También puede explorar la [Guía de discos administrados](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) de Cloud Foundry de código abierto como referencia. 
### <a name="availability-zone-"></a>Zona de disponibilidad *
Como plataforma de aplicaciones de nube nativas, Cloud Foundry está diseñado con el [nivel cuatro de alta disponibilidad](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Mientras que los tres primeros niveles de errores de software pueden ser controlados por el propio sistema de CF, los proveedores de nube proporcionan la tolerancia a errores de la plataforma. Los componentes clave de CF se deben proteger con una solución de alta disponibilidad de plataforma de un proveedor de nube. Esto incluye GoRouters, Diego Brains e iconos de base de datos y servicio de CF. De forma predeterminada, se usa un [conjunto de disponibilidad de Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) para la tolerancia a errores entre clústeres en un centro de datos.
La buena noticia es la publicación de [zona de disponibilidad de Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ), que lleva la tolerancia a errores a un nivel superior, con redundancia de baja latencia entre centros de datos.
Zona de disponibilidad de Azure logra la alta disponibilidad mediante la colocación de un conjunto de máquinas virtuales en más de dos centros de datos, y cada conjunto de máquinas virtuales es redundante a otros conjuntos. Si una zona está inactiva, los demás conjuntos siguen activos, aislados de un desastre.
> [!NOTE] 
> Zona de disponibilidad de Azure todavía no está disponible en todas las regiones; compruebe el [anuncio más reciente de la lista de regiones admitidas](https://docs.microsoft.com/azure/availability-zones/az-overview). Para Cloud Foundry de código abierto, consulte las [instrucciones de zona de disponibilidad de Azure para Cloud Foundry de código abierto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Enrutamiento de red
De forma predeterminada, se usa el equilibrador de carga básico de Azure para las solicitudes entrantes de aplicaciones y API de CF, y se reenvían a Gorouters. Los componentes de CF como Diego Brain, MySQL y ERT también pueden usar el equilibrador de carga para equilibrar el tráfico para alta disponibilidad. Azure también proporciona un conjunto de soluciones de equilibrio de carga totalmente administrados. Si desea para la terminación de TLS ("descarga SSL") o por el procesamiento de capa de aplicación de solicitud HTTP/HTTPS, considere la posibilidad de Application Gateway. Para obtener equilibrio de carga de alta disponibilidad y escalabilidad en el nivel 4, considere la posibilidad de un equilibrador de carga estándar.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) ofrece diversas funciones de equilibrio de carga de nivel 7, incluida la descarga de SSL, SSL de un extremo a otro, firewall de aplicaciones web, afinidad de sesión basada en cookies y mucho más. Puede [configurar Application Gateway en Cloud Foundry de código abierto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Para PCF, compruebe las [notas de la versión de PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para la prueba de concepto.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer es un equilibrador de carga de nivel 4. Sirve para distribuir el tráfico entre las instancias de servicios en un conjunto con equilibrio de carga. En la versión estándar se proporcionan [características avanzadas](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) sobre la versión básica. Por ejemplo, 1. El límite máximo del grupo de back-end se eleva de 100 a 1 000 máquinas virtuales.  2. Ahora los puntos de conexión admiten varios conjuntos de disponibilidad en lugar de uno solo.  3. Características adicionales, como puertos de alta disponibilidad, los datos de supervisión más completos y así sucesivamente. Si va a mover a la zona de disponibilidad de Azure, el equilibrador de carga estándar es necesario. Para una implementación nueva, se recomienda comenzar con Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) (Autenticación y cuenta de usuario de Cloud Foundry) es el servicio de administración de identidades central para CF y sus distintos componentes. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. De forma predeterminada, se usa UAA para la autenticación de Cloud Foundry. Como opción avanzada, UAA también es compatible con Azure AD como almacén de usuario externo. Los usuarios de Azure AD pueden acceder a Cloud Foundry con su identidad LDAP, sin una cuenta de Cloud Foundry. Siga estos pasos para [configurar Azure AD para UAA en PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Almacenamiento de datos para el sistema de tiempo de ejecución de Cloud Foundry
Cloud Foundry ofrece una excelente extensibilidad para usar Azure BlobStore o los servicios de Azure MySQL/PostgreSQL para el almacenamiento de sistema de runtime de aplicación.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure BlobStore para el almacén de blobs Cloud Controller de Cloud Foundry
El almacén de blobs Cloud Controller es un almacén de datos críticos para paquetes de compilación, droplets, paquetes y grupos de recursos. De forma predeterminada, se usa el servidor NFS para el almacén de blobs Cloud Controller. Para evitar un único punto de error, use Azure Blob Storage como almacén externo. Consulte la [documentación de Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) para obtener más información, y las [opciones de Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL como base de datos de tiempo de ejecución elástico de Cloud Foundry *
El tiempo de ejecución elástico de CF requiere dos bases de datos del sistema principales:
#### <a name="ccdb"></a>CCDB 
La base de datos de Cloud Controller.  Cloud Controller proporciona puntos de conexión de la API de REST para que los clientes accedan al sistema. CCDB almacena tablas para organizaciones, espacios, servicios, roles de usuario y más para Cloud Controller.
#### <a name="uaadb"></a>UAADB 
La base de datos para la autenticación y la cuenta de usuario. Almacena los datos relacionados con la autenticación de usuarios, por ejemplo nombres de usuario y contraseñas cifradas.

De manera predeterminada, se puede usar una base de datos de sistema local (MySQL). Para alta disponibilidad y escala, aproveche los servicios MySQL o PostgreSQL administrados de Azure. Esta es la instrucción para [habilitar Azure MySQL/PostgreSQL para CCDB, UAADB y otras bases de datos de sistema con Cloud Foundry de código abierto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Open Service Broker
El agente de servicio de Azure ofrece una interfaz coherente para administrar el acceso de la aplicación a los servicios de Azure. El nuevo [proyecto Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) proporciona una forma sencilla y única de proporcionar servicios a las aplicaciones a través de Cloud Foundry, OpenShift y Kubernetes. Vea el [mosaico Azure Open Service Broker for PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) para obtener instrucciones de implementación sobre PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas y registro
El inyector de Azure Log Analytics es un componente de Cloud Foundry que reenvía las métricas desde el [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) a [registros de Azure Monitor](https://azure.microsoft.com/services/log-analytics/). Con el inyector, puede recopilar, ver y analizar las métricas de rendimiento y estado de sistema de CF entre varias implementaciones.
Haga clic en [aquí](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) para aprender a implementar el inyector de Azure Log Analytics en código abierto y entorno de Pivotal Cloud Foundry y, a continuación, obtener acceso a los datos desde Azure Monitor registra la consola. 
> [!NOTE]
> De la versión 2.0 de PCF, BOSH métricas de mantenimiento para las máquinas virtuales se reenvían a Loggregator Firehose de forma predeterminada e integradas en la consola de los registros de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Ahorro de costos
### <a name="cost-saving-for-devtest-environments"></a>Ahorro para entornos de desarrollo y pruebas
#### <a name="b-series-"></a>Serie B: *
Aunque normalmente se recomienda la serie de máquinas virtuales F y D para el entorno de producción de Pivotal Cloud Foundry, la nueva [serie B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) "ampliable" aporta opciones nuevas. Las máquinas virtuales ampliables de la serie B son ideales para cargas de trabajo que no necesitan un rendimiento completo de la CPU de forma continua, como servidores web, pequeñas bases de datos y el desarrollo y entornos de prueba. Estas cargas de trabajo suelen necesitar unos requisitos de rendimiento ampliables. Son 0,012 USD por hora (B1) en comparación con 0,05 USD por hora (F1), vea la lista completa de [tamaños de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) y [precios](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) para obtener más información. 
#### <a name="managed-standard-disk"></a>Disco estándar administrado: 
Para un rendimiento confiable en producción se recomendaban los discos premium.  Con [Disco administrado](https://azure.microsoft.com/services/managed-disks/), el almacenamiento estándar también puede proporcionar una confiabilidad similar, con otro rendimiento. Para cargas de trabajo que no sea sensibles al rendimiento, como desarrollo, prueba o de entorno que no son críticas, los discos administrados estándares ofrecen una opción alternativa con el menor costo.  
### <a name="cost-saving-in-general"></a>Ahorro de costos general 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Ahorro importante de costos de máquinas virtuales con las reservas de Azure: 
Hoy en día, todas las máquinas virtuales de CF se facturan con precios "a petición", incluso cuando los entornos normalmente se mantienen activos de forma indefinida. Ahora puede reservar capacidad de máquina virtual en plazos de 1 o 3 años, y obtener descuentos del 45-65 %. Los descuentos se aplican en el sistema de facturación, sin cambios en el entorno. Para más información, consulte el artículo sobre [cómo funcionan las reservas de Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco administrado premium con tamaños más pequeños: 
Los discos administrados admiten tamaños de disco más pequeños, por ejemplo P4(32 GB) y P6(64 GB) para los discos estándar y premium. Si tiene cargas de trabajo pequeñas, puede ahorrar costos al migrar de discos premium estándar a discos premium administrados.
#### <a name="use-azure-first-party-services"></a>Use Azure primeros servicios de terceros: 
Aprovechar el servicio de primera entidad de Azure reducirá el costo de administración a largo plazo, además de obtener la alta disponibilidad y confiabilidad mencionadas en las secciones anteriores. 

Pivotal ha lanzado un [ERT de superficie mínima](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) para los clientes de PCF, con los componentes colocados en solo cuatro máquinas virtuales, y con un máximo de 2 500 instancias de aplicación en ejecución. La versión de prueba ya está disponible a través de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Pasos siguientes
En primer lugar están disponibles con las características de integración de Azure [Cloud Foundry de código abierto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), antes de que está disponible en Pivotal Cloud Foundry. Las características marcadas con * todavía no están disponibles a través de PCF. Cloud Foundry integración con Azure Stack o bien no se trata en este documento.
Para la compatibilidad de PCF en las características marcadas con *, o bien la integración de Cloud Foundry con Azure Stack, póngase en contacto con el administrador de cuentas de Pivotal y Microsoft para obtener el estado más reciente. 

