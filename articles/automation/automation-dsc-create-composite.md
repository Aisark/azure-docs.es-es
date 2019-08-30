---
title: 'Conversión de configuraciones en recursos compuestos para la configuración del estado: Azure Automation'
description: Aprenda a convertir configuraciones en recursos compuestos para la configuración del estado en Azure Automation.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d830b8e27bb6f66a533b8106cbec53eeca4ca139
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970713"
---
# <a name="convert-configurations-to-composite-resources"></a>Conversión de configuraciones en recursos compuestos

> Se aplica a: Windows PowerShell 5.1

Una vez que empiece a crear configuraciones, podrá crear rápidamente "escenarios" que administren grupos de valores.
Algunos ejemplos serían:

- crear un servidor web
- crear un servidor de nombres de dominio
- crear un servidor de SharePoint
- configurar un clúster de SQL
- administrar la configuración del firewall
- administrar la configuración de la contraseña

Si desea compartir este trabajo con otros usuarios, la mejor opción es empaquetar la configuración en forma de [recurso compuesto](/powershell/dsc/resources/authoringresourcecomposite).
La primera vez que se crean recursos compuestos la tarea puede ser sobrecogedora.

> [!NOTE]
> En este artículo se hace referencia a una solución que se mantiene en la comunidad de código abierto.
> La compatibilidad solo está disponible en forma de colaboración en GitHub, no con Microsoft.

## <a name="community-project-compositeresource"></a>Proyecto de la comunidad: CompositeResource

Para resolver este reto, se ha creado una solución mantenida por la comunidad llamada [CompositeResource](https://github.com/microsoft/compositeresource).

CompositeResource automatiza el proceso de creación de módulos a partir de su configuración.
Para empezar, [use el operador punto](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) en el script de configuración de la estación de trabajo (o servidor de compilación) para que se cargue en memoria.
A continuación, en lugar de ejecutar la configuración para generar un archivo MOF, use la función que proporciona el módulo CompositeResource para automatizar una conversión.
El cmdlet cargará el contenido de la configuración, obtendrá la lista de parámetros y generará un módulo con todo lo necesario.

Una vez que haya generado un módulo, puede usar una versión superior y agregar notas de la versión cada vez que realice cambios y la publique en su [repositorio de PowerShellGet](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Una vez que haya creado un módulo de recursos compuestos que contenga la configuración (o varias configuraciones), puede usarlos en la [creación de composiciones](/azure/automation/compose-configurationwithcompositeresources) en Azure, o bien agregarlos a [los scripts de configuración de DSC](/powershell/dsc/configurations/configurations) para generar archivos MOF y [cargar los archivos MOF en Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
A continuación, registre los servidores desde una [ubicación local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) o [en Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para extraer las configuraciones.
La actualización más reciente del proyecto también ha publicado [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) para que Azure Automation automatice el proceso de importación de configuraciones desde la Galería de PowerShell.

Para probar la automatización de la creación de recursos compuestos para DSC, visite la [Galería de PowerShell](https://www.powershellgallery.com/packages/compositeresource/) y descargue la solución, o bien haga clic en "Project Site" (Sitio del proyecto) para ver la [documentación](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Pasos siguientes

- [Windows PowerShell Desired State Configuration Overview (Información general de la configuración de estado deseado de Windows Powershell)](/powershell/dsc/overview/overview)
- [Recursos de DSC](/powershell/dsc/resources/resources)
- [Configuración del administrador de configuración local](/powershell/dsc/managing-nodes/metaconfig)
