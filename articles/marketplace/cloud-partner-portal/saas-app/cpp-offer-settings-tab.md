---
title: Configuración de la oferta de aplicación de SaaS Azure | Azure Marketplace
description: Configure los valores de la oferta de aplicación SaaS en Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6903226ecfe1478b340e390c783c4e57af778f3e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943514"
---
# <a name="saas-application-offer-settings-tab"></a>Pestaña Configuración de la oferta de la aplicación SaaS

En este artículo se describe cómo establecer la configuración de la oferta.

La página **Aplicación SaaS > Nueva oferta** se abre con el foco sobre la pestaña **Configuración de la oferta**. 

Use la pestaña Configuración de la oferta para configurar la **Identidad de la oferta**, como se muestra en la siguiente captura de pantalla. Un asterisco (*) junto al nombre del campo indica que es obligatorio.

![Pestaña Configuración de la oferta](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Valor Identidad de la oferta

En Identidad de la oferta, debe proporcionar información para los campos descritos en la tabla siguiente. Los campos obligatorios se indican con un asterisco (*).

|    Nombre del campo      |    DESCRIPCIÓN    |
|  ---------------   |  ---------------  |
|  **Id. de oferta\***    |  Identificador único de la oferta en un perfil del anunciante. Este identificador se mostrará en las direcciones URL de producto y en los informes de facturación. Puede contener solo caracteres alfanuméricos en minúscula o guiones (-). El identificador, que tiene 50 caracteres como máximo, no puede terminar con un guion. Tenga en cuenta que este campo queda bloqueado en cuanto se lanza una oferta. Por ejemplo, si un anunciante llamado Contoso publica una oferta con el identificador de oferta sample-vm, este aparecerá en Azure Marketplace como: https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  **Id. de publicador\***    |  El identificador del anunciante es su identificador único en Marketplace. Se deben asociar todas las ofertas al identificador del anunciante. Este identificador no se puede modificar una vez que se haya guardado la oferta.                |
|  **Nombre\***      |   Se trata del nombre para mostrar de la oferta. Este es el nombre que se mostrará en Azure Marketplace y en Azure Portal. Puede tener un máximo de 50 caracteres. Incluya un nombre de marca que identifique el producto. No incluya aquí el nombre de su empresa a menos sea así como se comercializa. Si comercializa esta oferta en su propio sitio web, asegúrese de que el nombre sea exactamente el mismo que aparece en el sitio web.               |
|  |  |

Seleccione **Guardar** para guardar lo que ha hecho hasta ahora.

## <a name="next-steps"></a>Pasos siguientes

[Pestaña Información técnica](./cpp-technical-info-tab.md)
