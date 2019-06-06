---
title: Panel de información general de Azure Application Insights | Microsoft Docs
description: Supervise las aplicaciones con Azure Application Insights y la funcionalidad del panel de información general.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497166"
---
# <a name="application-insights-overview-dashboard"></a>Panel de información general de Application Insights

Application Insights siempre ha proporcionado un panel de información general de resumen para permitir una valoración rápida a simple vista del mantenimiento y el rendimiento de una aplicación. El nuevo panel de información general proporciona una experiencia más rápida y flexible.

## <a name="how-do-i-test-out-the-new-experience"></a>¿Cómo se prueba la nueva experiencia?

Ahora, el nuevo panel de información general se inicia de forma predeterminada:

![Panel de información general (versión preliminar)](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Mejor rendimiento

Se ha simplificado la selección de intervalo de tiempo a favor de una interfaz más sencilla con un solo clic.

![Intervalo de tiempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

El rendimiento en general ha aumentado enormemente. Se tiene acceso con un solo clic a funciones populares como **Buscar** y **Análisis**. Cada mosaico de KPI de actualización dinámica predeterminado proporciona información de las funciones correspondientes de Application Insights. Para obtener más información acerca de las solicitudes erróneas, seleccione **Errores** en el encabezado **Investigar**:

![Errores](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Panel de la aplicación

El panel de la aplicación aprovecha la tecnología de paneles existente en Azure para proporcionar una única vista en el panel, totalmente personalizable, del mantenimiento y el rendimiento de la aplicación.

Para acceder al panel predeterminado, seleccione _Panel de la aplicación_ en la esquina superior izquierda.

![Vista de panel](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Si esta es la primera vez que accede al panel, se iniciará una vista predeterminada:

![Vista de panel](./media/overview-dashboard/0001-dashboard.png)

Puede conservar la vista predeterminada si así lo desea. Aunque también puede agregar o eliminar elementos del panel para adaptarlo mejor a las necesidades de su equipo.

> [!NOTE]
> Todos los usuarios con acceso al recurso de Application Insights comparten el mismo panel de la aplicación. Los cambios que realice cualquiera de ellos modificarán la vista de todos.

Para volver a la experiencia de información general, simplemente seleccione:

![Botón Información general](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>solución de problemas

Si selecciona **configurar las opciones de mosaico** y establezca un intervalo de tiempo personalizado que supere los 31 días, no se mostrará el panel más allá de 31 días de datos, incluso con la retención de datos predeterminado de 90 días. Actualmente no hay ninguna solución alternativa para este comportamiento.

## <a name="next-steps"></a>Pasos siguientes

- [Embudos](../../azure-monitor/app/usage-funnels.md)
- [Retención](../../azure-monitor/app/usage-retention.md)
- [Flujos de usuario](../../azure-monitor/app/usage-flows.md)