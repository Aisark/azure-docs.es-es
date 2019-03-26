---
title: Validación de la identidad de Azure en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para validar la identidad de Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: b43fb3ff158a7df609d7a828192815db6b15963b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850067"
---
# <a name="validate-azure-identity"></a>Validación de la identidad de Azure 
Use la herramienta Azure Stack Readiness Checker (AzsReadinessChecker) para validar que su instancia de Azure Active Directory (Azure AD) está lista para usarse con Azure Stack. Valide la solución de identidad de Azure antes de empezar la implementación de Azure Stack.  

La herramienta Readiness Checker valida:
 - Azure Active Directory (Azure AD) como proveedor de identidades de Azure Stack.
 - La cuenta de Azure AD que planea usar puede iniciar sesión como administrador global de Azure Active Directory. 

La validación garantiza que el entorno está listo para que Azure Stack almacene información sobre los usuarios, las aplicaciones, los grupos y las entidades de servicio de Azure Stack en Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker
Descargue la versión más reciente de Azure Stack Readiness Checker (AzsReadinessChecker) desde [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Requisitos previos
Deben cumplirse los siguientes requisitos previos.

**Equipo donde se ejecuta la herramienta:**
 - Windows 10 o Windows Server 2016, con conectividad a internet.
 - Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmd de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:  

   > `$PSVersionTable.PSVersion`
 - Configure [PowerShell para Azure Stack](azure-stack-powershell-install.md). 
 - Versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Entorno de Azure Active Directory:**
 - Identifique la cuenta de Azure AD que va a usar con Azure Stack y compruebe que es administrador global de Azure Active Directory.
 - Identifique el nombre del inquilino de Azure AD. El nombre del inquilino debe ser el nombre de dominio *principal* de Azure Active Directory. Por ejemplo, *contoso.onmicrosoft.com*. 
 - Identifique la clase AzureEnvironment que va a usar. Los valores admitidos para el parámetro de nombre de entorno son AzureCloud, AzureChinaCloud o AzureUSGovernment dependiendo de la suscripción a Azure que esté utilizando.

## <a name="validate-azure-identity"></a>Validación de la identidad de Azure 
1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para establecer *$serviceAdminCredential* como administrador de servicios del inquilino de Azure AD.  Reemplace *serviceadmin\@contoso.onmicrosoft.com* por su cuenta e inquilino. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para iniciar la validación de Azure AD. 
   - Especifique el valor de nombre de entorno para la clase AzureEnvironment. Los valores admitidos para el parámetro de nombre de entorno son AzureCloud, AzureChinaCloud o AzureUSGovernment dependiendo de la suscripción a Azure que esté utilizando.  
   - Especifique el nombre del inquilino de Azure Active Directory que quiere reemplazar *contoso.onmicrosoft.com*. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado es correcto para los requisitos de instalación. Una validación correcta tiene un aspecto similar al de la siguiente imagen: 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```


## <a name="report-and-log-file"></a>Informe y archivo de registro
Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell.

Estos archivos pueden ayudar a compartir el estado de validación antes de implementar Azure Stack o investigar problemas de validación.  Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación. 

De forma predeterminada, los dos archivos se escriben en *C:\Users\<nombre de usuario>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use el parámetro **-OutputPath** ***&lt;ruta de acceso&gt;***  al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.   
 - Use el parámetro **-CleanReport** al final del comando de ejecución para borrar la información de *AzsReadinessCheckerReport.json*.  sobre ejecuciones anteriores de la herramienta. 

Para obtener más información, vea [Informe de validación de Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación
Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en el AzsReadinessChecker.log.

Los ejemplos siguientes ofrecen instrucciones sobre errores de validación comunes.

### <a name="expired-or-temporary-password"></a>Contraseña temporal o expirada 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Causa**: la cuenta no puede iniciar sesión porque la contraseña es temporal o ha expirado.     

**Resolución**: en PowerShell, ejecute el siguiente comando y siga las indicaciones para restablecer la contraseña.  
> `Login-AzureRMAccount`

O bien, inicie sesión en https://portal.azure.com con la cuenta y el usuario se verá obligados a cambiar la contraseña.
### <a name="unknown-user-type"></a>Tipo de usuario desconocido 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Causa**: la cuenta no puede iniciar sesión en el entorno de Azure Active Directory especificado (AADDirectoryTenantName). En este ejemplo, *AzureChinaCloud* se especifica como valor de *AzureEnvironment*.

**Resolución**: confirme que la cuenta es válida para el entorno de Azure especificado. En PowerShell, ejecute el siguiente comando para comprobar que la cuenta es válida para un entorno concreto:   Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>La cuenta no es de administrador 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa**: aunque la cuenta puede iniciar sesión correctamente, la cuenta no es de administrador de Azure Active Directory (AADDirectoryTenantName).  

**Resolución**: inicie sesión en https://portal.azure.com con la cuenta, vaya a **Azure Active Directory** > **Usuarios** > *Seleccione el usuario* > **Rol del directorio** y asegúrese de que el usuario es **Administrador global**.  Si la cuenta es de usuario, vaya a **Azure Active Directory** >  **Nombres de dominio personalizado** y confirme que el nombre que especificó para *AADDirectoryTenantName* está marcado como nombre de dominio principal del directorio.  En este ejemplo, se trata de *contoso.onmicrosoft.com*. 

Azure Stack requiere que el nombre de dominio sea el nombre de dominio principal.

## <a name="next-steps"></a>Pasos siguientes
[Validación del registro de Azure](azure-stack-validate-registration.md)  
[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)  

