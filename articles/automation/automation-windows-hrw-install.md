---
title: Hybrid Runbook Worker de Azure Automation en Windows
description: En este artículo encontrará información sobre cómo instalar una instancia de Hybrid Runbook Worker de Azure Automation para ejecutar runbooks en equipos con Windows en su centro de datos local o entorno de nube.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd599fcfe403d64483e6b4db869b93b26f5db754
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480812"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementación de Hybrid Runbook Worker en Windows

La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados. En este artículo, se describe cómo instalar Hybrid Runbook Worker en una máquina Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Windows

Para instalar y configurar una instancia de Hybrid Runbook Worker en Windows, se pueden usar dos métodos. El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso para configurar un equipo Windows. El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente.

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con Desired State Configuration (DSC), debe agregarlos como nodos de DSC.

Estos son los requisitos mínimos de Hybrid Runbook Worker en Windows:

* Windows Server 2012 o posterior.
* Windows PowerShell 5.1 o posterior ([descargar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 o posterior.
* Dos núcleos
* 4 GB de RAM
* Puerto 443 (saliente)

Para obtener más requisitos de redes adicionales para Hybrid Runbook Worker, consulte el apartado sobre [configuración de la red](automation-hybrid-runbook-worker.md#network-planning).

Para obtener más información sobre la incorporación de servidores para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).
Si habilita la [solución Update Management](../operations-management-suite/oms-solution-update-management.md), los equipos Windows conectados al área de trabajo de Azure Log Analytics se configurarán automáticamente como una instancia de Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución. Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation. 

El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker. Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.

Después de implementar correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.

### <a name="automated-deployment"></a>Implementación automatizada

Realice los pasos siguientes para automatizar la instalación y configuración del rol Hybrid Worker en Windows:

1. Descargue el script New-OnPremiseHybridWorker.ps1 de la [Galería de PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) directamente desde el equipo que ejecuta el rol Hybrid Runbook Worker o desde otro equipo de su entorno. Copie el script en el trabajo.

   El script New-OnPremiseHybridWorker.ps1 requiere los siguientes parámetros durante la ejecución:

   * *AutomationAccountName* (obligatorio): el nombre de la cuenta de Automation.
   * *AAResourceGroupName* (obligatorio): el nombre del grupo de recursos que está asociado a su cuenta de Automation.
   * *OMSResourceGroupName* (opcional): el nombre del grupo de recursos para el área de trabajo de Log Analytics. Si no se especifica este grupo de recursos, se utiliza *AAResourceGroupName*.
   * *HybridGroupName* (obligatorio): el nombre de un grupo de Hybrid Runbook Worker que se especifica como destino para los runbooks que admiten este escenario.
   * *SubscriptionID* (obligatorio): el identificador de la suscripción de Azure en el que se encuentra su cuenta de Automation.
   * *WorkspaceName* (opcional): Nombre del área de trabajo de Log Analytics. Si no tiene un área de trabajo de Log Analytics, el script creará y configurará una.

   > [!NOTE]
   > Al habilitar las soluciones, solo en determinadas regiones se puede vincular un área de trabajo de Log Analytics y una cuenta de Automation.
   >
   > Para obtener una lista de los pares de asignación que se admiten, consulte [Asignación de región para la cuenta de Automation y el área de trabajo de Log Analytics](how-to/region-mappings.md).

2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** en modo de administrador.
3. Desde el shell de línea de comandos de PowerShell, vaya a la carpeta que contiene el script que ha descargado. Cambie los valores de los parámetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* y *-WorkspaceName*. Después, ejecute el script.

     > [!NOTE]
     > Se le solicitará que se autentique en Azure después de ejecutar el script. *Debe* iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Se le pedirá que acepte la instalación de NuGet y que se autentique con sus credenciales de Azure.

5. Una vez finalizado el script, la página **Grupos de Hybrid Worker** muestra el nuevo grupo y el número de miembros. Si se trata de un grupo existente, se incrementa el número de miembros. Puede seleccionar el grupo de la lista en la página **Grupos de Hybrid Worker** y seleccionar el icono **Hybrid Workers**. En la página **Hybrid Workers**, verá que aparece cada miembro del grupo.

### <a name="manual-deployment"></a>Implementación manual

Realice los dos primeros pasos una vez para su entorno de Automation y después repita los pasos restantes en cada equipo de trabajo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Creación de un área de trabajo de Log Analytics

Si todavía no tiene un área de trabajo de Log Analytics, puede crear una mediante las instrucciones que se indican en [Administración del área de trabajo](../azure-monitor/platform/manage-access.md). Si cuenta con un área de trabajo existente, puede usarla.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adición de la solución de Automation al área de trabajo de Log Analytics

La solución de registros de Azure Monitor de Automation agrega funcionalidad a Azure Automation, incluida la compatibilidad con Hybrid Runbook Worker. Cuando se agrega la solución al área de trabajo, se insertan automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Para agregar la solución de registros de Azure Monitor de **Automation** al área de trabajo, ejecute la instancia de PowerShell siguiente.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalación de Microsoft Monitoring Agent

Microsoft Monitoring Agent conecta los equipos a los registros de Azure Monitor. Cuando instala el agente en el equipo local y se conecte al espacio de trabajo, se descargan automáticamente los componentes necesarios para Hybrid Runbook Worker.

Para instalar el agente en el equipo local, siga las instrucciones que se encuentran en [Connect Windows computers to Azure Monitor logs](../log-analytics/log-analytics-windows-agent.md) (Conexión de equipos Windows a los registros de Azure Monitor). Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se ha conectado correctamente a los registros de Azure Monitor, se mostrará en la pestaña **Orígenes conectados** de la página **Configuración** de Log Analytics. Puede comprobar que el agente ha descargado correctamente la solución Automation cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar la versión de Hybrid Runbook Worker, puede ir a C:\Archivos de programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ y examinar la subcarpeta \\*versión*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalación del entorno de runbook y conexión con Azure Automation

Cuando se agrega un agente a los registros de Azure Monitor, la solución de Automation inserta el módulo **HybridRegistration** de PowerShell, que contiene el cmdlet **Add-HybridRunbookWorker**. Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Azure Automation.

Abra una sesión de PowerShell en modo de Administrador y ejecute los comandos siguientes para importar el módulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Puede obtener la información necesaria para este cmdlet en la página **Administrar claves** de Azure Portal. Para abrir esta página, seleccione la opción **Claves** en la página **Configuración** de su cuenta de Automation.

![Página Administrar claves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** es el nombre del grupo de Hybrid Runbook Worker. Si este grupo ya existe en la cuenta de Automation, se le agregará el equipo actual. Si este grupo no existe, se agrega.
* **EndPoint** es la entrada de **URL** en la página **Administrar claves**.
* **Token** es la entrada de **CLAVE DE ACCESO PRINCIPAL** en la página **Administrar claves**.

Para recibir información detallada sobre la instalación, use el modificador **-Verbose** con **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Instalación de módulos PowerShell

Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Azure Automation. Estos módulos no se implementan automáticamente en equipos locales, por lo que debe instalarlos de forma manual. La excepción es el módulo de Azure que se instala de manera predeterminada y ofrece acceso a los cmdlets para todos los servicios y actividades de Azure para Azure Automation.

Como el propósito principal de la característica Hybrid Runbook Worker es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos. Para más información sobre cómo instalar módulos de Windows PowerShell, consulte [Instalación de módulos](/powershell/scripting/developer/windows-powershell). 

Los módulos instalados deben estar en una ubicación a la que haga referencia la variable de entorno **PSModulePath** para que Hybrid Worker los importe automáticamente. Para más información, consulte [Modifying the PSModulePath Installation Path](/powershell/scripting/developer/windows-powershell) (Modificación de la ruta de instalación PSModulePath).

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o de otros entornos de nube.
* Para ver instrucciones sobre cómo quitar instancias de Hybrid Runbook Worker, consulte [Quitar instancias de Hybrid Runbook Worker de Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para más información sobre cómo solucionar problemas las instancias de Hybrid Runbook Worker, consulte [Troubleshooting Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows) (Solución de problemas de instancias de Hybrid Runbook Worker).
* Para ver pasos adicionales de solución de problemas relacionados con Update Management, consulte [Update Management: solución de problemas](troubleshoot/update-management.md).
