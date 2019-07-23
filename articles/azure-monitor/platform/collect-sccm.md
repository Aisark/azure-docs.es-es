---
title: Conexión de Configuration Manager con Azure Monitor | Microsoft Docs
description: En este artículo se muestran los pasos para conectar Configuration Manager con un área de trabajo en Azure Monitor y empezar a analizar datos.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922880"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conexión de Configuration Manager con Azure Monitor
Puede conectar el entorno de System Center Configuration Manager con Azure Monitor para sincronizar los datos de recopilación del dispositivo y hacer referencia a estas recopilaciones en Azure Monitor y Azure Automation.  

## <a name="prerequisites"></a>Requisitos previos

Azure Monitor es compatible con la rama actual de System Center Configuration Manager, versión 1606 y posteriores.  

## <a name="configuration-overview"></a>Información general sobre la configuración
Los pasos siguientes son un resumen de los pasos necesarios para configurar la integración de Configuration Manager con Azure Monitor.  

1. En Azure Portal, registre Configuration Manager como una aplicación web o una aplicación de API web y asegúrese de que tiene el identificador de cliente y la clave secreta de cliente en el registro de Azure Active Directory. Consulte [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](../../active-directory/develop/howto-create-service-principal-portal.md) para obtener información detallada sobre cómo realizar este paso.
2. En Azure Portal, [proporcione a Configuration Manager (la aplicación web registrada) permiso para acceder a Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. En Configuration Manager, agregue una conexión mediante el Asistente para agregar una conexión de OMS.
4. En Configuration Manager, actualice las propiedades de conexión si la clave secreta de cliente o la contraseña expiran o se pierden.
5. [Descargue e instale Microsoft Monitoring Agent](#download-and-install-the-agent) en el equipo que ejecuta el rol de sistema de sitio de punto de conexión del servicio de Configuration Manager. El agente envía datos de Configuration Manager al área de trabajo de Log Analytics en Azure Monitor.
6. En Azure Monitor, [importe recopilaciones de Configuration Manager](#import-collections) como grupos de equipos.
7. En Azure Monitor, visualice datos de Configuration Manager como [grupos de equipos](computer-groups.md).

Puede obtener más información sobre cómo conectar Configuration Manager a Azure Monitor si consulta [Sincronización de datos de Configuration Manager al área de trabajo de Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Concesión de permisos a Configuration Manager para acceder a Log Analytics
En el procedimiento siguiente, concede el rol *Colaborador* del área de trabajo de Log Analytics a la aplicación de AD y a la entidad de servicio que creó anteriormente para Configuration Manager.  Si no dispone de un área de trabajo, consulte [Creación de un área de trabajo en Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) antes de continuar.  Esto permite a Configuration Manager autenticarse y conectarse a su área de trabajo de Log Analytics.  

> [!NOTE]
> Debe especificar permisos en el área de trabajo de Log Analytics para Configuration Manager. De lo contrario, recibirá un mensaje de error cuando utilice el Asistente para configuración de Configuration Manager.
>

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione el área de trabajo que desea modificar.
3. En el panel izquierdo, seleccione **Access Control (IAM)** .
4. En la página de Access Control (IAM), haga clic en **Agregar asignación de roles** y aparecerá el panel **Agregar asignación de roles**.
5. En el panel **Agregar asignación de roles**, en la lista desplegable **Rol**, seleccione el rol **Colaborador**.  
6. En la lista desplegable **Asignar acceso a**, seleccione la aplicación de Configuration Manager que creó en AD anteriormente y, a continuación, haga clic en **Aceptar**.  

## <a name="download-and-install-the-agent"></a>Descarga e instalación del agente
Revise el artículo [Conexión de equipos Windows a Azure Monitor en Azure](agent-windows.md) para entender los métodos disponibles a la hora de instalar Microsoft Monitoring Agent en el equipo que hospeda el rol del sistema de sitio de punto de conexión del servicio Configuration Manager.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Agregar una conexión de Log Analytics a Configuration Manager
Para agregar una conexión de Log Analytics, el entorno de Configuration Manager necesita tener un [punto de conexión de servicio](https://technet.microsoft.com/library/mt627781.aspx) configurado para el modo en línea.

1. En el área de trabajo **Administración** de Configuration Manager, seleccione **OMS Connector** (Conector de OMS). Se abrirá el **Asistente para agregar una conexión de Log Analytics**. Seleccione **Next** (Siguiente).

   >[!NOTE]
   >OMS ahora se conoce como Log Analytics, que es una característica de Azure Monitor.
   
2. En la pantalla **General**, confirme que ha realizado las siguientes acciones y que tiene los detalles de cada elemento; después, seleccione **Siguiente**.

   1. En Azure Portal, ha registrado Configuration Manager como una aplicación web o una aplicación de API web y se ha asegurado de que tiene el [identificador de cliente en el registro](../../active-directory/develop/quickstart-register-app.md).
   2. En Azure Portal, ha creado una clave secreta de aplicación para la aplicación registrada en Azure Active Directory.  
   3. En Azure Portal, proporcionó a la aplicación web registrada permiso para acceder al área de trabajo de Log Analytics en Azure Monitor.  
      ![Conexión a la página General del Asistente para Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. En la pantalla **Azure Active Directory**, establezca la configuración de conexión al área de trabajo de Log Analytics proporcionando su **inquilino**, el **identificador de cliente** y la **clave secreta de cliente**; después, seleccione **Siguiente**.  
   ![Conexión a la página Azure Active Directory del Asistente para Log Analytics](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Si ha realizado todos los procedimientos correctamente, la información de la pantalla **OMS Connection Configuration** (Configuración de conexión de OMS) aparecerá automáticamente en esta página. Debe aparecer la información de la configuración de conexión para su **suscripción de Azure**, **grupo de recursos de Azure** y **área de trabajo de Operations Management Suite**.  
   ![Conexión a la página Conexión de Log Analytics del Asistente para Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. El asistente se conecta al área de trabajo de Log Analytics usando la información que introdujo. Seleccione las recopilaciones de dispositivos que desea sincronizar con el servicio y, después, haga clic en **Agregar**.  
   ![Selección de recopilaciones](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Compruebe la configuración de conexión de la pantalla **Resumen** y, después, seleccione **Siguiente**. La pantalla **Progreso** muestra el estado de conexión, que debería ser **Completado**.

> [!NOTE]
> Debe conectar el sitio de nivel superior de la jerarquía a Azure Monitor. Si conecta un sitio principal independiente a Azure Monitor y, después, agrega un sitio de administración central a su entorno, tendrá que eliminar y volver a crear la conexión dentro de la nueva jerarquía.
>
>

Después de haber vinculado Configuration Manager a Azure Monitor, puede agregar o quitar recopilaciones y ver las propiedades de la conexión.

## <a name="update-log-analytics-workspace-connection-properties"></a>Actualización de las propiedades de conexión del área de trabajo de Log Analytics
Si la clave secreta de cliente o la contraseña nunca expira o se pierde, tendrá que actualizar manualmente las propiedades de conexión de Log Analytics.

1. En Configuration Manager, acceda a **Cloud Services**, después, seleccione **OMS Connector** (Conector de OMS) para abrir la página **OMS Connection Properties** (Propiedades de conexión de OMS).
2. En esta página, haga clic en la pestaña **Azure Active Directory** para ver su **inquilino**, **identificador de cliente** y **expiración de clave secreta de cliente**. **Compruebe** si ha expirado la **clave secreta de cliente**.

## <a name="import-collections"></a>Importación de recopilaciones
Después de agregar una conexión de Log Analytics en Configuration Manager y de instalar el agente en el equipo que ejecuta el rol de sistema de sitio del punto de conexión de servicio de Configuration Manager, el paso siguiente es importar las recopilaciones de Configuration Manager en Azure Monitor como grupos de equipos.

Después de completar la configuración inicial para importar recopilaciones de dispositivos de la jerarquía, la información de pertenencia de la recopilación se recupera cada 3 horas para mantener actualizada la pertenencia. Puede deshabilitar esta opción en cualquier momento.

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Áreas de trabajo de Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione el área de trabajo en la que se ha registrado Configuration Manager.  
3. Seleccione **Configuración avanzada**.
4. Seleccione **Grupos de equipos** y, a continuación, **SCCM**.  
5. Seleccione **Importar pertenencias de la recopilación de Configuration Manager** y, después, haga clic en **Guardar**.  
   ![Grupos de equipos: pestaña SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualización de datos de Configuration Manager
Después de agregar una conexión de Log Analytics en Configuration Manager y de instalar el agente en el equipo que ejecuta el rol de sistema de sitio del punto de conexión de servicio de Configuration Manager, se envían datos desde el agente al área de trabajo de Log Analytics en Azure Monitor. En Azure Monitor, las recopilaciones de Configuration Manager aparecen como [grupos de equipos](../../azure-monitor/platform/computer-groups.md). Puede ver los grupos de la página **Configuration Manager** en **Configuración\Grupos de equipos**.

Después de importar las recopilaciones, puede ver cuántos equipos con pertenencias a las recopilaciones se han detectado. También puede ver el número de recopilaciones que se han importado.

![Grupos de equipos: pestaña SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Al hacer clic en cualquiera de ellos, se abre Búsqueda, que muestra todos los grupos importados o todos los equipos que pertenecen a cada grupo. Mediante [Búsqueda de registros](../../azure-monitor/log-query/log-query-overview.md), puede iniciar un análisis exhaustivo de datos de Configuration Manager.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsqueda de registros](../../azure-monitor/log-query/log-query-overview.md) para ver información detallada sobre los datos de Configuration Manager.
