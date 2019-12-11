---
title: Sincronización del contenido de una carpeta en la nube
description: Aprenda a implementar una aplicación en Azure App Service mediante una sincronización de contenido desde una carpeta de la nube, lo que incluye OneDrive o Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c55894bff9501d3ffb9aa843a9eaa240a213180e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671734"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronización de contenido de una carpeta de nube a Azure App Service
En este artículo se explica cómo sincronizar el contenido en [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) desde Dropbox y OneDrive. 

La implementación de la sincronización de contenido a petición funciona con el [motor de implementación Kudu](https://github.com/projectkudu/kudu/wiki) de App Service. Puede trabajar con el código y el contenido de la aplicación en una carpeta en la nube des y luego sincronizarlos en App Service con tan solo hacer clic en un botón. La sincronización de contenido usa el servidor de compilación Kudu. 

## <a name="enable-content-sync-deployment"></a>Habilitación de la implementación de la sincronización de contenido

Para habilitar la sincronización de contenido, vaya a la página de la aplicación de App Service en [Azure Portal](https://portal.azure.com).

En el menú de la izquierda, haga clic en **Centro de implementación** > **OneDrive** o **Dropbox** > **Autorizar**. Siga las indicaciones de autorización. 

![](media/app-service-deploy-content-sync/choose-source.png)

Solo debe autorizarse con OneDrive o Dropbox una vez. Si ya dispone de autorización, simplemente haga clic en **Continuar**. Puede cambiar la cuenta de OneDrive o Dropbox autorizada si hace clic en **Cambiar cuenta**.

![](media/app-service-deploy-content-sync/continue.png)

En la página **Configurar**, seleccione la carpeta con la que desea sincronizar. Esta carpeta se crea en la siguiente ruta de acceso de contenido designada en OneDrive o Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Cuando haya terminado, haga clic en **Continuar**.

En la página **Resumen**, verifique las opciones y haga clic en **Finalizar**.

## <a name="synchronize-content"></a>Sincronización de contenido

Si desea sincronizar contenido de la carpeta en la nube con App Service, vuelva a la página del **Centro de implementación** y haga clic en **Sincronizar**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Debido a diferencias subyacentes en las API, **OneDrive para la empresa** no se admite en este momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Deshabilitación de la implementación de la sincronización de contenido

Para deshabilitar la sincronización de contenido, vaya a la página de la aplicación de App Service en [Azure Portal](https://portal.azure.com).

En el menú de la izquierda, haga clic en **Centro de implementación** > **Desconectar**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación desde el repositorio GIT local](deploy-local-git.md)
