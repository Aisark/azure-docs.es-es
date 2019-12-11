---
title: Introducción a las aplicaciones de Xamarin.iOS
description: Siga este tutorial para empezar a usar Mobile Apps para el desarrollo de Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 2bb13393a9f348d85ab9fc964b784ba4d4d6a783
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668632"
---
# <a name="create-a-xamarinios-app"></a>Creación de una aplicación Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center admite servicios integrados de un extremo a otro fundamentales para el desarrollo de aplicaciones móviles. Los desarrolladores pueden usar los servicios de **compilación**, **prueba** y **distribución** para configurar la canalización de integración y entrega continuas. Una vez que se ha implementado la aplicación, los desarrolladores pueden supervisar el estado y el uso de su aplicación con los servicios de **análisis** y **diagnóstico**, e interactuar con los usuarios que utilizan el servicio de **Push** (inserción). Además, los desarrolladores pueden aprovechar **Auth** para autenticar a los usuarios y el servicio de **datos** para almacenar y sincronizar los datos de la aplicación en la nube.
>
> Si está pensando en integrar servicios en la nube en su aplicación para dispositivos móviles, regístrese en [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoy mismo.

## <a name="overview"></a>Información general
En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.iOS con un back-end de Aplicaciones móviles de Azure.  Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.iOS simple de la *lista de tareas pendientes* que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Xamarin.iOS sobre cómo usar la característica Mobile Apps en Azure App Service.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe cumplir los siguientes requisitos previos:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, regístrese para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio para Mac. Consulte [Setup and install for Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019) (Configuración e instalación de Visual Studio para Mac)
* Un equipo Mac con Xcode 9.0 o posterior.
  
## <a name="create-an-azure-mobile-app-backend"></a>Creación de un nuevo back-end de Azure Mobile App
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creación de una conexión de base de datos y configuración del proyecto de cliente y servidor
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Ejecute la aplicación Xamarin.iOS
1. Abra el proyecto de Xamarin.iOS

2. Vaya a [Azure Portal](https://portal.azure.com/) y diríjase a la aplicación móvil que ha creado. En la hoja `Overview`, busque la dirección URL que es el punto de conexión público de la aplicación móvil. Por ejemplo, el nombre de sitio para el nombre de mi aplicación "test123" será https://test123.azurewebsites.net.

3. Abra el archivo `QSTodoService.cs` en esta carpeta - xamarin.iOS/ZUMOAPPNAME. El nombre de la aplicación es `ZUMOAPPNAME`.

4. En la clase `QSTodoService`, reemplace la variable `ZUMOAPPURL` con el punto de conexión público anterior.

    `const string applicationURL = @"ZUMOAPPURL";`

    se convierte en
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Presione la tecla F5 para implementar y ejecutar la aplicación en un emulador de iPhone.

6. En la aplicación, escriba un texto significativo, como *Completar el tutorial* y luego haga clic en el botón +.

    ![][10]

    Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de la aplicación móvil devuelve los elementos almacenados en la tabla y los datos aparecen en la lista.

   > [!NOTE]
   > Puede revisar el código de acceso al back-end de aplicación móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png