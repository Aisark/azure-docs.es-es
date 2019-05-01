---
title: Aplicación de extensiones en Azure Active Directory B2C | Microsoft Docs
description: Restauración de b2c-extensions-app.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: aee21e6ea3c598818091aa11c3d13b9d6172743c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703435"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Aplicación de extensiones

Al crearse un directorio de Azure AD B2C, se crea automáticamente una aplicación denominada `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` dentro del nuevo directorio. Esta aplicación, llamada **b2c-extensions-app**, se puede ver en *Registros de aplicaciones*. El servicio Azure AD B2C la usa para almacenar información sobre usuarios y atributos personalizados. Si se elimina la aplicación, Azure AD B2C no funcionará correctamente y su entorno de producción se verá afectado.

> [!IMPORTANT]
> No elimine b2c-extensions-app a menos que tenga previsto eliminar el inquilino inmediatamente. Si la aplicación permanece eliminada durante más de 30 días, la información del usuario se perderá permanentemente.

## <a name="verifying-that-the-extensions-app-is-present"></a>Comprobación de que la aplicación de extensiones está presente

Para comprobar que b2c-extensions-app está presente:

1. En el inquilino de Azure AD B2C, haga clic en **Todos los servicios** en el menú de navegación izquierdo.
1. Busque y abra **Registros de aplicaciones**.
1. Busque una aplicación que empiece por **b2c-extensions-app**.

## <a name="recover-the-extensions-app"></a>Recuperación de la aplicación de extensiones

Si ha eliminado b2c-extensions-app accidentalmente, tiene 30 días para recuperarlo. Puede restaurar la aplicación mediante Graph API:

1. Vaya a [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Inicie sesión en el sitio como administrador global para el directorio de Azure AD B2C para el que desea restaurar la aplicación eliminada. Este administrador global debe tener una dirección de correo electrónico similar a la siguiente: `username@{yourTenant}.onmicrosoft.com`.
1. Emita HTTP GET en la dirección URL `https://graph.windows.net/myorganization/deletedApplications` con api-version=1.6. Esta operación enumerará todas las aplicaciones que se han eliminado en los últimos 30 días.
1. Busque la aplicación en la lista cuyo nombre comienza por "b2c-extension-app" y copie su valor de propiedad `objectid`.
1. Emita HTTP POST en la dirección URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Reemplace la parte `{OBJECTID}` de la dirección URL por `objectid` del paso anterior. 

Ahora debería poder [ver la aplicación restaurada](#verifying-that-the-extensions-app-is-present) en Azure Portal.

> [!NOTE]
> Una aplicación solo se puede restaurar si se ha eliminado en los últimos 30 días. Si han pasado más de 30 días, los datos se perderán permanentemente. Para obtener más ayuda, abra una incidencia de soporte técnico.
