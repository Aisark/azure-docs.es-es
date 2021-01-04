---
title: Conector de Administración de servicios de TI en Log Analytics
description: En este artículo se proporciona información general sobre el Conector de Administración de servicios de TI (ITSMC) e información sobre cómo usarlo para supervisar y administrar los elementos de trabajo de ITSM en Log Analytics y resolver rápidamente cualquier problema.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 3d4e5ad0b24b7163072d7e3110a523dad9608923
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507378"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Conexión de Azure a las herramientas de ITSM mediante el Conector de Administración de servicios de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

El Conector de Administración de servicios de TI (ITSMC) le permite conectar Azure a un producto o servicio de Administración de servicios de TI (ITSM) compatibles.

Servicios de Azure como Azure Log Analytics y Azure Monitor proporcionan herramientas para detectar, analizar y solucionar problemas relacionados con los recursos de Azure y de otros proveedores. Sin embargo, los elementos de trabajo relacionados con un problema suelen residir en un producto o servicio de ITSM. ITSMC proporciona una conexión bidireccional entre las herramientas de Azure e ITSM para ayudarle a resolver problemas con mayor rapidez.

ITSMC admite conexiones con las siguientes herramientas ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Proponemos a nuestros clientes de Cherwell y Provance que utilicen la [acción de Webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) en el punto de conexión a Cherwell y Provance como otra solución para la integración.

Con ITSMC, puede:

-  Crear elementos de trabajo en la herramienta de ITSM, en función de las alertas de Azure (alertas de métricas, de registro de actividad y de Log Analytics).
-  Sincronizar de forma opcional los datos de incidentes y solicitudes de cambio de la herramienta ITSM con un área de trabajo de Azure Log Analytics.

Para obtener información sobre los términos legales y la directiva de privacidad, vea la [declaración de privacidad de Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Para empezar a usar ITSMC, siga estos pasos:

1.  [Agregue ITSMC](#add-it-service-management-connector).
2. [Conectar productos o servicios de ITSM con el Conector de Administración de servicios de TI](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
3.  [Cree una conexión de ITSM](#create-an-itsm-connection).
4.  [Use la conexión](#use-itsmc).
   
   >[!NOTE]
> Debe seguir los pasos por este orden; de lo contrario, obtendrá un error.

##  <a name="add-it-service-management-connector"></a>Adición del Conector de Administración de servicios de TI

Para poder crear una conexión, debe agregar ITSMC.

1. En Azure Portal, seleccione **Crear un recurso**:

   ![Captura de pantalla en la que se muestra el elemento de menú Crear un recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Busque el **Conector de Administración de servicios de TI** en Azure Marketplace. Seleccione **Crear**:

   ![Captura de pantalla en la que muestra el botón Crear en Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. En la sección **Área de trabajo de OMS**, seleccione el área de trabajo de Azure Log Analytics donde quiera instalar ITSMC.
   >[!NOTE]
   > * Como parte de la transición continuada de Microsoft Operations Management Suite (OMS) a Azure Monitor, las áreas de trabajo de OMS ahora se conocen como *áreas de trabajo de Log Analytics*.
   > * ITSMC solo se puede instalar en áreas de trabajo de Log Analytics en las siguientes regiones: Este de EE. UU., Oeste de EE. UU. 2, Centro-sur de EE. UU., Centro-oeste de EE. UU., US Gov Arizona, US Gov Virginia, Centro de Canadá, Oeste de Europa, Sur de Reino Unido, Sudeste Asiático, Este de Japón, Centro de la India y Sudeste de Australia.


4. En la sección **Área de trabajo de Log Analytics**, seleccione el grupo de recursos donde quiera crear el recurso de ITSMC:

   ![Captura de pantalla en la que se muestra la sección Área de trabajo de Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte de la transición continuada de Microsoft Operations Management Suite (OMS) a Azure Monitor, las áreas de trabajo de OMS ahora se conocen como *áreas de trabajo de Log Analytics*.

5. Seleccione **Aceptar**.

Cuando se implemente el recurso de ITSMC, aparecerá una notificación en la esquina superior derecha de la ventana.


## <a name="create-an-itsm-connection"></a>Cree una conexión de ITSM

Después de instalar ITSMC, puede crear una conexión.

Para crear una conexión, debe preparar la herramienta de ITSM para permitir la conexión desde ITSMC.  

En función del producto de ITSM al que se va a conectar, seleccione alguno de los siguientes vínculos para obtener instrucciones:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Una vez que haya preparado las herramientas de ITSM, siga estos pasos para crear una conexión:

1. En **Todos los recursos**, busque **ServiceDesk(*nombre de su área de trabajo*)** :

   ![Captura de pantalla en la que se muestran los recursos recientes en Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. En **Orígenes de datos del área de trabajo** en el panel de la izquierda, seleccione **Conexiones de ITSM**:

   ![Captura de pantalla en la que se muestra el elemento de menú Conexiones de ITSM](media/itsmc-overview/add-new-itsm-connection.png).
   En esta página se muestra la lista de conexiones.
1. Seleccione **Agregar conexión**.

4. Especifique la configuración de conexión tal como se describe en el artículo [Configuración de la conexión de ITSMC con productos o servicios de ITSM](./itsmc-connections.md).

   > [!NOTE]
   >
   > De forma predeterminada, ITSMC actualiza los datos de configuración de la conexión una vez cada 24 horas. Para actualizar los datos de la conexión al instante para reflejar cualquier modificación o actualizaciones de plantilla que se realicen, seleccione el botón **Sincronizar** que se muestra en la hoja de la conexión:
   >
   > ![Captura de pantalla en la que se muestra el botón Sincronizar en la hoja conexión.](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>Uso de ITSMC
   Puede usar ITSMC para crear elementos de trabajo de alertas de Azure, alertas de Log Analytics y entradas de registros de Log Analytics.

## <a name="template-definitions"></a>Definiciones de plantillas
   Existen tipos de elementos de trabajo que pueden utilizar las plantillas que haya definido la herramienta ITSM.
Con el uso de plantillas, puede definir los campos que se rellenarán automáticamente en función de los valores fijos que se definen como parte del grupo de acciones. Las plantillas se definen en la herramienta ITSM. Puede definir qué plantilla le gustaría usar como parte de la definición del grupo de acciones.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Creación de elementos de trabajo de ITSM a partir de alertas de Azure

Después de crear la conexión de ITSM, puede crear elementos de trabajo en la herramienta ITSM en función de las alertas de Azure. Para crear los elementos de trabajo, usará la acción de ITSM en grupos de acciones.

Los grupos de acciones proporcionan una manera modular y reutilizable de desencadenar acciones para las alertas de Azure. Puede usar los grupos de acciones con alertas de métricas, de registros de actividad y de Azure Log Analytics en Azure Portal.

> [!NOTE]
> Después de crear la conexión de ITSM, debe esperar 30 minutos para que finalice el proceso de sincronización.
> 

Utilice el procedimiento siguiente para crear elementos de trabajo:

1. En Azure Portal, seleccione **Alertas**.
2. En el menú de la parte superior de la pantalla, seleccione **Administrar acciones**:

    ![Captura de pantalla en la que se muestra el elemento de menú Administrar acciones.](media/itsmc-overview/action-groups-selection-big.png)

   Aparece la ventana **Crear grupo de acciones**.

3. Seleccione las opciones de **Suscripción** y **Grupo de recursos** donde quiere crear el grupo de acciones. Escriba lo que corresponda en **Nombre del grupo de acciones** y **Nombre para mostrar** para el grupo de acciones. Seleccione **Siguiente: notificaciones**.

    ![Captura de pantalla en la que se muestra la ventana Crear grupo de acciones.](media/itsmc-overview/action-groups-details.png)

4. En la lista de notificaciones, seleccione **Siguiente: acciones**.
5. En la lista de acciones, seleccione **ITSM** en la lista **Tipo de acción**. Proporcione un **Nombre** para la acción. Seleccione el botón del lápiz que representa **Editar detalles**.
6. En la lista **Suscripción**, seleccione la suscripción en la que se encuentra el área de trabajo de Log Analytics. En la lista **Conexión**, seleccione el nombre del conector de ITSM. Irá seguido del nombre del área de trabajo. Por ejemplo, MiConectorITSM(MiÁreaDeTrabajo).

7. Seleccione un tipo de **Elemento de trabajo**.

8. Si desea rellenar los campos predeterminados con valores fijos, seleccione **Usar la plantilla personalizada**. De lo contrario, elija una [plantilla](#template-definitions) existente en la lista **Plantilla** y escriba los valores fijos en los campos de la plantilla.

9. Al seleccionar **Crear elementos de trabajo individuales para cada elemento de configuración**, cada elemento de configuración tendrá su propio elemento de trabajo. Habrá un elemento de trabajo por cada elemento de configuración. Se actualizará según las alertas que se crearán.

    * En caso de que seleccione en la lista desplegable de elemento de trabajo "Incidente" o "Alerta": Si desactiva la casilla **Crear elementos de trabajo individuales para cada elemento de configuración**, cada alerta creará un elemento de trabajo. Puede haber más de una alerta por elemento de configuración.

       ![Captura de pantalla que muestra la ventana Incidente de ITSM.](media/itsmc-overview/itsm-action-configuration.png)

    * En caso de que seleccione en la lista desplegable de elemento de trabajo "Evento": Al seleccionar **Crear elementos de trabajo individuales para cada entrada de registro** en la selección de botones de radio, cada alerta creará un nuevo elemento de trabajo. Al seleccionar **Crear elementos de trabajo individuales para cada elemento de configuración** en la selección de botones de radio, cada elemento de configuración tendrá su propio elemento de trabajo.
   ![Captura de pantalla en la que se muestra la ventana Evento de ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Seleccione **Aceptar**.

Al crear o editar una regla de alerta de Azure, use un grupo de acciones, que tiene una acción de ITSM. Cuando se desencadena la alerta, se crea o actualiza el elemento de trabajo en la herramienta ITSM.

> [!NOTE]
>
>- Para obtener información sobre los precios de la acción de ITSM, vea la [página de precios](https://azure.microsoft.com/pricing/details/monitor/) de los grupos de acciones.
>
>
>- El campo de descripción breve de la definición de la regla de alerta está limitado a 40 caracteres cuando se envía mediante la acción de ITSM.

## <a name="additional-information"></a>Información adicional

### <a name="data-synced-from-your-itsm-product"></a>Datos sincronizados desde el producto de ITSM
Los incidentes y las solicitudes de cambio se sincronizan desde el producto de ITSM con el área de trabajo de Log Analytics según la configuración de la conexión.

En esta sección se muestran algunos ejemplos de datos recopilados por ITSMC.

Los campos de **ServiceDesk_CL** varían según el tipo de elemento de trabajo que importe en Log Analytics. Esta es una lista de campos para dos tipos de elementos de trabajo:

**Elemento de trabajo:** **Incidentes**  
ServiceDeskWorkItemType_s="Incidente"

**Fields**

- ServiceDeskConnectionName
- Service Desk ID
- State
- Urgencia
- Impacto
- Priority
- Escalado
- Creado por
- Resuelto por
- Cerrado por
- Source
- Asignado a
- Category
- Título
- Descripción
- Fecha de creación
- Fecha de cierre
- Fecha de resolución
- Fecha de última modificación
- Computer


**Elemento de trabajo:** **Solicitudes de cambio**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk ID
- Creado por
- Cerrado por
- Source
- Asignado a
- Título
- Tipo
- Category
- State
- Escalado
- Estado del conflicto
- Urgencia
- Priority
- Riesgo
- Impacto
- Asignado a
- Fecha de creación
- Fecha de cierre
- Fecha de última modificación
- Fecha de solicitud
- Fecha de inicio planeada
- Fecha de finalización planeada
- Fecha de inicio del trabajo
- Fecha de finalización del trabajo
- Descripción
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Datos de salida para un incidente de ServiceNow

| Campo de Log Analytics | Campo de ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgencia |
| Impact_s |Impacto|
| Priority_s | Priority |
| CreatedBy_s | Abierto por |
| ResolvedBy_s | Resuelto por|
| ClosedBy_s  | Cerrado por |
| Source_s| Tipo de contacto |
| AssignedTo_s | Asignado a  |
| Category_s | Category |
| Title_s|  Descripción breve |
| Description_s|  Notas |
| CreatedDate_t|  Abierto |
| ClosedDate_t| closed|
| ResolvedDate_t|Resuelto|
| Computer  | Elemento de configuración |

## <a name="output-data-for-a-servicenow-change-request"></a>Datos de salida para una solicitud de cambio de ServiceNow

| Log Analytics | Campo de ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Cerrado por |
| AssignedTo_s | Asignado a  |
| Title_s|  Descripción breve |
| Type_s|  Tipo |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Urgencia |
| Priority_s| Priority|
| Risk_s| Riesgo|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por fecha |
| ClosedDate_t | Fecha de cierre |
| PlannedStartDate_t  | Fecha de inicio planeada |
| PlannedEndDate_t  | Fecha de finalización planeada |
| WorkStartDate_t  | Fecha de inicio real |
| WorkEndDate_t | Fecha de finalización real|
| Description_s | Descripción |
| Computer  | Elemento de configuración |

## <a name="contact-us"></a>Ponerse en contacto con nosotros

Si tiene consultas o comentarios sobre el Conector de Administración de servicios de TI, póngase en contacto con nosotros en [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes
[Incorporación de productos o servicios de ITSM al Conector de Administración de servicios de TI](./itsmc-connections.md)
