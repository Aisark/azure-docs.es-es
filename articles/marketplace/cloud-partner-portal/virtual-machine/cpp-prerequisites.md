---
title: Requisitos previos de la máquina virtual de Microsoft Azure | Azure Marketplace
description: Lista de requisitos previos necesarios para publicar una oferta de máquina virtual en Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 258d21eae5af50b5dc0bed6887618e2999cae45a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257396"
---
# <a name="virtual-machine-prerequisites"></a>Requisitos previos de las máquinas virtuales

En este artículo se muestra tanto las técnicas y requisitos empresariales que se deben cumplir antes de pueden publicar una oferta de máquina virtual a la [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Si aún no lo ha hecho, revise el [Guía de publicación de la oferta de máquina Virtual](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

Los requisitos previos técnicos para la publicación de una solución de máquina virtual (VM) son sencillos:

- Debe tener una cuenta de Azure activa. Si no tiene ninguna, puede registrarse en el [sitio de Microsoft Azure](https://azure.microsoft.com).  
- Debe tener un entorno configurado para admitir el desarrollo de máquinas virtuales Windows o Linux.  Para obtener más información, vea el sitio de documentación de máquinas virtuales asociado:
    - [Documentación sobre máquinas virtuales Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentación sobre máquinas virtuales Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos empresariales

Los requisitos empresariales incluyen obligaciones contractuales, de procedimientos y legales: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Debe ser publicador de Cloud Marketplace registrado.  Si aún no está registrado, siga los pasos del artículo [Conviértase en anunciante de Cloud Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Debe usar la misma cuenta de registro de Microsoft Developer Center para iniciar sesión en [Cloud Partner Portal](https://cloudpartner.azure.com).
    > Solo debe tener una cuenta Microsoft para sus ofertas de Azure Marketplace. Esta no debe ser específica para servicios u ofertas individuales.
    
- Su empresa (o sus subsidiarias) deben encontrarse en una venta-de-país/región admitido por Azure Marketplace.  Para obtener una lista actual de estos países o regiones, consulte [directivas de participación de Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- El producto debe contar con una licencia que sea compatible con los modelos de facturación admitidos por Azure Marketplace.  Para obtener más información, vea [Opciones de facturación de Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Usted es el responsable de poner el soporte técnico a disposición de los clientes de forma comercialmente razonable. Puede ser gratuito, de pago o a través de métodos de la comunidad.
- Asimismo, es responsable de la concesión de licencias para su software y las dependencias de software de terceros.
- Debe proporcionar contenido que cumpla los criterios para que la oferta se publique en Azure Marketplace y en Azure Portal. <!-- TD: Meaning/links? -->
- Debe aceptar los términos de las [Directivas de Participación de Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) y del Acuerdo del publicador.
- Debe cumplir la [términos de uso de Microsoft Azure sitio Web](https://azure.microsoft.com/support/legal/website-terms-of-use/), [declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement), y [programa contrato de Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Pasos siguientes

Una vez cumplidos estos requisitos previos, puede [crear su oferta de VM](./cpp-create-offer.md).
