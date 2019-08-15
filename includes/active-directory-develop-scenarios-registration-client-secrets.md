---
title: archivo de inclusión
description: archivo include para las páginas de aterrizaje del escenario de cliente confidencial (demonio, aplicación web, API web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8f98808aa0f8a2c32e2117447824114747091a82
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912490"
---
## <a name="registration-of-secrets-or-certificates"></a>Registro de certificados o secretos

Al igual que para cualquier aplicación cliente confidencial, debe registrar un secreto o certificado. Puede registrar los secretos de aplicación a través de la experiencia interactiva en [Azure Portal ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) o bien mediante herramientas de línea de comandos (por ejemplo, PowerShell).

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registro de secretos de cliente con el portal de registro de aplicaciones

La administración de credenciales de cliente se produce en la página **Certificados y secretos** de una aplicación:

![imagen](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Azure AD genera el secreto de aplicación (también denominado secreto de cliente) durante el registro de la aplicación cliente confidencial. Esta generación ocurre al seleccionar **Nuevo secreto de cliente**. En ese momento, debe copiar la cadena de secreto en el Portapapeles para utilizarla en su aplicación, antes de seleccionar **Guardar**. Esta cadena no volverá a aparecer.
- El certificado se carga en el registro de aplicación mediante el botón **Cargar certificado**. Azure AD solo admite certificados que se registran directamente en la aplicación y no siguen las cadenas de certificados.

Para más información, consulte [Inicio rápido: Configuración de una aplicación cliente para tener acceso a las API web | Adición de credenciales a la aplicación web](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Registro de los secretos de cliente mediante PowerShell

De forma alternativa, puede registrar la aplicación con Azure AD mediante herramientas de línea de comandos. El ejemplo [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) muestra cómo registrar un certificado o un secreto de aplicación con una aplicación de Azure AD:

- Para más información sobre cómo registrar un secreto de aplicación, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para más información sobre cómo registrar un certificado con la aplicación, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
