---
title: Guía de publicación de ofertas de Containers para Azure Marketplace
description: En este artículo se describen los requisitos para publicar Containers en Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: e81f2b42e725e50473c09622a486586fb6721ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882187"
---
# <a name="containers-offer-publishing-guide"></a>Guía de publicación de ofertas de Containers

Las ofertas de Containers le ayudan a publicar la imagen de contenedor en Azure Marketplace. Use esta guía para comprender los requisitos para esta oferta. 

Estas ofertas de transacción se implementan y facturan a través de Marketplace. La llamada a la acción que el usuario ve es "Obtener ahora".

Utilice el tipo de oferta Contenedor cuando la solución sea una imagen de contenedor de Docker aprovisionada como un servicio de contenedor de Azure basado en Kubernetes.

>[!NOTE]
>Por ejemplo, un servicio de contenedor de Azure basado en Kubernetes, como Azure Kubernetes Service o Azure Container Instances, la opción de los clientes de Azure para un runtime de contenedor basado en Kubernetes.  

Microsoft admite actualmente los modelos de licencia gratuito y BYOL (traiga su propia licencia).

## <a name="containers-offer"></a>Oferta de Containers

| Requisito | Detalles |  
|:--- |:--- |  
| Facturación y medición | Admita el modelo de facturación gratuito o traiga su propia licencia (BYOL). |  
| Imagen compilada desde Dockerfile | Las imágenes de contenedor se deben basar en la especificación de imagen de Docker y se deben compilar a partir de un archivo de Docker.<ul> <li>Para obtener más información sobre la creación de imágenes de Docker, visite la sección Usage (Uso) de [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedaje en ACR | Las imágenes de contenedor se deben hospedar en un repositorio de Azure Container Registry (ACR).<ul> <li>Para obtener más información sobre cómo usar ACR, visite el inicio rápido: Creación de un registro de contenedor con Azure Portal ubicada en [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Etiquetado de imágenes | Las imágenes de contenedor deben contener al menos 1 etiqueta (número máximo de etiquetas: 16).<ul> <li>Para obtener más información sobre cómo etiquetar una imagen, visite la página de la etiqueta de Docker en [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, 

- [Regístrese](https://azuremarketplace.microsoft.com/sell) en Marketplace.

Si está registrado y está creando una oferta nueva o trabajando en una existente,

- [Inicie sesión en Cloud Partner Portal](https://cloudpartner.azure.com) para crear o completar la oferta
- Consulte [Contenedores](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para obtener más información.
