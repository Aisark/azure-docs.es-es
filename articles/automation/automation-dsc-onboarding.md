---
title: Incorporación de máquinas para su administración mediante Azure Automation State Configuration
description: Configuración de máquinas para su administración mediante Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: b81dccf3c71fbaff7b0cbb9ba00f13b458156e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074599"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Incorporación de máquinas para su administración mediante Azure Automation State Configuration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>¿Por qué administrar máquinas con Azure Automation State Configuration?

Configuración de estado de automatización de Azure es un servicio de administración de configuración para los nodos de DSC en cualquier nube o centro de datos localmente.
Permite la escalabilidad en miles de máquinas de forma rápida y sencilla desde una ubicación central y segura.
Puede incorporar fácilmente máquinas, asignarles configuraciones declarativas y ver informes que muestran el cumplimiento por parte de cada máquina del estado deseado especificado.
El servicio de configuración de estado de Azure Automation es a DSC, ¿cuáles son los runbooks de Azure Automation para scripting de PowerShell.
En otras palabras, Azure Automation le ayuda a administrar las configuraciones de DSC, de la misma manera que lo hace con los scripts de PowerShell.
Para más información acerca de las ventajas de usar Azure Automation State Configuration, consulte [Introducción a Azure Automation State Configuration](automation-dsc-overview.md).

Azure Automation State Configuration puede usarse para administrar diversas máquinas:

- Máquinas virtuales de Azure
- Azure Virtual Machines (clásico)
- Instancias de EC2 de Amazon Web Services (AWS)
- Máquinas físicas y virtuales con Windows locales o en una nube que no sea Azure/AWS
- Máquinas físicas y virtuales con Linux locales, en Azure o en una nube que no sea Azure

Además, si no está preparado para administrar la configuración de máquina desde la nube, Azure Automation State Configuration también puede utilizarse como punto de conexión meramente informativo.
Esto le permite establecer configuraciones (inserción) a través de DSC y ver informes de detalles en Azure Automation.

> [!NOTE]
> La administración de máquinas virtuales de Azure con State Configuration se incluye sin cargo adicional si la extensión de máquina virtual DSC instalada es mayor que 2.70. Para más información, consulte la página [**Precios de Automation**](https://azure.microsoft.com/pricing/details/automation/).

En las secciones siguientes, se describe cómo incorporar cada tipo de máquina a Azure Automation State Configuration.

## <a name="azure-virtual-machines"></a>Máquinas virtuales de Azure

Con Azure Automation State Configuration, puede incorporar fácilmente máquinas virtuales de Azure para la administración de configuraciones, mediante Azure Portal, las plantillas de Azure Resource Manager o PowerShell. Internamente y sin que un administrador tenga que acceder de forma remota a la máquina virtual, la extensión Configuración de estado deseado de la máquina virtual de Azure registra la máquina virtual con Azure Automation State Configuration.
Puesto que la extensión Azure VM Desired State Configuration se ejecuta de forma asincrónica, en la sección [**Solución de problemas de incorporación de máquinas virtuales de Azure**](#troubleshooting-azure-virtual-machine-onboarding) se proporcionan los pasos para realizar un seguimiento de su progreso o solucionar problemas.

### <a name="azure-portal"></a>Azure Portal

En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de Azure Automation en la que quiere incorporar máquinas virtuales. En la página de State Configuration, en la pestaña **Nodos**, haga clic en **+ Agregar**.

Seleccione una máquina virtual de Azure para incorporarla.

Si la máquina no tiene instalada la extensión de estado deseada de PowerShell y el estado de energía es "en ejecución", haga clic en **Conectar**.

En **Registro**, escriba los [valores del Administrador de configuración local de DSC de PowerShell](/powershell/dsc/metaconfig4) necesarios para su caso de uso, así como, opcionalmente, una configuración de nodo para asignarla a la máquina virtual.

![incorporación](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure

Se pueden implementar máquinas virtuales de Azure e incorporarlas a Azure Automation State Configuration mediante las plantillas de Azure Resource Manager. Consulte [servidor administrado por servicio Desired State Configuration](https://azure.microsoft.com/en-us/resources/templates/101-automation-configuration/) para una plantilla de ejemplo que incorpora una máquina virtual existente a la configuración de estado de automatización de Azure.
Si administra un conjunto de escalado de máquinas virtuales, consulte la plantilla de ejemplo [definir configuración de escalado de máquina virtual administrada por Azure Automation](https://azure.microsoft.com/en-us/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

El cmdlet [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) sirve para incorporar máquinas virtuales en el Portal de Azure por medio de PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrar las máquinas virtuales en suscripciones de Azure

La mejor manera de registrar las máquinas virtuales desde otras suscripciones de Azure es usar la extensión DSC en una plantilla de implementación de Azure Resource Manager.
Se proporcionan ejemplos en [extensión Desired State Configuration con plantillas de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/dsc-template).
Para buscar la clave de registro y la dirección URL de registro que se usará como parámetros en la plantilla, vea el siguiente [ **registro seguro** ](#secure-registration) sección.

## <a name="azure-virtual-machines-classic"></a>Azure Virtual Machines (clásico)

Con Azure Automation State Configuration, puede incorporar fácilmente máquinas virtuales de Azure (clásico) para la administración de configuraciones mediante el Azure Portal o PowerShell. Internamente y sin que un administrador tenga que acceder de forma remota a la máquina virtual, la extensión Configuración de estado deseado de la máquina virtual de Azure registra la máquina virtual con Azure Automation State Configuration.
Se proporcionan pasos para seguir el progreso o solucionar problemas en la siguiente [ **incorporación de máquinas virtuales de la solución de problemas de Azure** ](#troubleshooting-azure-virtual-machine-onboarding) sección.

### <a name="azure-portal-classic-virtual-machines"></a>Portal de Azure (máquinas virtuales clásicas)

En [Azure Portal](https://portal.azure.com/) , haga clic en **Examinar** -> **Virtual Machines (clásico)**. Seleccione la máquina virtual de Windows que desea incorporar. En la hoja del panel de la máquina virtual, haga clic en **Toda la configuración** -> **Extensiones** -> **Agregar** -> **DSC de Azure Automation** -> **Crear**.
Escriba el [valores del Administrador de configuración Local de PowerShell DSC](/powershell/dsc/metaconfig4) para la clave de registro de su cuenta de Automation y dirección URL de registro y, opcionalmente, una configuración de nodo asignar a la máquina virtual.

![Extensiones de máquina virtual de Azure para DSC](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para buscar la dirección URL de registro y la clave de la cuenta de Automation a la que incorporar la máquina, consulte la sección [**Registro seguro**](#secure-registration):

### <a name="powershell-classic-virtual-machines"></a>PowerShell (máquinas virtuales clásicas)

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ''
$ServiceName = ''
$AutomationAccountName = ''
$AutomationAccountResourceGroup = ''

# fill in the name of a Node Configuration in Azure Automation State Configuration, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ''

# get Azure Automation State Configuration registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation State Configuration
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ''
    ModulesUrl = 'https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip'
    ConfigurationFunction = 'RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2'

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://docs.microsoft.com/powershell/dsc/metaConfig for more details
    Properties = @{
        RegistrationKey = @{
            UserName = 'notused'
            Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = 'ApplyAndMonitor'
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = 'ContinueConfiguration'
        AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.76 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Los nombres de configuración del nodo de State Configuration distinguen mayúsculas de minúsculas en el portal. Si estas no coinciden, el nodo no se mostrará en la pestaña **Nodos**.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuales de Amazon Web Services (AWS)

Puede incorporar fácilmente máquinas virtuales de Amazon Web Services para la administración de la configuración mediante el Kit de herramientas de DSC AWS de Azure Automation State Configuration. Puede obtener más información acerca de estas herramientas [aquí](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Máquinas físicas y virtuales con Windows locales o en una nube que no sea Azure/AWS

Los servidores de Windows que ejecutan de forma local o en otros entornos de nube también se pueden incorporar a la configuración de estado de Azure Automation, siempre y cuando tengan acceso saliente a Azure:

1. Asegúrese de que la versión más reciente de [WMF 5](https://aka.ms/wmf5latest) esté instalada en las máquinas que desee incorporar a Azure Automation State Configuration.
1. Siga las indicaciones de la siguiente sección: [**Generación de metaconfiguraciones de DSC**](#generating-dsc-metaconfigurations) para generar una carpeta que contenga las metaconfiguraciones de DSC necesarias.
1. Aplique de forma remota la metaconfiguración de DSC de PowerShell a las máquinas que quiere incorporar. **La máquina desde la que se ejecuta este comando debe tener instalada la versión más reciente de [WMF 5](https://aka.ms/wmf5latest)**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Si no puede aplicar las metaconfiguraciones de DSC de PowerShell de forma remota, copie la carpeta de metaconfiguraciones del paso 2 en cada máquina que vaya a incorporar. Después, llame a **Set-DscLocalConfigurationManager** localmente en cada máquina que vaya a incorporar.
1. Mediante Azure Portal o los cmdlets, compruebe que las máquinas que vaya a incorporar aparezcan ahora como nodos de State Configuration registrados en su cuenta de Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Linux físicos o virtuales máquinas locales, o en una nube que no sea Azure

Servidores Linux que se ejecutan de forma local o en otros entornos de nube también se pueden incorporar a la configuración de estado de Azure Automation, siempre y cuando tengan acceso saliente a Azure:

1. Asegúrese de que la versión más reciente d [PowerShell Desired State Configuration para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) esté instalada en las máquinas que desee incorporar a Azure Automation State Configuration.
1. Si los [valores predeterminados del Administrador de configuración local de PowerShell DSC](/powershell/dsc/metaconfig4) coinciden con su caso de uso, y quiere incorporar máquinas de modo que **tanto** extraigan información de Azure Automation State Configuration como que envíen informes allí:

   - En cada máquina con Linux que vaya a incorporar a Azure Automation State Configuration, use `Register.py` para incorporarla con los valores predeterminados del Administrador de configuración local de PowerShell DSC:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para buscar la clave de registro y la dirección URL de registro para su cuenta de Automation, consulte la sección [**Registro seguro**](#secure-registration).

     Si el valor predeterminado es el Administrador de configuración Local de PowerShell DSC **no** coincide con el caso de uso, o desea incorporar máquinas, que solo informen a la configuración de estado de automatización de Azure, siga los pasos del 3 al 6. De lo contrario, vaya directamente al paso 6.

1. Siga las indicaciones de la siguiente sección: [**Generación de metaconfiguraciones de DSC**](#generating-dsc-metaconfigurations) para generar una carpeta que contenga las metaconfiguraciones de DSC necesarias.
1. Aplique de forma remota la metaconfiguración de DSC de PowerShell a las máquinas que desea incorporar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

La máquina desde la que se ejecuta este comando debe tener instalada la versión más reciente de [WMF 5](https://aka.ms/wmf5latest) .

1. Si no se puede aplicar las metaconfiguraciones de DSC de PowerShell de forma remota, copie la metaconfiguración correspondiente a esa máquina desde la carpeta en el paso 5 en el equipo Linux. Después, llame a `SetDscLocalConfigurationManager.py` localmente en cada máquina Linux que desee incorporar a Azure Automation State Configuration:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Mediante Azure Portal o los cmdlets, compruebe que las máquinas que vaya a incorporar aparezcan ahora como nodos de DSC registrados en su cuenta de Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generación de metaconfiguraciones de DSC

Para incorporar genéricamente cualquier máquina a la configuración de estado de Azure Automation, un [metaconfiguración de DSC](/powershell/dsc/metaconfig) puede generarse que indica al agente de DSC que extraiga de o informe a la configuración de estado de automatización de Azure. Las metaconfiguraciones de Azure Automation State Configuration se pueden generar con una configuración de PowerShell DSC o con los cmdlets de PowerShell de Azure Automation.

> [!NOTE]
> Las metaconfiguraciones de DSC contienen los secretos necesarios para incorporar una máquina a una cuenta de Automation para su administración. Asegúrese de proteger adecuadamente cualquier metaconfiguración de DSC que cree o elimínelos tras su uso.

### <a name="using-a-dsc-configuration"></a>Uso de una configuración de DSC

1. Abra VSCode (o su editor preferido) como administrador en una máquina de su entorno local. La máquina debe tener instalada la versión más reciente de [WMF 5](https://aka.ms/wmf5latest) .
1. Copie el siguiente script localmente. Este script contiene una configuración de DSC de PowerShell para crear metaconfiguraciones y un comando para iniciar la creación de la metaconfiguración.

> [!NOTE]
> Los nombres de configuración del nodo de State Configuration distinguen mayúsculas de minúsculas en el portal. Si estas no coinciden, el nodo no se mostrará en la pestaña **Nodos**.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Rellene la clave del Registro y la dirección URL para su cuenta de Automation, así como los nombres de las máquinas que quiere incorporar. Todos los demás parámetros son opcionales. Para buscar la clave de registro y la dirección URL de registro para su cuenta de Automation, consulte la sección [**Registro seguro**](#secure-registration).
1. Si quiere que las máquinas notifiquen la información de estado de DSC a Azure Automation State Configuration, pero que no extraigan configuración o módulos de PowerShell, establezca el parámetro **ReportOnly** en verdadero.
1. Ejecute el script. Ahora debería tener una carpeta llamada **DscMetaConfigs** en el directorio de trabajo que contenga las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Uso de los cmdlets de Azure Automation

Si los valores predeterminados del Administrador de configuración local de DSC de PowerShell coinciden con su caso de uso, y quiere incorporar máquinas de modo que ambas extraigan de Azure Automation State Configuration y notifiquen allí, los cmdlets de Azure Automation ofrecen un método simplificado de generar las configuraciones de DSC necesarias:

1. Abra la consola de PowerShell o VSCode como administrador en una máquina de su entorno local.
2. Conéctese a Azure Resource Manager con `Connect-AzureRmAccount`
3. Descargue las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar desde la cuenta de Automation a la que quiere incorporar nodos:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Ahora debería tener una carpeta llamada ***DscMetaConfigs***, que contenga las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registro seguro

Las máquinas se pueden incorporar de forma segura a una cuenta de Azure Automation mediante el protocolo de registro WMF 5 de DSC, que permite que un nodo de DSC se autentique en un servidor de informes o de extracción de DSC de PowerShell (lo que incluye Azure Automation State Configuration). El nodo se registra en el servidor en una **dirección URL de registro**, que se autentica mediante una **clave de registro**. Durante el registro, el nodo de DSC y el servidor de informes o de extracción de DSC negocian un certificado único que este nodo usará para autenticarse en el servidor después del registro. Este proceso impide que los nodos incorporados se suplanten entre sí, como por ejemplo si un nodo está afectado y se comporta de forma malintencionada. Después del registro, la clave de registro no se vuelve a usar para la autenticación y se elimina del nodo.

Puede obtener la información necesaria para el protocolo de registro de State Configuration en **Claves** dentro de **Configuración de la cuenta** en Azure Portal. Para abrir esta hoja, haga clic en el icono de llave en el panel **Essentials** de la cuenta de Automation.

![Claves y dirección URL de Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- La dirección URL de registro es el campo de dirección URL en la hoja Administrar claves.
- La clave de registro es la clave de acceso principal o la clave de acceso secundaria en la hoja Administrar claves. Se puede usar cualquiera de las dos.

Para mayor seguridad, se pueden volver a generar las claves de acceso principal y secundaria de una cuenta de Automation en cualquier momento (en la página **Administrar claves** ) para evitar que los registros de nodo futuros usen claves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solución de problemas de incorporación de máquinas virtuales de Azure

Azure Automation State Configuration permite incorporar fácilmente máquinas virtuales de Azure con Windows para la administración de configuraciones. Internamente, la extensión Configuración de estado deseado de la máquina virtual de Azure se usa para registrar la máquina virtual con Azure Automation State Configuration. Puesto que la extensión Configuración de estado deseado de la máquina virtual de Azure se ejecuta de forma asincrónica, puede ser importante realizar el seguimiento de su progreso y solucionar los problemas de ejecución.

> [!NOTE]
> Cualquier método para incorporar una máquina virtual de Azure con Windows a Azure Automation State Configuration que use la extensión Azure VM Desired State Configuration podría tardar hasta una hora en mostrar el nodo como registrado en Azure Automation. Esto se debe a la instalación de Windows Management Framework 5.0 en la máquina virtual por la extensión de DSC de la máquina virtual de Azure, que es necesario para incorporar la máquina a Azure Automation State Configuration.

Para solucionar problemas o ver el estado de la extensión Azure VM Desired State Configuration en Azure Portal, vaya a la máquina virtual que se va a incorporar y haga clic en **Extensiones** dentro de **Configuración**. A continuación, haga clic en **DSC** o **DSCForLinux** según el sistema operativo. Para obtener más detalles, puede hacer clic en **Ver estado detallado**.

## <a name="certificate-expiration-and-reregistration"></a>Expiración y repetición del registro del certificado

Después de registrar un equipo como un nodo de DSC en Azure Automation State Configuration, hay una serie de motivos por los que puede que necesite volver a registrar ese nodo en el futuro:

- Tras el registro, cada nodo negocia automáticamente un certificado único para la autenticación que expira después de un año. En la actualidad, el protocolo de registro de DSC de PowerShell no puede renovar automáticamente los certificados que vayan a expirar, por lo que tendrá que volver a registrar los nodos transcurrido un año. Antes de volver a registrar, asegúrese de que cada nodo ejecute Windows Management Framework 5.0 RTM. Si el certificado de autenticación de un nodo expira y el nodo no se registra de nuevo, este no puede comunicarse con la Azure Automation y se marcará como "No responde". Si dicha actualización se realiza en un plazo de 90 días o menos desde la fecha de expiración del certificado o en cualquier momento después de dicha fecha, se generará y usará un certificado nuevo.
- Para cambiar cualquier [valor del Administrador de configuración local de DSC de PowerShell](/powershell/dsc/metaconfig4) que se estableció durante el registro inicial del nodo, como ConfigurationMode. Actualmente, estos valores de agente DSC solo pueden cambiarse mediante un nuevo registro. La única excepción es la configuración de nodo asignada al nodo, que se puede cambiar directamente en DSC de Azure Automation.

El nuevo registro se puede realizar tal y como registró el nodo inicialmente, con cualquiera de los métodos incorporados descritos en este documento. No es necesario anular el registro de un nodo desde Azure Automation State Configuration antes de volver a registrarlo.

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a usar Azure Automation State Configuration, consulte [Introducción a Azure Automation State Configuration](automation-dsc-getting-started.md)
- Para obtener información acerca de la compilación de configuraciones de DSC para que poder asignarlas a los nodos de destino, consulte [Compilación de configuraciones en Azure Automation State Configuration](automation-dsc-compile.md)
- Para la referencia de cmdlets de PowerShell, consulte [cmdlets de Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Para obtener información de precios, consulte [Precios de Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Para ver un ejemplo del uso de Azure Automation State Configuration en una canalización de implementación continua, consulte [Implementación continua mediante Azure Automation State Configuration y Chocolatey](automation-dsc-cd-chocolatey.md)
