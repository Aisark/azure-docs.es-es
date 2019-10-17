---
title: Incorporación de una entidad de servicio al rol de administrador del servidor de Azure Analysis Services | Microsoft Docs
description: Aprenda cómo agregar una entidad de servicio de automatización al rol de administrador del servidor
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e4650d3be5ce21a49b419577eaf83225b1c0d4d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298723"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Incorporación de una entidad de servicio al rol de administrador del servidor 

 Para automatizar las tareas de PowerShell desatendidas, una entidad de servicio debe tener privilegios de **administrador del servidor** en el servidor de Analysis Services que se está administrando. En este artículo se describe cómo agregar una entidad de servicio al rol de administrador de servidor en un servidor de Azure AS.

## <a name="before-you-begin"></a>Antes de empezar
Antes de completar esta tarea, debe tener registrada una entidad de servicio en Azure Active Directory.

[Creación de una entidad de servicio: Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Creación de una entidad de servicio: PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Permisos necesarios
Para completar esta tarea, debe tener permisos de [administrador del servidor](analysis-services-server-admins.md) en el servidor Azure AS. 

## <a name="add-service-principal-to-server-administrators-role"></a>Incorporación de una entidad de servicio al rol de administradores del servidor

1. En SSMS, conéctese a su servidor Azure AS.
2. En **Propiedades del servidor** > **Seguridad**, haga clic en **Agregar**.
3. En **Seleccione un usuario o grupo**, busque la aplicación registrada por su nombre, selecciónela y, a continuación, haga clic en **Agregar**.

    ![Búsqueda de la cuenta de la entidad de servicio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Compruebe el identificador de la cuenta de la entidad de servicio y, a continuación, haga clic en **Aceptar**.
    
    ![Búsqueda de la cuenta de la entidad de servicio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para las operaciones del servidor que usan cmdlets de Azure PowerShell, la entidad de servicio que ejecuta el programador también debe pertenecer al rol **Propietario** para el recurso en [Control de acceso basado en rol (RBAC) de Azure](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Información relacionada

* [Descarga del módulo de PowerShell de SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Descarga de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


