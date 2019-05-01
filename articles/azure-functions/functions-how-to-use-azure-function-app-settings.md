---
title: Definición de la configuración de Azure Function App | Microsoft Docs
description: Obtenga información sobre cómo definir la configuración de Azure Function App.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7497255dcad55cea86e0c640e2f1423d7d763a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738100"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Administración de una Function App en Azure Portal 

En Azure Functions, una Function App ofrece el contexto de ejecución de funciones individuales. Los comportamientos de Function App se aplican a todas las funciones hospedadas en una Function App determinada. En este tema se describe cómo configurar y administrar Function App en Azure Portal.

Para comenzar, vaya a [Azure Portal](https://portal.azure.com) e inicie sesión en la cuenta de Azure. En la barra de búsqueda en la parte superior del portal, escriba el nombre de la Function App y selecciónela en la lista. Después de seleccionar la Function App, vea la siguiente página:

![Información general sobre Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funciones favoritas en el portal 

En ocasiones, puede resultar difícil encontrar los recursos en [Azure Portal]. Para que sea más fácil encontrar las aplicaciones de función que ha creado, agregue Function App a sus favoritos en el portal. 

1. Inicie sesión en [Azure Portal].

2. Haga clic en la flecha en la parte inferior izquierda para ampliar todos los servicios, escriba `Functions` en el campo **Filtro** y, después, haga clic en la estrella junto a **Instancias de Function App**.  
 
    ![Creación de una aplicación de función en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Esto agrega el icono Funciones al menú de la izquierda del portal.

3. Cierre el menú y después desplácese hasta la parte inferior para ver el icono Funciones. Haga clic en este icono para ver una lista de todas sus instancias de Function App. Haga clic en su Function App para trabajar con las funciones de esta aplicación. 
 
    ![Function Apps en favoritos](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Azure Portal]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Pestaña Configuración de Function App

![Información general sobre Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

En la pestaña **Configuración**, puede actualizar la versión de Functions en tiempo de ejecución que la Function App utiliza. Aquí también puede administrar las claves de host usadas para restringir el acceso HTTP a todas las funciones que Function App hospeda.

Functions admite los planes de hospedaje de consumo y App Service. Para más información, vea [Elija el plan de servicio correcto para Azure Functions](functions-scale.md). Para poder predecir mejor en el plan de consumo, Functions le permite limitar el uso de la plataforma mediante la configuración de una cuota de uso diaria, en gigabytes por segundo. Cuando se alcanza la cuota de uso diaria, la Function App se detiene. Una Function App que se haya detenido como resultado de alcanzar la cuota de gasto se puede volver a habilitar desde el mismo contexto que con el que se estableciera la cuota de gasto diario. Vea la [página de precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para consultar los detalles de facturación.   

## <a name="platform-features-tab"></a>Pestaña Características de la plataforma

![Pestaña Características de la plataforma de Function App](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Las Function App se ejecutan en la plataforma de Azure App Service, donde también se realiza su mantenimiento. Por tanto, Function App tiene acceso a la mayoría de las características de la plataforma de hospedaje web principal de Azure. En la pestaña **Características de la plataforma** puede acceder a muchas características de la plataforma de App Service que puede usar en las Function App. 

> [!NOTE]
> No todas las características de App Service están disponibles cuando una Function App se ejecuta con el plan de hospedaje de consumo.

El resto de este tema se centra en las siguientes características de App Service en Azure Portal que resultan útiles para Functions:

+ [Editor de App Service](#editor)
+ [Configuración de la aplicación](#settings) 
+ [Console](#console)
+ [Herramientas avanzadas (Kudu)](#kudu)
+ [Opciones de implementación](#deployment)
+ [CORS](#cors)
+ [Autenticación](#auth)
+ [Definición de la API](#swagger)

Para más información sobre cómo trabajar con la configuración de App Service, vea [Configuración de Azure App Service](../app-service/web-sites-configure.md).

### <a name="editor"></a>Editor de App Service

| | |
|-|-|
| ![Editor de App Service de Function App](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | El Editor de App Service es un editor en portal avanzado que puede usar para modificar archivos de configuración JSON y archivos de código similares. Al seleccionar esta opción se inicia una pestaña de explorador independiente con un editor básico. Esto le permite realizar la integración con el repositorio Git, ejecutar y depurar código y modificar la configuración de Function App. Este editor proporciona un entorno de desarrollo mejorado para las funciones en comparación con la hoja de Function App predeterminada.    |

![Editor de App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Configuración de la aplicación

| | |
|-|-|
| ![Configuración de la aplicación Function App](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | En la hoja **Configuración de la aplicación** de App Service puede configurar y administrar las versiones de Framework, la depuración remota, la configuración de las aplicaciones y las cadenas de conexión. Al integrar Function App con otros servicios de Azure y de terceros, puede modificar esta configuración aquí. Para eliminar un valor, desplácese a la derecha y seleccione el icono **X** situado en el extremo derecho de la línea (no se muestra en la siguiente imagen).

![Configuración de la aplicación](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Consola

| | |
|-|-|
| ![Consola de Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | La consola del portal es una herramienta ideal para desarrolladores si prefiere interactuar con Function App desde la línea de comandos. Los comandos comunes incluyen creación de archivos y directorios y navegación por los mismos, así como la ejecución de archivos y scripts por lotes. |

![Consola de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Herramientas avanzadas (Kudu)

| | |
|-|-|
| ![Kudu de Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Las herramientas avanzadas para App Service (también conocidas como Kudu) proporcionan acceso a las características administrativas avanzadas de la Function App. Con Kudu, puede administrar la información del sistema, la configuración de las aplicaciones, las variables del entorno, las extensiones del sitio, los encabezados HTTP y las variables del servidor. También puede iniciar **Kudu** si examina el punto de conexión de SCM de la Function App, como `https://<myfunctionapp>.scm.azurewebsites.net/`. |

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opciones de implementación

| | |
|-|-|
| ![Opciones de implementación de Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions permite desarrollar código de funciones en la máquina local. Después, puede cargar el proyecto de Function App local en Azure. Además de la carga por FTP tradicional, Functions permite implementar Function App con soluciones populares de integración continua, como GitHub, Azure DevOps, Dropbox, Bitbucket y otras. Para más información, vea [Implementación continua para Azure Functions](functions-continuous-deployment.md). Para realizar cargas manuales con FTP o Git local, también debe [configurar las credenciales de implementación](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS de Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para evitar la ejecución de código malintencionado en los servicios, App Service bloquea las llamadas a las Function App desde orígenes externos. Functions admite el uso compartido de recursos entre orígenes (CORS) para que pueda definir una "lista blanca" de orígenes permitidos desde los que las funciones puedan aceptar solicitudes remotas.  |

![Configuración de CORS de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticación

| | |
|-|-|
| ![Autenticación de Function App en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Si las funciones usan un desencadenador HTTP, puede requerir que las llamadas se autentiquen primero. App Service admite la autenticación de Azure Active Directory y el inicio de sesión en proveedores locales, como Facebook, Microsoft y Twitter. Para más información sobre cómo configurar los proveedores de autenticación específicos, consulte [Autenticación y autorización en Azure App Service](../app-service/overview-authentication-authorization.md). |

![Configuración de la autenticación de una Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definición de la API

| | |
|-|-|
| ![Definición de API de Function App mediante Swagger en Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions admite Swagger para permitir que los clientes consuman las funciones desencadenadas por HTTP de forma más fácil. Para más información sobre cómo crear definiciones de API con Swagger, visite [Hospedaje de una API RESTful con CORS en Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). También puede usar Functions Proxies para definir una única superficie de API para varias funciones. Para más información, vea [Trabajo con Azure Functions Proxies](functions-proxies.md). |

![Configuración de la API de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Pasos siguientes

+ [Configuración de Azure App Service](../app-service/web-sites-configure.md)
+ [Implementación continua para Azure Functions](functions-continuous-deployment.md)



