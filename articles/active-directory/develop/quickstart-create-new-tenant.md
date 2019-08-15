---
title: Creación de un inquilino de Azure Active Directory | Microsoft Docs
description: Obtenga más información acerca de cómo crear un inquilino de Azure AD para el registro y la creación de aplicaciones.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 703976e328eabb891e76459785484d929e2c5d3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853150"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Inicio rápido: Configuración de un entorno de desarrollo

La plataforma de identidad de Microsoft permite a los desarrolladores crear aplicaciones destinadas a una amplia variedad de identidades y entornos de Microsoft 365 personalizados. Para comenzar a usar la plataforma de identidad de Microsoft, deberá acceder a un entorno, también denominado inquilino de Azure AD, que pueda registrar y administrar aplicaciones, tendrá acceso a datos de Microsoft 365 e implementará restricciones personalizadas de inquilino y acceso condicional.

Un inquilino es la representación de una organización. Se trata de una instancia dedicada de Azure AD que una organización o el desarrollador de la aplicación reciben cuando crean una relación con Microsoft, como al registrarse en Azure, Microsoft Intune o Microsoft 365.

Cada inquilino de Azure AD es distinto e independiente de los demás inquilinos de Azure AD y tiene su propia representación de identidades profesionales y educativas, identidades de consumidor (si es un inquilino de Azure AD B2C) y registros de aplicaciones. Un registro de aplicación dentro del inquilino puede permitir autenticaciones de cuentas solo dentro de su inquilino o en todos los inquilinos.

## <a name="determining-environment-type"></a>Determinación del tipo de entorno

Hay dos tipos de entornos que puede crear. Para decidir cuál necesita, debe basarse únicamente en los tipos de usuarios que autenticará la aplicación.

* Cuentas profesionales o educativas (cuentas de Azure AD) o cuentas de Microsoft (como outlook.com y live.com)
* Cuentas locales y sociales (Azure AD B2C)

La guía de inicio rápido se divide en dos escenarios según el tipo de aplicación que desea crear. Si necesita más ayuda con un tipo de identidad de destino, eche un vistazo a la sección [Acerca de la plataforma de identidad de Microsoft](about-microsoft-identity-platform.md).

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Cuentas profesionales o educativas o cuentas personales de Microsoft

### <a name="use-an-existing-tenant"></a>Uso de un inquilino existente

Muchos desarrolladores ya tienen inquilinos mediante servicios o suscripciones que están vinculados a inquilinos de Azure AD, como suscripciones de Microsoft 365 o Azure.

1. Para comprobar el inquilino, inicie sesión en [Azure Portal](https://portal.azure.com) con la cuenta que desea usar para administrar la aplicación.
1. Compruebe la esquina superior derecha. Si tiene un inquilino, iniciará sesión de forma automática y verá el nombre de este directamente bajo el nombre de la cuenta.
   * Mantenga el puntero sobre el nombre de su cuenta en el lado superior derecho de Azure Portal para ver el nombre, el correo electrónico, el directorio e identificador de inquilino (un GUID) y el dominio.
   * Si la cuenta está asociada a varios inquilinos, puede seleccionar el nombre de la cuenta para abrir un menú donde puede cambiar entre los inquilinos. Cada inquilino tiene su propio identificador de inquilino.

> [!TIP]
> Si necesita encontrar el identificador del inquilino, puede:
> * Mantener el puntero sobre el nombre de cuenta para obtener el directorio o el identificador de inquilino.
> * Seleccionar **Azure Active Directory > Propiedades > Id. de directorio** en Azure Portal.

Si no tiene un inquilino existente asociado a la cuenta, verá un GUID bajo el nombre de la cuenta y no podrá realizar acciones como el registro de aplicaciones hasta que siga los pasos de la siguiente sección.

### <a name="create-a-new-azure-ad-tenant"></a>Creación de un nuevo inquilino de Azure AD

Si aún no tiene un inquilino de Azure AD o si desea crear uno nuevo para su desarrollo, siga la [experiencia de creación de directorio](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Tendrá que proporcionar la información siguiente para crear el nuevo inquilino:

- **Nombre de la organización**
- **Dominio inicial**: formará parte de *.onmicrosoft.com. El dominio se puede personalizar más adelante.
- **País o región**

> [!NOTE]
> Al asignar un nombre a su inquilino, utilice caracteres alfanuméricos. No se aceptan caracteres especiales. El nombre no puede tener más de 256 caracteres.

## <a name="social-and-local-accounts"></a>Cuentas locales y sociales

Para empezar a crear aplicaciones que inician sesión en cuentas sociales y locales, deberá crear a un inquilino de Azure AD B2C. Para empezar, siga los pasos que se indican en [Creación de un inquilino de Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Pasos siguientes

* Pruebe un inicio rápido de codificación y empiece a autenticar a los usuarios. 
* Para obtener ejemplos de código más detallados, consulte la sección **Tutoriales** de la documentación.
* ¿Desea implementar la aplicación en la nube? Revise la [implementación de contenedores en Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
