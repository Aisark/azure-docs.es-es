---
title: Distribuciones de Linux aprobadas en Azure | Microsoft Docs
description: Aprenda sobre Linux en las distribuciones aprobadas de Azure, incluyendo instrucciones para Ubuntu, CentOS, Oracle y SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/01/2019
ms.author: szark
ms.openlocfilehash: db10c95c982132c41193ed1cccfdcdca6f60ba5e
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993082"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Con el respaldo de las distribuciones de Linux en Azure
Los asociados ofrecen imágenes de Linux en Azure Marketplace. Trabajamos con distintas comunidades de Linux para agregar aún más tipos a la lista de distribuciones aprobadas. Mientras tanto, para las distribuciones que no están disponibles en Marketplace, siempre puede aportar su propia imagen de Linux siguiendo las directrices dadas en [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distribuciones y versiones admitidas
En la tabla siguiente se enumeran las distribuciones y versiones de Linux que se admiten en Azure. Consulte [soporte para imágenes de Linux en Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obtener más información sobre la compatibilidad con Linux y tecnología de código abierto en Azure.

Los controladores de los Servicios de integración de Linux (LIS) para Hyper-V y Azure son módulos del kernel que Microsoft aporta directamente al kernel de Linux del canal de subida.  Algunos controladores LIS están integrados de forma predeterminada en el kernel de la distribución. Hay distribuciones anteriores basadas en Red Hat Enterprise (RHEL)/CentOS disponibles como descarga independiente en [Linux Integration Services versión 4.2 para Hyper- V y Azure](https://www.microsoft.com/download/details.aspx?id=55106). Vea [Requisitos para el kernel de Linux](create-upload-generic.md#linux-kernel-requirements) para obtener más información sobre los controladores de LIS.

El Agente de Linux de Azure ya está preinstalado en las imágenes de Azure Marketplace y generalmente está disponible en el repositorio de paquetes de la distribución. El código fuente se puede encontrar en [GitHub](https://github.com/azure/walinuxagent).

  
| Distribución | `Version` | Controladores | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3: [Descarga de LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 o superior: En kernel |Paquete: en el [repositorio](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/), en "WALinuxAgent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |En kernel |Código fuente: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |En kernel |Paquete: en el repositorio, en "waagent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |En kernel |Paquete: en el repositorio, en "WALinuxAgent" <br/>Código fuente: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+ |En kernel |Paquete: en el repositorio, en "WALinuxAgent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES para SAP<br>11 SP4<br>12 SP1+<br>15|En kernel |Paquete:<p> para 11 en el repositorio [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>para 12, que se incluye en el módulo "Nube pública" en "python-azure-agent"<br/>Código fuente: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |En kernel |Paquete: en el repositorio [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools), en "python-azure-agent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |En kernel |Paquete: en el repositorio, en "walinuxagent" <br/>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  información sobre el soporte extendido de Ubuntu 12.04 y 14.04 puede encontrarse aquí: [Ubuntu extendidos de mantenimiento de seguridad](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadencia de actualización de imagen
Azure requiere que los publicadores de las distribuciones de Linux aprobadas actualizan con regularidad sus imágenes en Azure Marketplace con las últimas revisiones y revisiones de seguridad, en una cadencia más rápida o trimestral. Las imágenes actualizadas en Azure Marketplace están disponibles automáticamente a los clientes como las nuevas versiones de una SKU de imagen. Obtener más información acerca de cómo encontrar imágenes de Linux: [Buscar imágenes de VM de Linux en Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Vínculos adicionales
 - [Ciclo de vida de imagen SUSE en la nube pública](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Kernels optimizado de Azure

Azure trabaja en estrecha colaboración con diversas distribuciones de Linux aprobadas para optimizar las imágenes que publican en Azure Marketplace. Un aspecto de esta colaboración es el desarrollo de los kernels de Linux "ajustados" que está optimizado para la plataforma Azure y se entregan como componentes totalmente compatibles de la distribución de Linux. Los kernels ajustados Azure incorporan nuevas características y mejoras de rendimiento y a un ritmo más rápido (normalmente trimestral) en comparación con el valor predeterminado o kernels genéricos que están disponibles en la distribución.

En la mayoría de los casos, encontrará estos kernels preinstalados en las imágenes predeterminadas en Azure Marketplace y los clientes de Azure por lo que obtendrá inmediatamente la ventaja de estos kernels optimizados. Para obtener más información acerca de estos kernels optimizado de Azure se puede encontrar en los siguientes vínculos:

 - CentOS ajustados Azure Kernel - disponible a través de la virtualización de CentOS SIG - [obtener más información](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Kernel en la nube - disponible con la imagen de Debian 9 "backports" en Azure - y 10 Debian [más información](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - Kernel SLES Azure ajustados - [obtener más información](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Kernel de Ubuntu ajustados Azure - [obtener más información](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Asociados

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

En el sitio web de CoreOS:

*CoreOS está diseñado para la seguridad, coherencia y confiabilidad. En lugar de instalar paquetes a través de yum o apt, CoreOS utiliza contenedores de Linux para administrar los servicios en un nivel superior de abstracción. Un código y todas las dependencias de un servicio único se empaquetan dentro de un contenedor que se puede ejecutar en uno o varios equipos de CoreOS.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ es una empresa de servicios y consultoría independiente que se especializa en el desarrollo y la implementación de soluciones profesionales mediante el uso de software gratuito. Como especialistas en código abierto líderes en el sector, Credative goza de reconocimiento en el ámbito internacional y numerosos departamentos de TI recurren a su soporte técnico. Actualmente, Credativ está preparando con Microsoft imágenes de Debian correspondientes a Debian 8 (Jessie) y Debian antes de 7 (Wheezy). Ambas imágenes están diseñadas especialmente para ejecutarse en Azure y pueden administrarse fácilmente a través de la plataforma. Credativ también será compatible con el mantenimiento y la actualización a largo plazo de las imágenes de Debian para Azure a través de sus centros de soporte técnico de código abierto.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La estrategia de Oracle consiste en ofrecer una amplia gama de soluciones para nubes públicas y privadas. Esta estrategia ofrece e los clientes posibilidad de elección y flexibilidad en la forma en que distribuyen software de Oracle en nubes de Oracle y en otras nubes. La asociación de Oracle y Microsoft permite que los clientes implementen software de Oracle en nubes públicas y privadas de Microsoft con la confianza que brinda la certificación y el respaldo de Oracle.  El compromiso y la inversión de Oracle en las soluciones de nubes públicas y privadas de Oracle siguen intactos.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat, proveedor líder mundial de soluciones de código abierto, ayuda a más del 90 % de las empresas Fortune 500 a resolver desafíos empresariales, a alinear sus estrategias empresariales y de TI, y a prepararse para el futuro de la tecnología. Red Hat hace esto ofreciendo soluciones seguras a través de un modelo de negocio abierto y un modelo de suscripción asequible y predecible.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server en Azure es una plataforma probada que brinda confiabilidad y seguridad de nivel superior para la informática en nube. La versátil plataforma Linux de SUSE se integra de manera fluida con los servicios en la nube de Azure para brindar un entorno de nube fácilmente administrable. Con más de 9200 aplicaciones certificadas de más de 1800 fabricantes de software independiente para SUSE Linux Enterprise Server, SUSE garantiza que las cargas de trabajo que se ejecutan de manera compatible en el centro de datos pueden implementarse con confianza en Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

La ingeniería de Canonical y la gobernanza de comunidad abierta impulsan el éxito de Ubuntu en la informática en la nube, servidor y cliente, incluidos servicios personales en la nube para consumidores. La visión de Canonical de una plataforma unificada y gratuita en Ubuntu, del teléfono a la nube, ofrece una serie de interfaces coherentes para teléfono, tableta, TV y escritorio. Esta visión convierte a Ubuntu en la primera opción para diversas instituciones, desde proveedores en la nube pública hasta los fabricantes de aparatos electrónicos de consumo, y el producto favorito entre tecnólogos individuales.

Con desarrolladores y centros de ingeniería por todo el mundo, Canonical ocupa una posición privilegiada para asociarse con fabricantes de hardware, proveedores de contenido y desarrolladores de software para llevar al mercado las soluciones de Ubuntu para equipos, servidores y dispositivos portátiles.
