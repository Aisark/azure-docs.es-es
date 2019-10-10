---
title: 'Configuración basada en STIG para usar en la configuración de estado: Azure Automation'
description: Aprenda sobre las configuraciones basadas en STIG para la configuración de estado en Azure Automation.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243529"
---
# <a name="configuration-based-on-stig"></a>Configuración basada en STIG

> Se aplica a: Windows PowerShell 5.1

Crear contenido de configuración por primera vez puede ser un reto.
En muchos casos, el objetivo es automatizar la configuración de los servidores después de una "línea de base" que, con suerte, se alinea con una recomendación del sector.

> [!NOTE]
> En este artículo se hace referencia a una solución que se mantiene en la comunidad de código abierto.
> La compatibilidad solo está disponible en forma de colaboración en GitHub, no con Microsoft.

## <a name="community-project-powerstig"></a>Proyecto de la comunidad: PowerSTIG

Un proyecto de la comunidad denominado [PowerSTIG](https://github.com/microsoft/powerstig) pretende resolver este problema mediante la generación de contenido DSC basado en la [información pública](https://public.cyber.mil/stigs/) proporcionada sobre la guía de implementación técnica de seguridad (STIG, por sus siglas en inglés).

Tratar con las líneas de base es más complicado de lo que parece.
Muchas organizaciones necesitan [documentar las excepciones](https://github.com/microsoft/powerstig#powerstigdata) a las reglas y administrar los datos a escala.
Para hacer frente a este problema, PowerSTIG proporciona [recursos compuestos](https://github.com/microsoft/powerstig#powerstigdsc) para abordar cada área de la configuración, en lugar de intentar abordar toda la configuración de un archivo grande.

Una vez que se han generado las configuraciones, puede usar los [scripts de configuración de DSC](/powershell/scripting/dsc/configurations/configurations) para generar archivos MOF y [cargarlos en Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
A continuación, registre los servidores desde una [ubicación local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) o [en Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para extraer las configuraciones.

Para probar PowerSTIG, visite la [Galería de PowerShell](http://www.powershellgallery.com) y descargue la solución o haga clic en "Project Site" (Sitio del proyecto) para ver la [documentación](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Pasos siguientes

- [Windows PowerShell Desired State Configuration Overview (Información general de la configuración de estado deseado de Windows Powershell)](/powershell/dsc/overview/overview)
- [Recursos de DSC](/powershell/dsc/resources/resources)
- [Configuración del administrador de configuración local](/powershell/dsc/managing-nodes/metaconfig)
