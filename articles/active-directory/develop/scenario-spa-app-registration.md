---
title: 'Aplicación de página única (registro de la aplicación): Plataforma de identidad de Microsoft'
description: Aprenda a compilar una aplicación de página única (registro de la aplicación)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074836"
---
# <a name="single-page-application---app-registration"></a>Aplicación de página única: registro de la aplicación

En esta página se explican los detalles del registro de la aplicación para una aplicación de página única (SPA).

Siga los pasos para [registrar una nueva aplicación en la Plataforma de identidad de Microsoft](quickstart-register-app.md) y seleccione las cuentas admitidas para la aplicación. El escenario de SPA puede admitir la autenticación con cuentas de su organización o cuentas de cualquier organización y personales de Microsoft.

A continuación, obtenga información sobre los aspectos específicos del registro de una aplicación que se aplican a las aplicaciones de página única.

## <a name="register-a-redirect-uri"></a>Registrar un URI de redireccionamiento

El flujo implícito envía los tokens en un redirección a la aplicación de página única que se ejecuta en un explorador web. Por lo tanto, es un requisito importante registrar un URI de redireccionamiento adonde la aplicación pueda recibir los tokens. Asegúrese de que el URI de redireccionamiento coincida exactamente con el URI de su aplicación.

En [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908), vaya a la aplicación registrada, en la página **Autenticación** de la aplicación, seleccione la plataforma **Web** y escriba el valor del URI de redireccionamiento de la aplicación en el campo **URI de redirección**.

## <a name="enable-the-implicit-flow"></a>Habilitar el flujo implícito

En la misma página **Autenticación**, en **Configuración avanzada**, también debe habilitar la **Concesión implícita**. Si la aplicación solo se encarga del inicio de sesión de los usuarios y de obtener tokens de identificador, es suficiente con habilitar la casilla **Tokens de id.**

Si la aplicación también necesita obtener los tokens de acceso para llamar a las API, asegúrese de habilitar la casilla **Tokens de acceso**. Para más información, consulte los [tokens de identificador](./id-tokens.md) y los [tokens de acceso](./access-tokens.md).

## <a name="api-permissions"></a>Permisos de API

Las aplicaciones de página única pueden llamar a las API en nombre del usuario con sesión iniciada. Tienen que solicitar permisos delegados. Para más detalles, consulte [Adición de permisos para acceder a las API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración del código de la aplicación](scenario-spa-app-configuration.md)
