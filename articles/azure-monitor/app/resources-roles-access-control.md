---
title: Recursos, roles y control de acceso en Azure Application Insights | Microsoft Docs
description: Propietarios, colaboradores y lectores de las perspectivas de su organización.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 0f348e3e7dc2812bf354d1f8ec86330b0742439a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60373715"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, roles y control de acceso en Application Insights

Puede controlar quién tiene acceso de lectura y actualización a sus datos en Azure [Application Insights][start], mediante el uso del [control de acceso basado en rol de Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Asigne acceso a los usuarios para el **grupo de recursos o la suscripción** a los que pertenece el recurso de aplicación, no para el propio recurso. Asigne el rol de **colaborador de componentes de Application Insights** . De esta forma, se garantiza el control de acceso uniforme a las alertas y las pruebas web junto con su recurso de aplicación. [Más información](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos y suscripciones

En primer lugar, vamos a ver algunas definiciones:

* **Recurso** : una instancia de un servicio de Microsoft Azure. El recurso de Application Insights recopila, analiza y muestra los datos de telemetría enviados desde su aplicación.  Otros tipos de recursos de Azure son aplicaciones web, bases de datos y máquinas virtuales.
  
    Para ver todos los recursos, vaya a [Azure Portal][portal], inicie sesión y haga clic en Todos los recursos. Para buscar un recurso, escriba parte del nombre en el campo de filtro.
  
    ![Enumeración de los recursos de Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group]: cada recurso pertenece a un grupo. Un grupo es una forma cómoda de administrar los recursos relacionados, especialmente de cara al control de acceso. Por ejemplo, en un grupo de recursos podría colocar una aplicación web, un recurso de Application Insights para supervisar la aplicación y un recurso de almacenamiento para mantener los datos exportados.

* [**Suscripción**](https://portal.azure.com): para usar Application Insights u otros recursos de Azure, inicie sesión en una suscripción de Azure. Cada grupo de recursos pertenece a una suscripción de Azure, donde elije su paquete de precios y, si se trata de una suscripción de la organización, selecciona los miembros y sus permisos de acceso.
* [**Cuenta de Microsoft**][account]: el nombre de usuario y la contraseña que usa para iniciar sesión en suscripciones de Microsoft Azure, XBox Live, Outlook.com y otros servicios de Microsoft.

## <a name="access"></a> Control de acceso para el grupo de recursos

Es importante comprender que, además del recurso que ha creado para su aplicación, también hay recursos ocultos independientes para las alertas y las pruebas web. Estos están conectados al mismo [grupo de recursos](#resource-group) que el recurso de Application Insights. También podría haber colocado ahí otros servicios de Azure, como sitios web o almacenamiento.

Para controlar el acceso a estos recursos, se recomienda por lo tanto lo siguiente:

* Controlar el acceso en el nivel de **grupo de recursos o suscripción** .
* Asignar el rol de **colaborador de componentes de Application Insights** a los usuarios. Esto les permite editar pruebas web, alertas y recursos de Application Insights, sin proporcionar acceso a otros servicios en el grupo.

## <a name="to-provide-access-to-another-user"></a>Para proporcionar acceso a otro usuario, siga estos pasos:

Debe tener derechos de propietario a la suscripción o al grupo de recursos.

El usuario debe tener una [cuenta de Microsoft][account] o tener acceso a su [cuenta de Microsoft de organización](../../active-directory/fundamentals/sign-up-organization.md). Puede proporcionar acceso individual y también a grupos de usuarios definidos en Active Directory de Azure.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Vaya al grupo de recursos o directamente al recurso mismo.

Elija **Control de acceso (IAM)** en el menú de la izquierda.

![Captura de pantalla del botón Control de acceso en Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Seleccione **Agregar asignación de roles**.

![Captura de pantalla del menú Control de acceso con el botón Agregar resaltado en rojo](./media/resources-roles-access-control/0002-add.png)

La vista **Agregar permisos** a continuación es principalmente específica a los recursos de Application Insights, si estuviera viendo los permisos de control de acceso de un nivel superior, como grupos de recursos, vería que roles adicionales no centrados en Application Insights.

Para ver la información sobre todos los roles integrados de control de acceso basado en rol de Azure, use el [contenido de referencia oficial](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Captura de pantalla de la lista de roles de usuario de Control de acceso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Seleccione un rol.

Cuando corresponda, tendrá un vínculo a la documentación de referencia oficial asociada.

| Rol | En el grupo de recursos: |
| --- | --- |
| [Propietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Puede cambiar cualquier cosa, incluido el acceso de usuario. |
| [Colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Puede editar cualquier cosa, incluidos todos los recursos. |
| [Colaborador de componentes de Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Puede editar alertas, pruebas web y recursos de Application Insights. |
| [Lector](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Puede ver, pero no puede cambiar nada. |
| [Depurador de instantáneas de Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Concede permiso al usuario para usar las características de Application Insights Snapshot Debugger. Tenga en cuenta que este rol no se incluye en los roles de propietario ni colaborador. |
| Colaborador para la administración de versiones de implementación de servicios de Azure | Rol de colaborador para servicios que se implementan a través de implementación de servicios de Azure. |
| [Purgador de datos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Rol especial para purgar datos personales. Para más información, consulte nuestros [guía para datos personales](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data).   |
| Administrador de ExpressRoute | Puede crear, eliminar y administrar rutas rápidas.|
| [Colaborador de Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Un colaborador de Log Analytics puede leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure.  |
| [Lector de Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Un lector de Log Analytics puede ver y buscar todos los datos de supervisión, así como consultar la configuración de supervisión, incluida la de Azure Diagnostics en todos los recursos de Azure. |
| masterreader | Permite a un usuario ver todo el contenido, pero no realizar cambios. |
| [Colaborador de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Puede leer todos los datos de supervisión y actualizar la configuración de esta. |
| [Supervisión del publicador de métricas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Permite publicar las métricas de los recursos de Azure. |
| [Lector de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Puede leer todos los datos de supervisión. |
| Colaborador de la directiva de recursos (versión preliminar) | Los usuarios repuestos de EA, con derechos para crear o modificar la directiva de recursos, crean incidencias de soporte técnico y leen los recursos o la jerarquía.  |
| [Administrador de acceso de usuario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Permite a un usuario administrar el acceso de otros usuarios a los recursos de Azure.|
| [Colaborador de sitio web](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Permite administrar los sitios web (no planes web), pero no acceder a ellos.|

La "edición" incluye la creación, la eliminación y la actualización:

* Recursos
* Pruebas web
* Alertas
* Exportación continua

#### <a name="select-the-user"></a>Seleccione el usuario.

Si el usuario de su elección no está en el directorio, puede invitar a cualquier persona con una cuenta Microsoft.
(Si se usan servicios como Outlook.com, OneDrive, Windows Phone o XBox Live, se tiene una cuenta Microsoft).

## <a name="related-content"></a>Contenido relacionado

* [Control de acceso basado en rol de Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Consulta de PowerShell para determinar la pertenencia a roles

Como algunos roles pueden vincularse a notificaciones y alertas de correo electrónico, puede resultar útil poder generar una lista de los usuarios que pertenecen a un determinado rol. Para ayudar con la generación de estos tipos de listas, le ofrecemos las siguientes consultas de ejemplo, que puede adaptar a sus necesidades específicas:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Suscripción completa a consultas para roles de administradores y roles de colaborador

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Consulta en el contexto de un recurso específico de Application Insights para propietarios y colaboradores

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Consulta en el contexto de un grupo de recursos específico para propietarios y colaboradores

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
