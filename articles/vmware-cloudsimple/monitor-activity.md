---
title: 'Azure VMware Solutions (AVS): supervisión de la actividad de la nube privada de AVS'
description: Describe la información disponible sobre las actividades en el entorno de Azure VMware Solution by AVS, que incluye alertas, eventos, tareas y auditorías.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019679"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Supervisión de la actividad de VMware Solutions (AVS)

Los registros de actividad de AVS proporcionan una visión general de las operaciones realizadas en el portal de AVS. La lista incluye alertas, eventos, tareas y auditorías. Use los registros de actividad para determinar quién, cuándo y qué operaciones se realizaron. Los registros de actividad no incluyen las operaciones de lectura completadas por un usuario.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Acceso al portal de AVS

Acceso al [portal de AVS](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Información de la actividad

Para acceder a las páginas de actividad, seleccione **Actividad** en el menú lateral.

![Información general de la página Actividad](media/activity-page-overview.png)

Para ver detalles acerca de cualquiera de las actividades de la página Actividad, seleccione la actividad. Se abre un panel de detalles a la derecha. Las acciones del panel dependen del tipo de actividad. Haga clic en **X** para cerrar el panel.

Haga clic en un encabezado de columna para ordenar la visualización. Puede filtrar las columnas para ver valores específicos. Para descargar el informe de actividades, haga clic en el icono **Descargar como CSV**.

## <a name="alerts"></a>Alertas

Las alertas son notificaciones de cualquier actividad significativa en el entorno de AVS. Las alertas incluyen eventos que afectan a la facturación o al acceso de los usuarios.

Para confirmar las alertas y quitarlas de la lista, seleccione una o más de la lista y haga clic en **Confirmar**.

Las siguientes columnas de información están disponibles para las alertas. Haga clic en **Editar columnas** y seleccione las columnas que quiere ver.

| Columna | Descripción |
------------ | ------------- |
| Tipo de alerta | Categoría de la alerta.|
| Time | Hora en que se produjo la alerta. |
| severity | Importancia de la alerta.|
| Nombre de recurso | Nombre asignado al recurso, como el nombre de la nube privada de AVS. |
| Tipo de recurso | Categoría del recurso: Nube privada de AVS, bastidor de nube. |
| Id. de recurso | Identificador del recurso. |
| Descripción | Descripción de lo que desencadenó la alerta. |
| Confirmado | Indicación de si se ha confirmado la alerta. |

## <a name="events"></a>Eventos

Los eventos muestran la actividad del usuario y del sistema en el portal de AVS. La página Eventos muestra la actividad asociada a un recurso específico y la gravedad del impacto.

Las siguientes columnas de información están disponibles para las alertas. Haga clic en **Editar columnas** y seleccione las columnas que quiere ver.

| Columna | Descripción |
------------ | ------------- |
| Time | Fecha y hora en que se produjo el evento. |
| Tipo de evento | Código numérico que identifica el evento. |
| severity | Gravedad del evento.|
| Nombre de recurso | Nombre asignado al recurso, como el nombre de la nube privada de AVS. |
| Tipo de recurso | Categoría del recurso: Nube privada de AVS, bastidor de nube. |
| Descripción | Descripción de lo que desencadenó la alerta. |

## <a name="tasks"></a>Tareas

Las tareas son actividades de la nube privada de AVS que se espera tarden 30 segundos o más en completarse. (Las actividades que se espera que tarden menos de 30 segundos solo se muestran como eventos). Abra la página Tareas para hacer un seguimiento del progreso de las tareas de la nube privada de AVS.

Las siguientes columnas de información están disponibles para las alertas. Haga clic en **Editar columnas** y seleccione las columnas que quiere ver.

| Columna | Descripción |
------------ | ------------- |
| Id. de tarea | Identificador único para la tarea. |
| Operación | Acción que realiza la tarea. |
| Usuario | Usuario asignado para completar la tarea. |
| Nombre de recurso | Nombre asignado al recurso. |
| Tipo de recurso | Categoría del recurso: Nube privada de AVS, bastidor de nube. |
| Id. de recurso | Identificador del recurso. |
| Start | Hora de inicio de la tarea. |
| End | Hora de finalización de la tarea. |
| Status | Estado actual de la tarea. |
| Tiempo transcurrido | Tiempo en que tardó en completarse la tarea (si se completa) o que está tomando (si está en curso). |
| Descripción | Descripción de la tarea. |

## <a name="audit"></a>Auditoría

Los registros de auditoría hacen un seguimiento de la actividad del usuario. Puede usar los registros de auditoría para supervisar la actividad de usuario de todos los usuarios.

Las siguientes columnas de información están disponibles para las alertas. Haga clic en **Editar columnas** y seleccione las columnas que quiere ver.

| Columna | Descripción |
------------ | ------------- |
| Time | Hora de la entrada de auditoría. |
| Operación | Acción que realiza la tarea. |
| Usuario | Usuario asignado a la tarea. |
| Nombre de recurso | Nombre asignado al recurso. |
| Tipo de recurso | Categoría del recurso: Nube privada de AVS, bastidor de nube. |
| Id. de recurso | Identificador del recurso. |
| Resultado | Resultado de la actividad, como **Correcto**. |
| Tiempo empleado | Tiempo para completar la tarea. |
| Descripción | Descripción de la acción. |

## <a name="next-steps"></a>Pasos siguientes

* [Uso de máquinas virtuales de VMware en Azure](quickstart-create-vmware-virtual-machine.md)
* Más información sobre las [nubes privadas de AVS](cloudsimple-private-cloud.md)
