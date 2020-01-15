---
title: Configuración del consentimiento de los usuarios finales a las aplicaciones con Azure AD
description: Aprenda a administrar cómo y cuándo los usuarios pueden dar consentimiento a las aplicaciones que tendrán acceso a los datos de su organización.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443401"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configuración del consentimiento de los usuarios finales a las aplicaciones

Las aplicaciones se pueden integrar con la Plataforma de identidad de Microsoft para permitir que los usuarios inicien sesión con su cuenta profesional o educativa en Azure Active Directory (Azure AD) y obtengan acceso a los datos de su organización con el fin de ofrecer experiencias enriquecidas controladas por datos. Distintos permisos permiten que la aplicación tenga diferentes niveles de acceso para los datos de los usuarios y de la organización.

De forma predeterminada, los usuarios pueden dar consentimiento a las aplicaciones para acceder a los datos de la organización, aunque solo con algunos permisos. Por ejemplo, de forma predeterminada, un usuario puede dar su consentimiento para permitir que una aplicación acceda a su propio buzón o a las conversaciones de un equipo del que es propietario en Teams, pero no para permitir que una aplicación acceda de forma desatendida para leer y escribir en todos los sitios de SharePoint de la organización. Aunque permitir que los usuarios den su consentimiento por su cuenta les permite adquirir fácilmente aplicaciones útiles que se integran con Microsoft 365, Azure y otros servicios, puede representar un riesgo si no se utiliza y supervisa cuidadosamente.

Microsoft recomienda deshabilitar las operaciones futuras de consentimiento del usuario para ayudar a reducir el área expuesta y a mitigar este riesgo. Si el consentimiento del usuario se deshabilita, se seguirán respetando los consentimientos previos, pero todas las operaciones de consentimiento futuras tendrá que realizarlas un administrador. Los usuarios pueden solicitar el consentimiento del administrador para todo el inquilino a través de un [flujo de trabajo de solicitud de consentimiento del administrador](configure-admin-consent-workflow.md) integrado o a través de sus propios procesos de soporte técnico. Para obtener más información consulte [Cinco pasos para asegurar su infraestructura de identidad](../../security/fundamentals/steps-secure-identity.md).

## <a name="configure-user-consent-to-applications"></a>Configuración del consentimiento del usuario a aplicaciones
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Deshabilitación o habilitación del consentimiento del usuario en Azure Portal

Puede usar Azure Portal para deshabilitar o habilitar la capacidad de los usuarios de dar su consentimiento a las aplicaciones que acceden a los datos de su organización:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como [Administrador global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Seleccione **Azure Active Directory**, **Aplicaciones empresariales** y **Configuración de usuario**.
3. Habilite o deshabilite el consentimiento del usuario con el control que tiene la etiqueta **Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre**.
4. (Opcional) Configure un [flujo de trabajo de solicitud de consentimiento del administrador](configure-admin-consent-workflow.md) para asegurarse de que los usuarios que no tienen permiso para dar su consentimiento a una aplicación puedan solicitar la aprobación.

> [!TIP]
> Para permitir que los usuarios soliciten la revisión por parte de un administrador de una aplicación a la que el usuario no está autorizado a dar su consentimiento (por ejemplo, porque el consentimiento del usuario se ha deshabilitado o porque la aplicación está solicitando permisos que el usuario no puede conceder), plantéese la posibilidad de [configurar el flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Deshabilitación o habilitación del consentimiento del usuario con PowerShell

Puede usar el módulo Azure AD PowerShell v1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)), para deshabilitar o habilitar la capacidad de los usuarios de dar su consentimiento a las aplicaciones que acceden a los datos de la organización.

1. Inicie sesión en la organización mediante la ejecución de este cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Compruebe si el consentimiento del usuario está habilitado mediante la ejecución de este cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Habilite o deshabilite el consentimiento del usuario. Por ejemplo, para deshabilitar el consentimiento del usuario, ejecute este cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configuración del consentimiento del propietario del grupo para las aplicaciones que acceden a los datos del grupo

> [!IMPORTANT]
> La siguiente información corresponde a una próxima característica que permitirá a los propietarios del grupo conceder a las aplicaciones acceso a los datos de sus grupos. Cuando esta funcionalidad se publique, se habilitará de forma predeterminada. Aunque esta característica aún no se ha publicado ampliamente, puede usar estas instrucciones para deshabilitar la funcionalidad antes de su publicación.

Los propietarios de grupo pueden autorizar a las aplicaciones (por ejemplo, las aplicaciones publicadas por otros proveedores) el acceso a los datos de la organización asociados al grupo. Por ejemplo, el propietario de un equipo (que es el propietario del grupo Office 365 del equipo) puede permitir que una aplicación lea todos los mensajes de Teams del equipo o bien mostrar el perfil básico de los miembros de un grupo.

> [!NOTE]
> Independientemente de esta configuración, siempre se permite que el propietario del grupo agregue otros usuarios o aplicaciones directamente como propietarios de un grupo.

### <a name="configure-group-owner-consent-using-powershell"></a>Configuración del consentimiento del propietario del grupo con PowerShell

Puede usar el módulo en versión preliminar de Azure AD PowerShell ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)), para deshabilitar o habilitar la capacidad de los usuarios de dar su consentimiento a las aplicaciones que acceden a los datos de la organización.

1. Asegúrese de que usa el módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (este paso es importante si ha instalado el módulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) y el módulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Conéctese a Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Recupere el valor actual para la configuración del directorio *Consent Policy Settings* del inquilino. Esto requiere comprobar si se ha creado la configuración del directorio para esta característica y, si no es así, usar los valores de la correspondiente plantilla de configuración de directorio.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Comprenda los valores de configuración. Hay dos valores de configuración que definen qué usuarios podrán permitir que una aplicación acceda a los datos de su grupo:

    | Configuración       | Tipo         | Descripción  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  Marca que indica si los propietarios de los grupos pueden conceder permisos específicos del grupo. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Si _EnableGroupSpecificConsent_ se establece en "True" y este valor se establece en el identificador de objeto de un grupo, los miembros del grupo identificado estarán autorizados a conceder permisos específicos de grupo a los grupos que poseen. |

5. Actualice los valores de configuración para la configuración deseada:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Guarde la configuración.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

[Configuración del flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md)

[Concesión del consentimiento del administrador para todo el inquilino para una aplicación](grant-admin-consent.md)

[Permisos y consentimiento en la plataforma de identidad de Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD en StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
