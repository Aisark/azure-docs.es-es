---
title: Dirigir actualizaciones mediante colecciones de SCCM en Azure Automation | Update Management
description: Este artículo está pensado para ayudarle a configurar System Center Configuration Manager con esta solución para administrar las actualizaciones de los equipos administrado con SCCM.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddc055be45eae923be31d7d11621c9427660bf74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499876"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integración de System Center Configuration Manager con Update Management

Los clientes que han invertido en System Center Configuration Manager para administrar equipos, servidores y dispositivos móviles también dependen de su solidez y nivel de madurez para administrar actualizaciones de software como parte de su ciclo de Administración de actualizaciones de software (SUM).

Puede crear informes sobre los servidores Windows administrados y actualizarlos. Para hacerlo, cree y preconfigure implementaciones de actualizaciones de software en Configuration Manager y obtenga el estado detallado de las implementaciones de actualizaciones completadas mediante la [solución Update Management](automation-update-management.md). Si utiliza Configuration Manager para los informes de cumplimiento de las actualizaciones, pero no para administrar implementaciones de actualizaciones con los servidores de Windows, puede seguir notificando a Configuration Manager mientras se administran las actualizaciones de seguridad con la solución Update Management.

## <a name="prerequisites"></a>Requisitos previos

* Debe tener la [solución Update Management](automation-update-management.md) agregada a su cuenta de Automation.
* Los servidores de Windows que, actualmente, administra System Center Configuration Manager también deben informar al área de trabajo de Log Analytics que también tenga habilitada la solución Update Management.
* Esta característica se habilita en la rama actual de System Center Configuration Manager, versión 1606 y posteriores. Para integrar el sitio de administración central de Configuration Manager o un sitio primario independiente con los registros de Azure Monitor e importar las recopilaciones, revise [conectar Configuration Manager a Azure Monitor registra](../azure-monitor/platform/collect-sccm.md).  
* Los agentes de Windows deben estar configurados para comunicarse con un servidor de Windows Server Update Services (WSUS) o tener acceso a Microsoft Update si no reciben actualizaciones de seguridad de Configuration Manager.   

La forma de administrar los clientes hospedados en IaaS de Azure con el entorno existente de Configuration Manager depende, principalmente, de la conexión que tiene entre centros de datos de Azure y su infraestructura. Esta conexión afecta a los cambios de diseño que pueda necesitar hacer en la infraestructura de Configuration Manager y a los costos derivados de los cambios necesarios. Para comprender qué consideraciones de planeación debe evaluar antes de continuar, revise [Configuration Manager en Azure - Preguntas más frecuentes](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuración

### <a name="manage-software-updates-from-configuration-manager"></a>Administrar actualizaciones de software de Configuration Manager 

Siga estos pasos si va a continuar administrando las implementaciones de actualizaciones desde Configuration Manager. Azure Automation se conecta a Configuration Manager para aplicar actualizaciones a los equipos cliente conectados al área de trabajo de Log Analytics. El contenido de la actualización está disponible en la caché del equipo cliente como si la implementación se administrara mediante Configuration Manager.

1. Cree una implementación de actualizaciones de software desde el sitio de nivel superior de la jerarquía de Configuration Manager mediante el proceso descrito en el [proceso de implementación de actualizaciones de software](/sccm/sum/deploy-use/deploy-software-updates). La única opción que se debe configurar de forma distinta de una implementación estándar es la opción **No instalar actualizaciones de software** para controlar el comportamiento de descarga del paquete de implementación. La solución Update Management administra este comportamiento mediante la creación de una implementación de actualizaciones programada en el paso siguiente.

1. En Azure Automation, seleccione **Update Management**. Cree una nueva implementación según los pasos descritos en [Creación de una implementación de actualizaciones](automation-tutorial-update-management.md#schedule-an-update-deployment) y seleccione **Grupos importados** en la lista desplegable **Tipo** para seleccionar la colección adecuada de Configuration Manager. Tenga en cuenta los siguientes puntos importantes: a. Si una ventana de mantenimiento se define en la colección de dispositivos de Configuration Manager, los miembros de la colección respetan sus condiciones, en lugar de la configuración **Duración** definida en la implementación programada.
    b. Los miembros de la colección de destino deben tener una conexión a Internet (ya sea directamente, a través de un servidor proxy o a través de la puerta de enlace de Log Analytics).

Después de completar la implementación de actualizaciones mediante Azure Automation, los equipos de destino que son miembros del grupo de equipos seleccionado instalarán las actualizaciones a la hora programada desde la memoria caché del cliente local. También puede [ver el estado de implementación de actualizaciones](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para supervisar los resultados de la implementación.

### <a name="manage-software-updates-from-azure-automation"></a>Administrar actualizaciones de software desde Azure Automation

Para administrar actualizaciones para VM Windows Server que son clientes de Configuration Manager, debe configurar la directiva de cliente para deshabilitar la característica Administración de actualizaciones de software para todos los clientes que administra esta solución. De forma predeterminada, la configuración de cliente está dirigida a todos los dispositivos de la jerarquía. Para obtener más información sobre esta configuración de directiva y cómo configurarla, revise [Cómo configurar el cliente en System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings).

Después de aplicar este cambio de configuración, cree una nueva implementación según los pasos descritos en [Creación de una implementación de actualizaciones](automation-tutorial-update-management.md#schedule-an-update-deployment) y seleccione **Grupos importados** en la lista desplegable **Tipo** para seleccionar la colección adecuada de Configuration Manager.

## <a name="next-steps"></a>Pasos siguientes

