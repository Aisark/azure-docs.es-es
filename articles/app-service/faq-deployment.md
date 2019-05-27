---
title: Preguntas frecuentes acerca de la implementación de Azure App Service | Microsoft Docs
description: Conozca las respuestas a las preguntas más frecuentes sobre la implementación en la característica Web Apps de Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: beee76bdc443b3a66b4500b83d228075b84eed1e
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864764"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Preguntas más frecuentes sobre la implementación en Web Apps en Azure

En este artículo se proporcionan las respuestas a las preguntas más frecuentes sobre los problemas relacionados con la implementación en la [característica Web Apps de Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Acabo de empezar a usar aplicaciones web de App Service. ¿Cómo publico el código?

Aquí tiene algunas opciones para publicar código de aplicaciones web:

*   Realice la implementación con Visual Studio. Si dispone de la solución de Visual Studio, haga clic con el botón derecho en el proyecto de aplicación web y seleccione **Publicar**.
*   Realice la implementación mediante un cliente FTP. En Azure Portal, descargue el perfil de publicación para la aplicación web en la que quiere implementar el código. Después, cargue los archivos en \site\wwwroot con las mismas credenciales FTP del perfil de publicación.

Para obtener más información, vea cómo [implementar la aplicación en App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Veo un mensaje de error al intentar implementar desde Visual Studio. ¿Cómo se puede resolver este error?

Si ve el mensaje siguiente, es posible que esté usando una versión anterior del SDK: “Error during deployment for resource 'YourResourceName' in resource group 'YourResourceGroup': MissingRegistrationForLocation: The subscription is not registered for the resource type 'components' in the location 'Central US'. Volver a registrar este proveedor para obtener acceso a esta ubicación." 

Para resolver este error, actualice al [SDK más reciente](https://azure.microsoft.com/downloads/). Si ve este mensaje y tiene el SDK más reciente, envíe una solicitud de soporte técnico.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>¿Cómo se implementa una aplicación ASP.NET de Visual Studio en App Service?
<a id="deployasp"></a>

El tutorial [crear su primera aplicación web ASP.NET en Azure en cinco minutos](app-service-web-get-started-dotnet.md) se muestra cómo implementar una aplicación web ASP.NET en una aplicación web en App Service mediante Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>¿Cuáles son los distintos tipos de credenciales de implementación?

App Service admite dos tipos de credenciales para la implementación de GIT local y la implementación FTP/S. Para obtener más información sobre cómo configurar las credenciales de implementación, vea [Configuración de credenciales de implementación para App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>¿Cuál es la estructura de archivos o directorios de mi aplicación web de App Service?

Para obtener información sobre la estructura de archivos de la aplicación de App Service, vea [File structure in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (Estructura de archivos en Azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>¿Cómo puedo resolver el error FTP 550 "Espacio insuficiente en el disco" al intentar usar FTP con mis archivos?

Si ve este mensaje, es probable que está ejecutando en una cuota de disco en el plan de servicio para la aplicación web. Tal vez necesite escalar verticalmente a un nivel de servicio superior, en función de sus necesidades de espacio en disco. Para obtener más información sobre los planes de precios y los límites de recursos, vea [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>¿Cómo se configura la implementación continua para la aplicación web de App Service?

Puede configurar la implementación continua desde varios recursos, incluidos Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox y otros repositorios de GIT. Estas opciones están disponibles en el portal. El tutorial [Implementación continua en App Service](deploy-continuous-deployment.md) es un recurso muy útil en el que se explica cómo se configura la implementación continua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>¿Cómo se solucionan los problemas relacionados con la implementación continua desde GitHub y Bitbucket?

Para ayudar a investigar los problemas vinculados a la implementación continua desde GitHub o Bitbucket, vea [Investigating continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment) (Investigar la implementación continua).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>No puedo usar FTP con mi sitio y publicar mi código. ¿Cómo se resuelve este problema?

Para solucionar problemas de FTP:

1. Compruebe que está especificando el nombre de host correcto y credenciales. Para obtener información detallada sobre los distintos tipos de credenciales y cómo usarlas, vea [Deployment credentials](https://github.com/projectkudu/kudu/wiki/Deployment-credentials) (Credenciales de implementación).
2. Compruebe que los puertos FTP no estén bloqueados por un firewall. Los puertos deben tener estos valores de configuración:
    * Puerto de conexión de control de FTP: 21
    * Puerto de conexión de datos de FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>¿Cómo publico mi código en App Service?

El inicio rápido de Azure está diseñado para ayudarle a implementar la aplicación mediante el uso de la pila de implementación y el método que elija. Para utilizar Inicio rápido, en Azure Portal, vaya al servicio de aplicación en **Implementación** y seleccione **Inicio rápido**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>¿Por qué mi aplicación se reinicia a veces después de la implementación en App Service?

Para obtener información sobre las circunstancias en las que una implementación de aplicación podría producir un reinicio, vea [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") (Problemas de implementación frente a problemas en tiempo de ejecución). Como se describe en el artículo, App Service implementa los archivos en la carpeta wwwroot. Nunca reinicia directamente la aplicación.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>¿Cómo se puede integrar código de Azure DevOps con App Service?

Tiene dos opciones para usar la implementación continua con Azure DevOps:

*   Use un proyecto de GIT. Conectarse a través de App Service mediante el centro de implementación.
*   Use un proyecto de Control de versiones de Team Foundation (TFVC). Realice la implementación mediante el agente de compilación para App Service.

La implementación de código continua para ambas opciones depende de los flujos de trabajo de desarrollador existentes y de los procedimientos de inserción en el repositorio. Para obtener más información, consulte estos artículos: 

*   [Aplicar una implementación continua de la aplicación en un sitio web de Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar una organización de Azure DevOps para que pueda implementarse en una aplicación web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>¿Cómo se usa FTP o FTPS para implementar la aplicación en App Service?

Para obtener información sobre el uso de FTP o FTPS para implementar la aplicación web en App Service, vea [Implementación de la aplicación en App Service mediante FTP/S](deploy-ftp.md).
