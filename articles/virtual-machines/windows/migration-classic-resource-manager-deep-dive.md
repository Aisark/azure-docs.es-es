---
title: Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager | Microsoft Docs
description: En este artículo se realiza una profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 1729a19c1d1fe6b7e46f68fd3bc515a9194efe03
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723191"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager
Profundicemos en la migración desde el modelo de implementación clásica de Azure hasta el modelo de implementación de Azure Resource Manager. Nos centramos en los recursos en un nivel de recursos y función que le ayudarán a comprender cómo la Plataforma de Azure migra los recursos entre los dos modelos de implementación. Para obtener más información, lea el artículo de anuncio de servicio: [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Información general sobre la migración compatible con la plataforma de recursos de IaaS desde el modelo de implementación clásica a Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Planificación de la migración de recursos de IaaS del modelo clásico a Azure Resource Manager)
* [Migración de recursos de IaaS de la implementación clásica a Resource Manager con Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migración de VPN Gateway (clásico) a Resource Manager](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migración de circuitos ExpressRoute y las redes virtuales asociadas del modelo de implementación clásica a Resource Manager](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Herramientas de la comunidad para ayudar con la migración de recursos de IaaS del modelo de implementación clásica a Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Revisión de los errores más comunes en la migración](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Revisión de las preguntas más frecuentes acerca de cómo migrar recursos de IaaS de la versión clásica a Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
