---
title: Administración del acceso a la administración de Azure con acceso condicional en Azure Active Directory
description: Obtenga más información sobre el uso del acceso condicional en Azure AD para administrar el acceso a la administración de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: d4036b2aaa59eeb8e38a5881e465031d7a2f15e3
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899723"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Administración el acceso a la administración de Azure con acceso condicional

El acceso condicional en Azure Active Directory (Azure AD) controla el acceso a las aplicaciones en la nube en función de las condiciones concretas que especifique. Para permitir el acceso, cree directivas de acceso condicional que permitan o bloqueen el acceso en función de si se cumplen los requisitos de la directiva. 

Normalmente, se utiliza el acceso condicional para controlar el acceso a las aplicaciones en la nube. También puede configurar directivas para controlar el acceso a la administración de Azure en función de ciertas condiciones (como el riesgo de inicio de sesión, la ubicación o el dispositivo) y para exigir requisitos como la autenticación multifactor.

Para crear una directiva de administración de Azure, seleccione **Microsoft Azure Management** en **Aplicaciones en la nube** al elegir la aplicación a la que se va a aplicar la directiva.

![Acceso condicional para la administración de Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

La Directiva que se crea se aplica a todos los puntos de conexión de administración de Azure, incluidos los siguientes:

- Portal de Azure
- Proveedor de Azure Resource Manager
- Service Management API clásicas
- Azure PowerShell
- Portal de administrador de suscripciones de Visual Studio
- Azure DevOps
- Portal de Azure Data Factory

Tenga en cuenta que la directiva se aplica a Azure PowerShell, que llama a la API de Azure Resource Manager. No es aplicable a [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), que llama a Microsoft Graph.

> [!CAUTION]
> Asegúrese de comprender cómo funciona el acceso condicional antes de configurar una directiva para administrar el acceso a la administración de Azure. Asegúrese de no crear condiciones que pudieran bloquear su propio acceso al portal.

Para obtener más información sobre cómo configurar y usar el acceso condicional, vea [Acceso condicional de Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
