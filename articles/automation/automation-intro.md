---
title: Introducción a Azure Automation
description: Obtenga información sobre cómo usar Azure Automation para automatizar el ciclo de vida de la infraestructura y las aplicaciones.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventario, runbooks, Python, gráfico
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 3359d99d7e20bbced8950171fa34592fd2612500
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930396"
---
# <a name="an-introduction-to-azure-automation"></a>Introducción a Azure Automation

Azure Automation ofrece un servicio de configuración y de automatización basado en la nube que proporciona una administración coherente en los entornos que se encuentren dentro y fuera de Azure. Se compone de automatización de procesos, administración de actualizaciones y características de configuración. Azure Automation proporciona un control completo durante la implementación, las operaciones y la retirada de las cargas de trabajo y recursos.
En este artículo se ofrece una breve introducción sobre Azure Automation y se responden algunas preguntas habituales. Para obtener más información sobre las distintas funcionalidades, visite los vínculos a lo largo de esta introducción.

## <a name="azure-automation-capabilities"></a>Funcionalidades de Azure Automation

![Funcionalidades de la introducción a Automation](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Automatización de procesos

Azure Automation le ofrece la posibilidad de automatizar las tareas de administración en la nube que requieren mucho tiempo y son frecuentes y propensas a errores. Esta automatización le ayuda a centrarse en el trabajo que agrega valor empresarial. Al reducir los errores y aumentar la eficacia, también contribuye en la reducción de los costos operativos. Puede integrar los servicios de Azure y otros sistemas públicos que son necesarios en la implementación, configuración y administración de los procesos de un extremo a otro. El servicio le permite [crear runbooks](automation-runbook-types.md) gráficamente en PowerShell o Python. Mediante el uso de un trabajo de runbook híbrido, puede unificar la administración mediante la organización de los entornos locales. Los [webhook](automation-webhooks.md) proporcionan una manera de responder a solicitudes y asegurar las operaciones y la entrega continua. Para ello, desencadenan la automatización desde ITSM, DevOps y los sistemas de supervisión.

### <a name="configuration-management"></a>Administración de configuración

[Desired State Configuration](automation-dsc-overview.md) de Azure Automation es una solución basada en la nube para DSC de PowerShell que proporciona los servicios necesarios para los entornos empresariales. Administre los recursos de DSC en Azure Automation y aplique las configuraciones a las máquinas virtuales o físicas desde un servidor de extracción DSC en la nube de Azure. Proporciona informes completos que le informan de eventos importantes, como cuando los nodos se desviaron de la configuración asignada. Puede supervisar y actualizar automáticamente la configuración de máquinas virtuales y físicas, tanto con Windows como con Linux, ya sea en la nube o en la infraestructura local.

Puede obtener un inventario sobre los recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración. Hay disponibles funcionalidades de búsqueda y un informe completo para encontrar información detallada rápidamente que le ayude a entender qué está configurado en el sistema operativo. Puede realizar un seguimiento de los cambios en servicios, demonios, software, el registro y archivos para identificar rápidamente las posibles causas de problemas. Además, DSC puede ayudarle con el diagnóstico y alertarle cuando se produzcan cambios no deseados en su entorno.

### <a name="update-management"></a>Administración de actualizaciones

Actualice los sistemas Windows y Linux en entornos híbridos con Azure Automation. Con ello, obtiene visibilidad del cumplimiento de las actualizaciones en Azure, el entorno local y otras instancias en la nube. Puede crear implementaciones de programación para organizar la instalación de actualizaciones en una ventana de mantenimiento definida. Si una actualización no debería instalarse en una máquina, puede excluirla de una implementación.

### <a name="shared-resources"></a>Recursos compartidos

Azure Automation consta de un conjunto de recursos compartidos que le facilitan la automatización y configuración de los entornos a escala.

* **[Programaciones](automation-schedules.md)** : se usan en el servicio para desencadenar la automatización en momentos predefinidos.
* **[Módulos ](automation-integration-modules.md)** : módulos usados para administrar Azure y otros sistemas. Importe a la cuenta de Automation para Microsoft, recursos de DSC y cmdlets definidos personalizados, de la comunidad o de terceros.
* **[Galería de módulos](automation-runbook-gallery.md)** : integración nativa con la Galería de PowerShell para ver runbooks e importarlos a la cuenta de Automation.
* **[Paquetes de Python 2](python-packages.md)** : incorporación de paquetes de Python 2 a su cuenta de automatización para su uso en runbooks de Python.
* **[Credenciales](automation-credentials.md)** : almacene de forma segura la información confidencial que los runbooks y las configuraciones pueden usar en tiempo de ejecución.
* **[Conexiones](automation-connections.md)** : almacene información de pares de nombre-valor con información común de cuándo se conecta a sistemas en los recursos de conexión. El autor del módulo define las conexiones para usarlas en tiempo de ejecución en runbooks y configuraciones.
* **[Certificados](automation-certificates.md)** : almacénelos y póngalos a disposición en tiempo de ejecución para que puedan usarse para la protección y autenticación de los recursos implementados.
* **[Variables](automation-variables.md)** : proporcionan una manera de almacenar el contenido que puede usarse en runbooks y configuraciones. Puede cambiar los valores sin tener que modificar los runbooks ni las configuraciones que les hagan referencia.

### <a name="source-control-integration"></a>Integración de control de código fuente

Azure Automation tiene la capacidad de [integrar con control de código fuente](source-control-integration.md), que promueve la configuración como código donde los runbooks o las configuraciones pueden insertarse en un sistema de control de código fuente.

### <a name="role-based-access-control"></a>Control de acceso basado en rol

Azure Automation admite el control de acceso basado en rol para controlar el acceso a la cuenta de Automation y sus recursos. Para más información sobre RBAC de configuración en su cuenta de Automation, runbooks y trabajos, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).

### <a name="windows-and-linux"></a>Windows y Linux

Azure Automation está diseñado para trabajar tanto en el entorno de nube híbrida como en Windows y Linux. Le ofrece una manera coherente de automatizar y configurar las cargas de trabajo implementadas y el sistema operativo en el cual se ejecutan.

### <a name="community-gallery"></a>Galería de la comunidad

Examine la [Galería de Automation](automation-runbook-gallery.md) de runbooks y módulos para empezar a integrar y crear rápidamente los procesos desde la Galería de PowerShell y el Centro de scripts de Microsoft.

## <a name="common-scenarios-for-automation"></a>Escenarios comunes para Automation

Azure Automation se administra en el ciclo de vida de la infraestructura y las aplicaciones. Transfiera conocimientos al sistema sobre cómo la organización entrega y mantiene las cargas de trabajo. Cree en lenguajes comunes como PowerShell, Desired State Configuration, Python y runbooks gráficos. Obtenga un inventario completo de los recursos implementados con fines de compatibilidad, creación de informes y cumplimiento. Identifique los cambios que pueden provocar una configuración incorrecta y mejore el cumplimiento operativo.

* **Compilación o implementación de recursos**: implemente máquinas virtuales en un entorno híbrido mediante runbooks y plantillas de Azure Resource Manager. Integre en las herramientas de desarrollo como Jenkins y Azure DevOps.
* **Configuración de máquinas virtuales**: evalúe y configure máquinas Windows y Linux con la configuración deseada para la infraestructura y la aplicación.
* **Supervisión**: identifique los cambios en las máquinas que están ocasionando problemas y corríjalos o remítalos a una instancia superior de los sistemas de administración.
* **Protección**: ponga en cuarentena una VM si se genera una alerta de seguridad. Establezca los requisitos de invitado.
* **Control**: configure el control de acceso basado en rol para equipos. Recupere recursos no utilizados.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Precios para Automation

Puede revisar el precio para Azure Automation en la página de [precios](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Crear una cuenta de automatización](automation-quickstart-create-account.md)

