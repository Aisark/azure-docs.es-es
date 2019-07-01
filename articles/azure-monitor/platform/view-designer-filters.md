---
title: Filtros en las vistas de Azure Monitor | Microsoft Docs
description: Un filtro de una vista de Azure Monitor permite a los usuarios filtrar los datos de la vista por el valor de una propiedad determinada sin modificar la vista propiamente dicha.  En este artículo se describe cómo usar un filtro y agregar uno a una vista personalizada.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551743"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros en las vistas de Azure Monitor
Un **filtro** de una [vista de Azure Monitor](view-designer.md) permite a los usuarios filtrar los datos de la vista por el valor de una propiedad determinada sin modificar la vista propiamente dicha.  Por ejemplo, podría permitir que los usuarios de la vista la filtraran por los datos de solo un determinado equipo o conjunto de equipos.  Puede crear varios filtros en una sola vista para permitir que los usuarios filtren por varias propiedades.  En este artículo se describe cómo usar un filtro y agregar uno a una vista personalizada.

## <a name="using-a-filter"></a>Uso de un filtro
Haga clic en el intervalo de hora y fecha en la parte superior de una vista para abrir el menú desplegable donde se puede cambiar dicho intervalo para la vista.

![Ejemplo de filtro](media/view-designer-filters/filters-example-time.png)

Haga clic en **+** para agregar un filtro mediante filtros personalizados definidos para la vista. Seleccione un valor para el filtro de la lista desplegable o escriba un valor. Para seguir agregando filtros, haga clic en **+** . 


![Ejemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Si quita todos los valores de un filtro, ese filtro dejará de aplicarse.


## <a name="creating-a-filter"></a>Creación de un filtro

Cree un filtro en la pestaña **Filtros** al [editar una vista](view-designer.md).  El filtro es global para la vista y se aplica a todas sus partes.  

![Configuración de filtros](media/view-designer-filters/filters-settings.png)

En la tabla siguiente se describe la configuración de un filtro.

| Configuración | DESCRIPCIÓN |
|:---|:---|
| Nombre del campo | Nombre del campo usado para filtrar.  Este campo debe coincidir con el campo de resumen de **Consultar valores**. |
| Consultar valores | Consulta que se ejecutará para rellenar la lista desplegable de filtros del usuario.  Esta consulta debe usar [summarize](/azure/kusto/query/summarizeoperator) o [distinct](/azure/kusto/query/distinctoperator) para proporcionar valores únicos para un campo determinado y debe coincidir con el valor de **Nombre de campo**.  Puede usar [sort](/azure/kusto/query/sortoperator) para ordenar los valores que se muestran al usuario. |
| Etiqueta | Nombre del campo que se usa en las consultas que admiten el filtro y también se muestra al usuario. |

### <a name="examples"></a>Ejemplos

En la tabla siguiente se incluyen algunos ejemplos de filtros comunes.  

| Nombre del campo | Consultar valores | Etiqueta |
|:--|:--|:--|
| Equipo   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Equipos |
| EventLevelName | Event &#124; distinct EventLevelName | Gravedad |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Gravedad |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificación de las consultas de vista

Para que un filtro surta efecto, debe modificar las consultas de la vista para filtrar por los valores seleccionados.  Si no modifica las consultas de la vista, los valores que el usuario seleccione no tendrán ningún efecto.

La sintaxis para usar un valor de filtro en una consulta es la siguiente: 

    where ${filter name}  

Por ejemplo, si la vista tiene una consulta que devuelve eventos y usa un filtro llamado _Equipos_, podría usar la siguiente consulta.

    Event | where ${Computers} | summarize count() by EventLevelName

Si agregara otro filtro denominado Gravedad, podría usar la siguiente consulta para utilizar ambos filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Pasos siguientes
* Aprenda más sobre los [elementos de visualización](view-designer-parts.md) que puede agregar a la vista personalizada.
