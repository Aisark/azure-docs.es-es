---
title: Información general del agente Connected Machine de Windows
description: En este artículo se proporciona una descripción detallada del agente de servidores habilitados para Azure Arc disponible, que admite la supervisión de máquinas virtuales hospedadas en entornos híbridos.
ms.date: 12/15/2020
ms.topic: conceptual
ms.openlocfilehash: 531041b7d7439dd2a48fa9e06eb82796f470e9ed
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563031"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Información general del agente de servidores habilitados para Azure Arc

El agente Connected Machine de servidores habilitados para Azure Arc permite administrar las máquinas Windows y Linux hospedadas fuera de Azure en la red corporativa o en otro proveedor de nube. En este artículo se proporciona una descripción detallada del agente, de los requisitos del sistema y de la red, y de los diferentes métodos de implementación.

>[!NOTE]
>A partir del lanzamiento general de los servidores habilitados para Azure Arc en septiembre de 2020, todas las versiones preliminares del agente de Azure Connected Machine (agentes con versiones inferiores a la 1.0) quedarán **en desuso** el **2 de febrero de 2021**.  Este período de tiempo le permite actualizar a la versión 1.0 o superior antes de que los agentes de versión preliminar ya no puedan comunicarse con el servicio de servidores habilitados para Azure Arc.

## <a name="agent-component-details"></a>Detalles del componente de agente

El paquete del agente Azure Connected Machine contiene varios componentes lógicos que se agrupan juntos.

* Hybrid Instance Metadata Service (HIMDS) administra la conexión a Azure y la identidad de Azure de la máquina conectada.

* El agente de configuración de invitado proporciona la funcionalidad de Directiva de invitado y Configuración de invitado, como la evaluación de si la máquina cumple con las directivas requeridas.

    Tenga en cuenta el siguiente comportamiento con [Configuración de invitado](../../governance/policy/concepts/guest-configuration.md) de Azure Policy para una máquina desconectada:

    * Una asignación de directiva de Configuración de invitado que se dirige a las máquinas desconectadas no se ve afectada.
    * La asignación de invitado se almacena de forma local durante 14 días. En el período de 14 días, si el agente Connected Machine se vuelve a conectar al servicio, se vuelven a aplicar las asignaciones de directiva.
    * Las asignaciones se eliminan después de 14 días y no se reasignan a la máquina después de dicho período.

* El agente de extensión administra las extensiones de VM, incluida la instalación, desinstalación y actualización. Las extensiones se descargan de Azure y se copian en la carpeta `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` en Windows, y en `/opt/GC_Ext/downloads` en Linux. En Windows, la extensión se instala en la siguiente ruta de acceso `%SystemDrive%\Packages\Plugins\<extension>` y, en Linux, la extensión se instala en `/var/lib/waagent/<extension>`.

## <a name="download-agents"></a>Descarga de agentes

Puede descargar el paquete del agente de Azure Connected Machine para Windows y Linux desde las ubicaciones que se indican a continuación.

* [Paquete de Windows Installer con el agente](https://aka.ms/AzureConnectedMachineAgent) del centro de descarga de Microsoft.

* En el caso del paquete del agente de Linux, este se distribuye a través del [repositorio de paquetes](https://packages.microsoft.com/) de Microsoft con el formato preferido para la distribución (.RPM o .DEB).

El agente de Azure Connected Machine para Windows y Linux se puede actualizar a la versión más reciente de forma manual o automática según sus necesidades. Para más información, consulte [esta página](manage-agent.md).

## <a name="prerequisites"></a>Prerrequisitos

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Las siguientes versiones de los sistemas operativos Windows y Linux son compatibles oficialmente con el agente de Azure Connected Machine:

- Windows Server 2012 R2 y versiones posteriores (incluido Windows Server Core)
- Ubuntu 16.04 y 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

> [!WARNING]
> El nombre de host de Linux o de equipo de Windows no puede usar una de las palabras reservadas o marcas en el nombre; de lo contrario, si intenta registrar la máquina conectada con Azure, se producirá un error. Consulte [Resolución de errores en los nombres de recursos reservados](../../azure-resource-manager/templates/error-reserved-resource-name.md) para obtener una lista de las palabras reservadas.

### <a name="required-permissions"></a>Permisos necesarios

* Para incorporar máquinas, debe ser miembro del rol **Incorporación de Azure Connected Machine**.

* Para leer, modificar y eliminar una máquina, debe ser miembro del rol **Administrador de recursos de Azure Connected Machine**. 

### <a name="azure-subscription-and-service-limits"></a>Límites del servicio y la suscripción de Azure

Antes de configurar las máquinas con servidores habilitados para Azure Arc, revise los [límites de suscripción](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) y los [límites del grupo de recursos](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) de Azure Resource Manager para planear el número de máquinas que se van a conectar.

### <a name="transport-layer-security-12-protocol"></a>Protocolo Seguridad de la capa de transporte 1.2

Para garantizar la seguridad de los datos en tránsito hacia Azure, se recomienda encarecidamente configurar la máquina para que use Seguridad de la capa de transporte (TLS) 1.2. Las versiones anteriores de TLS/Capa de sockets seguros (SSL) han demostrado ser vulnerables y, si bien todavía funcionan para permitir la compatibilidad con versiones anteriores, **no se recomiendan**.

|Plataforma/lenguaje | Soporte técnico | Más información |
| --- | --- | --- |
|Linux | Las distribuciones de Linux tienden a basarse en [OpenSSL](https://www.openssl.org) para la compatibilidad con TLS 1.2. | Compruebe el [registro de cambios de OpenSSL](https://www.openssl.org/news/changelog.html) para confirmar si su versión de OpenSSL es compatible.|
| Windows Server 2012 R2 y versiones posteriores | Compatible y habilitado de manera predeterminada. | Para confirmar que aún usa la [configuración predeterminada](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Configuración de red

El agente de Connected Machine para Linux y Windows se comunica de forma segura con la salida de Azure Arc a través del puerto TCP 443. Si la máquina se conecta mediante un servidor proxy o un firewall para comunicarse a través de Internet, consulte la siguiente información para comprender qué configuración de red es necesaria.

Si la conectividad saliente está restringida por el firewall o el servidor proxy, asegúrese de que las direcciones URL que se muestran a continuación no estén bloqueadas. Si solo permite los intervalos IP o los nombres de dominio necesarios para que el agente se comunique con el servicio, también debe permitir el acceso a las siguientes etiquetas y direcciones URL del servicio.

Etiquetas de servicio:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Direcciones URL:

| Recurso del agente | Descripción |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Configuración de invitado|
|`*.his.arc.azure.com`|Servicio de identidad híbrida|

Los agentes de versión preliminar (versión 0.11 y anteriores) también requieren acceso a las siguientes direcciones URL:

| Recurso del agente | Descripción |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Configuración de invitado|
|`*-agentservice-prod-1.azure-automation.net`|Configuración de invitado|

Para obtener una lista de direcciones IP para cada etiqueta o región de servicio, consulte el archivo JSON [Rangos de direcciones IP y etiquetas de servicio de Azure: nube pública](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publica actualizaciones semanales que incluyen cada uno de los servicios de Azure y los intervalos IP que usan. Para más información, consulte [Etiquetas de servicio](../../virtual-network/network-security-groups-overview.md#service-tags).

Se requieren las direcciones URL de la tabla anterior junto con la información del intervalo IP de la etiqueta de servicio, ya que la mayoría de los servicios no tienen actualmente un registro de etiquetas de servicio. Por este motivo, las direcciones IP están sujetas a cambios. Si se necesitan intervalos de direcciones IP para la configuración del firewall, se debe usar la etiqueta de servicio **AzureCloud** para permitir el acceso a todos los servicios de Azure. No deshabilite la supervisión de seguridad ni la inspección de estas direcciones URL, pero permítalas como haría con otro tráfico de Internet.

### <a name="register-azure-resource-providers"></a>Registro de proveedores de recursos de Azure

Los servidores habilitados para Azure Arc dependen de los siguientes proveedores de recursos de Azure en la suscripción a fin de poder usar este servicio:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Si no están registrados, puede registrarlos con los comandos siguientes:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

CLI de Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Para registrar también los proveedores de recursos en Azure Portal siga los pasos que se describen en [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalación y configuración

La conexión de máquinas del entorno híbrido directamente con Azure se puede lograr mediante diferentes métodos según sus requisitos. En la tabla siguiente se resalta cada método para determinar cuál se adapta mejor a su organización.

> [!IMPORTANT]
> El agente de Connected Machine no se puede instalar en una máquina virtual Windows de Azure. Si lo intenta, la instalación lo detectará y revertirá la operación.

| Método | Descripción |
|--------|-------------|
| interactivamente, | Instale manualmente el agente en una máquina, o en un grupo reducido de estas, siguiendo los pasos que se indican en [Conexión de máquinas desde Azure Portal](onboard-portal.md).<br> En Azure Portal, puede generar un script y ejecutarlo en la máquina para automatizar los pasos de instalación y configuración del agente.|
| A escala | Instale y configure el agente para varias máquinas según lo que se indica en [Conexión de máquinas mediante una entidad de servicio](onboard-service-principal.md).<br> Este método crea una entidad de servicio para conectar máquinas de forma no interactiva.|
| A escala | Instale y configure el agente para varias máquinas según el método [Uso de DSC de Windows PowerShell](onboard-dsc.md).<br> Este método usa una entidad de servicio para conectar máquinas de forma no interactiva con DSC de PowerShell. |

## <a name="connected-machine-agent-technical-overview"></a>Introducción técnica al agente de Connected Machine

### <a name="windows-agent-installation-details"></a>Detalles de instalación del agente de Windows

El agente Connected Machine para Windows se puede instalar con uno de los tres métodos siguientes:

* Haga doble clic en el archivo `AzureConnectedMachineAgent.msi`.
* Ejecute manualmente el paquete de Windows Installer `AzureConnectedMachineAgent.msi` desde el shell de comandos.
* Desde una sesión de PowerShell mediante un método con scripts.

Después de instalar el agente Connected Machine para Windows, se aplican los siguientes cambios de configuración adicionales a todo el sistema.

* Durante la instalación se crean las carpetas de instalación siguientes.

    |Carpeta |Descripción |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Ruta de instalación predeterminada que contiene los archivos de compatibilidad del agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contiene los archivos de configuración del agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contiene los tokens adquiridos.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contiene el archivo de configuración del agente `agentconfig.json` que registra su información de registro con el servicio.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Ruta de instalación que contiene los archivos del agente de Configuración de invitado. |
    |%ProgramData%\GuestConfig |Contiene las directivas (aplicadas) de Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Las extensiones se descargan de Azure y se copian aquí.|

* Los servicios de Windows siguientes se crean en el equipo de destino durante la instalación del agente.

    |Nombre del servicio |Nombre para mostrar |Nombre del proceso |Descripción |
    |-------------|-------------|-------------|------------|
    |himds |Instance Metadata Service de Azure híbrido |himds |Este servicio implementa Azure Instance Metadata Service (IMDS) para administrar la conexión a Azure y la identidad de Azure de la máquina conectada.|
    |GCArcService |Servicio de Arc de configuración de invitado |gc_service |Supervisa la configuración del estado deseado de la máquina.|
    |ExtensionService |Servicio de extensión de la configuración de invitado | gc_service |Instala las extensiones necesarias que tienen como destino la máquina.|

* Las variables de entorno siguientes se crean durante la instalación del agente.

    |Nombre |Valor predeterminado |Descripción |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Hay varios archivos de registro disponibles para la solución de problemas. Se describen en la tabla siguiente.

    |Log |Descripción |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registra los detalles del servicio de agentes (HIMDS) e interacción con Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contiene la salida de los comandos de la herramienta azcmagent, cuando se usa el argumento verbose (-v).|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Registra los detalles de la actividad del servicio DSC,<br> en concreto la conectividad entre el servicio HIMDS y Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Registra los detalles sobre la telemetría del servicio DSC y el registro detallado.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Registra los detalles sobre el componente de agente de extensión.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|Registra los detalles de la extensión instalada.|

* Se crea el grupo de seguridad local **Aplicaciones de extensión de agente híbrido**.

* Durante la desinstalación del agente, no se quitan los artefactos siguientes.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent y subdirectorios
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Detalles de instalación del agente de Linux

El agente de Connected Machine de Linux se proporciona en el formato de paquete preferido para la distribución (.RPM o .DEB) que se hospeda en el [repositorio de paquetes](https://packages.microsoft.com/) de Microsoft. El agente se instala y configura con el paquete de scripts de shell [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Después de instalar el agente Connected Machine para Linux, se aplican los siguientes cambios de configuración adicionales a todo el sistema.

* Durante la instalación se crean las carpetas de instalación siguientes.

    |Carpeta |Descripción |
    |-------|------------|
    |/var/opt/azcmagent/ |Ruta de instalación predeterminada que contiene los archivos de compatibilidad del agente.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Ruta de instalación que contiene los archivos del agente de Configuración de invitado.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contiene los tokens adquiridos.|
    |/var/lib/GuestConfig |Contiene las directivas (aplicadas) de Azure.|
    |/opt/GC_Ext/downloads|Las extensiones se descargan de Azure y se copian aquí.|

* Los demonios siguientes se crean en el equipo de destino durante la instalación del agente.

    |Nombre del servicio |Nombre para mostrar |Nombre del proceso |Descripción |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Servicio del Agente de Azure Connected Machine |himds |Este servicio implementa Azure Instance Metadata Service (IMDS) para administrar la conexión a Azure y la identidad de Azure de la máquina conectada.|
    |gcad.servce |Servicio de Arc de GC |gc_linux_service |Supervisa la configuración del estado deseado de la máquina. |
    |extd.service |Servicio de extensión |gc_linux_service | Instala las extensiones necesarias que tienen como destino la máquina.|

* Hay varios archivos de registro disponibles para la solución de problemas. Se describen en la tabla siguiente.

    |Log |Descripción |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registra los detalles del servicio de agentes (HIMDS) e interacción con Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contiene la salida de los comandos de la herramienta azcmagent, cuando se usa el argumento verbose (-v).|
    |/opt/logs/dsc.log |Registra los detalles de la actividad del servicio DSC,<br> en concreto la conectividad entre el servicio himds y Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Registra los detalles sobre la telemetría del servicio DSC y el registro detallado.|
    |/var/lib/GuestConfig/ext_mgr_logs |Registra los detalles sobre el componente de agente de extensión.|
    |/var/lib/GuestConfig/extension_logs|Registra los detalles de la extensión instalada.|

* Las variables de entorno siguientes se crean durante la instalación del agente. Estas variables se establecen en `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Nombre |Valor predeterminado |Descripción |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante la desinstalación del agente, no se quitan los artefactos siguientes.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a evaluar los servidores habilitados para Azure Arc, siga el artículo [Conexión de máquinas híbridas a Azure desde Azure Portal](onboard-portal.md).

* Puede encontrar información sobre la solución de problemas en la guía [Solución de problemas de conexión del agente de Connected Machine](troubleshoot-agent-onboard.md).
