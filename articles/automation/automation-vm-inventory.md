---
title: Administración de una máquina virtual de Azure con la recopilación de inventario | Microsoft Docs
description: Administración de una máquina virtual con la recopilación de inventario
services: automation
ms.subservice: change-inventory-management
keywords: inventario, automatización, cambio, seguimiento
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844826"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Administración de una máquina virtual de Azure con la recopilación de inventario

Puede habilitar el seguimiento del inventario para una máquina virtual de Azure desde la página de recursos de esta. Puede recopilar y ver la siguiente información de inventario en los equipos:

- software de Windows (aplicaciones Windows y actualizaciones de Windows), servicios, archivos y claves del registro;
- archivos y demonios de software de Linux (paquetes).

Este método proporciona una interfaz de usuario basada en explorador para instalar y configurar la recopilación de inventario.

## <a name="before-you-begin"></a>Antes de empezar

Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

En este artículo se da por supuesto que tiene una máquina virtual activada para configurar la solución. Si no tiene una máquina virtual de Azure, cree una [máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitación de la recopilación de inventario desde la página de recursos de la máquina virtual

1. En el panel izquierdo de Azure Portal, seleccione **Máquinas virtuales**.
2. En la lista de máquinas virtuales, seleccione una máquina virtual.
3. En el menú **Recurso**, en **Operaciones**, seleccione **Inventario**.
4. Seleccione un área de trabajo de Log Analytics para almacenar los registros de datos.
    Si no hay áreas de trabajo disponibles para esa región, deberá crear una predeterminada y una cuenta de Automation.
5. Seleccione **Habilitar** para iniciar la incorporación al equipo.

   ![Vista de las opciones de incorporación](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Una barra de estado le notifica que la solución se está habilitando. Este proceso puede tardar hasta 15 minutos. Durante este tiempo, puede cerrar la ventana o mantenerla abierta, y se le notificará cuando la solución esté habilitada. Puede supervisar el estado de la implementación desde el panel de notificaciones.

   ![Vista de la solución de inventario inmediatamente después de la incorporación](./media/automation-vm-inventory/inventory-onboarded.png)

Una vez completada la implementación, la barra de estado desaparece. El sistema sigue recopilando datos de inventario y puede que estos aún no se vean. La recopilación de datos puede tardar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configuración del inventario

De forma predeterminada, el software, los servicios de Windows y los demonios de Linux están configurados para la recopilación. Para recopilar el registro de Windows y el inventario de archivos, configure las opciones de recopilación de inventario.

1. En la vista **Inventario**, seleccione el botón **Editar configuración** de la parte superior de la ventana.
2. Para agregar una nueva configuración de recopilación, vaya a la categoría de configuración que desee agregar seleccionando las pestañas **Registro de Windows**, **Archivos de Windows** y **Archivos de Linux**.
3. Seleccione la categoría correspondiente y haga clic en **Agregar** en la parte superior de la ventana.

Las tablas siguientes proporcionan información sobre cada propiedad que se puede configurar para las distintas categorías.

### <a name="windows-registry"></a>Registro de Windows

|Propiedad  |Descripción  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Clave del registro de Windows   | La ruta de acceso para buscar el archivo, por ejemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Archivos de Windows

|Propiedad  |Descripción  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: "c:\temp\myfile.txt"

### <a name="linux-files"></a>Archivos de Linux

|Propiedad  |Descripción  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: "/etc/*.conf"       |
|Tipo de ruta de acceso     | Tipo de elemento cuyo seguimiento se va a realizar; posibles valores son Archivo y Directorio        |
|Recursividad     | Determina si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar.        |
|Usar sudo     | Esta configuración determina si se va a utilizar sudo al buscar el elemento.         |
|Vínculos     | Esta configuración determina cómo se tratan los vínculos simbólicos cuando se recorren directorios.<br> **Omitir**: ignora los vínculos simbólicos y no incluye los archivos y directorios de referencia.<br>**Seguir**: sigue los vínculos simbólicos durante la recursión y también incluye los archivos y directorios de referencia.<br>**Administrar**: sigue los vínculos simbólicos y permite modificar el tratamiento del contenido devuelto.      |

## <a name="manage-machine-groups"></a>Administración de grupos de máquinas

El inventario le permite crear y ver grupos de máquinas de los registros de Azure Monitor. Los grupos de máquinas son colecciones de máquinas definidas por una consulta en los registros de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para ver los grupos de máquinas, seleccione la pestaña **Grupos de máquinas** en la página Inventario.

![Visualización de grupos de máquinas en la página Inventario](./media/automation-vm-inventory/inventory-machine-groups.png)

Si selecciona un grupo de máquinas de la lista se abrirá la página Grupos de máquinas. Esta página muestra detalles sobre el grupo de máquinas. Estos detalles incluyen la consulta de Log Analytics que se usa para definir el grupo. En la parte inferior de la página, hay una lista paginada de las máquinas que forman parte de ese grupo.

![Ver página Grupo de máquinas](./media/automation-vm-inventory/machine-group-page.png)

Haga clic en el botón **+ Clonar** para clonar el grupo de máquinas. Aquí debe asignar al grupo un nuevo nombre y un alias. La definición se puede modificar en este momento. Después de cambiar la consulta, presione **Validar consulta** para obtener una vista previa de las máquinas que se pueden seleccionar. Cuando esté satisfecho con el grupo, haga clic en **Crear** para crear el grupo de máquinas

Si quiere crear un nuevo grupo de máquinas, seleccione **+ Create a machine group** (+ Crear un grupo de máquinas). Este botón abre la **página Crear un nuevo grupo de máquinas** en la que puede definir el nuevo grupo. Haga clic en **Crear** para crear el grupo.

![Crear grupo de máquinas](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Interrupción de la administración de la máquina virtual

Para dejar de administrar el inventario de la máquina virtual:

1. En el panel izquierdo de Azure Portal, seleccione **Log Analytics** y, a continuación, el área de trabajo que usó durante la incorporación a la máquina virtual.
2. En la ventana **Log Analytics**, en la categoría **Orígenes de datos del área de trabajo** del menú **Recurso**, seleccione **Máquinas virtuales**.
3. En la lista, seleccione la máquina virtual que desee desconectar. La máquina virtual tiene una marca de verificación verde junto a **Esta área de trabajo** en la columna **Conexión a OMS**.

   >[!NOTE]
   >OMS ahora se conoce como registros de Azure Monitor.
   
4. En la parte superior de la página siguiente, seleccione **Desconectar**.
5. En la ventana de confirmación, seleccione **Sí**.
    Esta acción interrumpe la administración de la máquina.

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de cómo administrar los cambios de configuración en los archivos y el registro de las máquinas virtuales, consulte [Seguimiento de cambios de software en su entorno con la solución de seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md).
* Para más información sobre cómo administrar Windows y actualizaciones de paquetes en las máquinas virtuales, consulte [Solución Update Management en Azure](../operations-management-suite/oms-solution-update-management.md).

