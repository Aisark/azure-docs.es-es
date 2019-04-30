---
title: Activos de certificados en Azure Automation
description: Los certificados son de forma segura en Azure Automation por lo que se puede acceder a los runbooks o configuraciones de DSC para autenticarse en Azure y recursos de terceros.  Este artículo explica los detalles de los certificados y cómo trabajar con ellos en la creación de textos y de gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216088"
---
# <a name="certificate-assets-in-azure-automation"></a>Activos de certificados en Azure Automation

Certificados se almacenan de forma segura en Azure Automation para que se pueden acceder mediante runbooks o configuraciones de DSC mediante la **Get-AzureRmAutomationCertificate** actividad para los recursos de Azure Resource Manager. Esta funcionalidad le permite crear runbooks y configuraciones de DSC que usan certificados para autenticación o agregarlos a Azure o a recursos de terceros.

>[!NOTE]
>Los recursos protegidos en Azure Automation incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Azure Automation con una clave única que se genera para cada cuenta de Automation. Esta clave se almacena en una instancia de Key Vault administrada por el sistema. Antes de almacenar un recurso seguro, la clave se carga desde Key Vault y luego se usa para cifrar el recurso. Este proceso se administra mediante Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets de AzureRM PowerShell

En AzureRM, los cmdlets de la tabla siguiente se usan para crear y administrar recursos de credenciales de automatización con Windows PowerShell. Se incluyen como parte de la [módulo Azurerm](/powershell/azure/overview), que está disponible para su uso en los runbooks de Automation y las configuraciones de DSC.

|Cmdlets|DESCRIPCIÓN|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera información sobre un certificado para utilizarlo en un runbook o en la configuración de DSC. Solo puede recuperar el certificado mismo desde la actividad Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Crea un certificado nuevo en Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Quita un certificado de Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Establece las propiedades de un certificado existente, incluyendo la carga del archivo de certificado y el establecimiento de la contraseña de un .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carga un certificado de servicio para el servicio en la nube especificado.|

## <a name="activities"></a>Actividades

Las actividades de la tabla siguiente se usan para obtener acceso a los certificados de un runbook y las configuraciones de DSC.

| Actividades | DESCRIPCIÓN |
|:---|:---|
|Get-AutomationCertificate|Obtiene un certificado para usarlo en un runbook o una configuración de DSC. Devuelve un objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Debe evitar el uso de variables en el parámetro –Name de **Get-AutomationCertificate** en los runbooks o en las configuraciones de DSC, ya que complica la detección de dependencias entre ellos y las variables de Automation durante el diseño.

## <a name="python2-functions"></a>Funciones de Python2

La función de la tabla siguiente se usa para obtener acceso a los certificados de un runbook de Python2.

| Función | DESCRIPCIÓN |
|:---|:---|
| automationassets.get_automation_certificate | Recupera información de un recurso de certificado. |

> [!NOTE]
> Debe importar el módulo **automationassets** al principio del runbook de Python para poder tener acceso a las funciones del recurso.

## <a name="creating-a-new-certificate"></a>Creación de un certificado nuevo

Cuando crea un certificado nuevo, debe cargar un archivo .cer o .pfx a Azure Automation. Si marca el certificado como exportable, podrá transferirlo fuera del almacén de certificados de Azure Automation. Si no es exportable, a continuación, se solo sirve para iniciar el runbook o una configuración de DSC. Azure Automation requiere que el certificado tenga como proveedor a: **proveedor de servicios criptográficos AES y RSA mejorado de Microsoft**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para crear un certificado nuevo con el portal de Azure

1. En su cuenta de Automation, haga clic en el **activos** icono para abrir el **activos** página.
2. Haga clic en el **certificados** icono para abrir el **certificados** página.
3. Haga clic en **agregar un certificado** en la parte superior de la página.
4. Escriba un nombre para el certificado en el cuadro **Nombre** .
5. Para buscar un archivo .cer o .pfx, haga clic en **Seleccione un archivo** en **Cargar un archivo de certificado**. Si selecciona un archivo .pfx, especifique una contraseña y si puede exportarse.
6. Haga clic en **Crear** para guardar el recurso de certificado nuevo.

### <a name="to-create-a-new-certificate-with-powershell"></a>Para crear un nuevo certificado con PowerShell

En el ejemplo siguiente se muestra cómo crear un nuevo certificado de Automation y marcarlo como exportable. Esta acción importa un archivo pfx ya existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Crear un nuevo certificado con la plantilla de Resource Manager

El ejemplo siguiente muestra cómo implementar un certificado en su cuenta de Automation mediante una plantilla de Resource Manager a través de PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Uso de un certificado

Para usar un certificado, use la actividad **Get-AutomationCertificate**. No puede usar el [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet debido a que devuelve información sobre el recurso de certificado, pero no el propio certificado.

### <a name="textual-runbook-sample"></a>Ejemplo de runbook de texto

El código de ejemplo siguiente muestra cómo agregar un certificado a un servicio en la nube en un runbook. En este ejemplo, la contraseña se recupera a partir de una variable de automatización cifrada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Ejemplo de runbook gráfico

Agrega un **Get-AutomationCertificate** a un runbook gráfico, con el botón secundario en el certificado en el panel biblioteca y seleccione **agregar a lienzo**.

![Adición del certificado al lienzo](../media/certificates/automation-certificate-add-to-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar un certificado en un runbook gráfico. Este es el mismo que el ejemplo anterior que se muestra cómo agregar un certificado a un servicio de nube desde un runbook textual.

![Creación gráfica de ejemplo](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ejemplo de Python2

En el ejemplo siguiente se muestra cómo obtener acceso a los certificados en runbooks de Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo trabajar con vínculos para controlar el flujo lógico de las actividades que su runbook está diseñado para efectuar, consulte el tema sobre los [vínculos en la creación gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
