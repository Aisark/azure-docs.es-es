---
title: Introducción a Azure AD en proyectos de WebApi de Visual Studio
description: Cómo empezar a usar Azure Active Directory en los proyectos de WebApi después de crear un Azure AD usando los servicios conectados de Visual Studio o de conectarse a él
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662d958fc7ef6fe5c9d0e61a1d8e48983d10196f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353812"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Introducción a Azure Active Directory (proyectos de WebApi)

> [!div class="op_single_selector"]
> - [Introducción](vs-active-directory-webapi-getting-started.md)
> - [¿Qué ha ocurrido?](vs-active-directory-webapi-what-happened.md)

En este artículo se proporcionan instrucciones adicionales después de haber agregado Active Directory a un proyecto de WebAPI para ASP.NET mediante el comando **Proyecto > Servicios conectados** de Visual Studio. Si aún no ha agregado el servicio al proyecto, puede hacerlo en cualquier momento.

Para ver los cambios realizados en el proyecto al agregar el servicio conectado, consulte [¿Qué le ha ocurrido a mi proyecto de WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Requerimiento de autenticación para obtener acceso a los controladores

Todos los controladores de su proyecto cuentan ahora con el atributo `[Authorize]`. Este atributo requiere que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios de autenticación para Azure Active Directory](authentication-scenarios.md)
- [Adición de inicio de sesión con Microsoft a una aplicación web ASP.NET](quickstart-v1-aspnet-webapp.md)
