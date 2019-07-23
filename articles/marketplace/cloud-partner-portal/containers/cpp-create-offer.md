---
title: Creación de una oferta de contenedor de Azure | Azure Marketplace
description: Publicación de una nueva oferta de contenedor para Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 1a0a2bd9132ba5d018bc5d45699c052d10c30162
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942674"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Creación de una oferta de contenedor con Cloud Partner Portal

En este artículo se explica cómo crear y publicar una entrada de oferta de contenedor para Azure Marketplace. Todas las ofertas aparecen como una entidad propia en Azure Marketplace y se asocian a una o varias SKU.  Una oferta de contenedor se compone de las siguientes agrupaciones de recursos y servicios auxiliares:

|  **Grupo de recursos**   |  **Descripción**  |
|  ---------------   |  ---------------  |
|    SKU            |  La unidad más pequeña que se puede implementar de una oferta. Una única oferta (clase de producto) puede tener varias SKU asociadas a ella. Puede usar SKU para diferenciar entre características admitidas y modelos de facturación. |
|  Marketplace       | Contiene recursos y especificaciones de marketing, legales y de administración de clientes potenciales.  <ul><li> Los recursos de marketing incluyen el nombre de la oferta, la descripción y los logotipos</li> <li> Los recursos legales incluyen una directiva de privacidad, términos de uso y otra documentación legal</li>  <li> La directiva de administración de clientes potenciales permite especificar cómo se controlan los clientes potenciales desde el portal del usuario final de Azure Marketplace.</li> </ul> |
| Soporte técnico            | Contiene información de contacto y directivas de soporte técnico |


## <a name="new-offer-form"></a>Formulario Nueva oferta 

Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com/) y seleccione **+ New offer** (+ Nueva oferta) en la barra de menús de la izquierda. En el menú New Offer (Nueva oferta), seleccione **Containers** (Contenedores) para mostrar el formulario **New Offer** (Nueva oferta) e iniciar el proceso de definir los recursos para una nueva oferta de contenedor.

![Seleccionar la opción Contenedores de Nueva oferta](./media/azure-container-offer.png)

## <a name="next-steps"></a>Pasos siguientes

La página **New Offer** (Nueva oferta) del tipo de oferta de contenedor proporciona un conjunto de pestañas y campos de formulario que se usarán para crear una oferta. En cada uno de los siguientes artículos se explica cómo usar las pestañas para definir los grupos de recursos y servicios auxiliares de la nueva oferta de contenedor.

- [Pestaña Configuración de la oferta](./cpp-offer-settings-tab.md)
- [Pestaña SKU](./cpp-skus-tab.md)
- [Pestaña Marketplace](./cpp-marketplace-tab.md)
- [Pestaña Soporte técnico](./cpp-support-tab.md)
