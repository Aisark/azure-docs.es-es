---
title: Configuración de la visualización de dependencias en Azure Migrate
description: Describe la configuración de la visualización de dependencias en Azure Migrate Server Assessment.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: f5b0682adf345681c03bb6dbf14e9a0d9ba1fd58
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589080"
---
# <a name="set-up-dependency-visualization"></a>Configuración de la visualización de dependencias

En este artículo se describe cómo configurar la visualización de dependencias Azure Migrate: Server Assessment. La [visualización de dependencias](concepts-dependency-visualization.md#what-is-dependency-visualization) le ayuda a identificar y comprender las dependencias en las máquinas que quiere evaluar y migrar a Azure.

## <a name="before-you-start"></a>Antes de comenzar

- [Revise](concepts-dependency-visualization.md) los requisitos y los costos asociados con la visualización de dependencias.
- Asegúrese de que ha [creado](how-to-add-tool-first-time.md) un proyecto de Azure Migrate.
- Si ya ha creado un proyecto, asegúrese de que ha [agregado](how-to-assess.md) la herramienta Azure Migrate: Server Assessment.
- Asegúrese de que ha configurado un [dispositivo Azure Migrate](migrate-appliance.md) para detectar las máquinas locales. Obtenga información sobre cómo configurar un dispositivo para [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). El dispositivo detecta máquinas locales, y envía metadatos y datos de rendimiento a Azure Migrate: Server Assessment.
- Si quiere usar la visualización de dependencias, debe asociar un [área de trabajo de Log Analytics](../azure-monitor/platform/manage-access.md) con un proyecto de Azure Migrate:
    - Asegúrese de que tiene un área de trabajo en la suscripción que contiene el proyecto de Azure Migrate.
    - El área de trabajo debe residir en las regiones Este de EE. UU., Sudeste Asiático u Oeste de Europa. Las áreas de trabajo de otras regiones no se pueden asociar a un proyecto.
    - El área de trabajo debe estar en una región en la que [se admita Service Map](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - Puede asociar un área de trabajo de Log Analytics nueva o existente con un proyecto de Azure Migrate.
    - La primera vez que configure la visualización de dependencias para una máquina, deberá asociar el área de trabajo. El área de trabajo de un proyecto de Azure Migrate no se puede modificar una vez que se ha agregado.
    - En Log Analytics, el área de trabajo asociada con Azure Migrate se etiqueta con la clave del proyecto de migración y el nombre del proyecto.

- Solo puede asociar un área de trabajo después de detectar máquinas en el proyecto de Azure Migrate. Para ello, puede configurar un dispositivo Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). El dispositivo detecta máquinas locales y envía metadatos y datos de rendimiento a Azure Migrate: Server Assessment. [Más información](migrate-appliance.md).

## <a name="associate-a-workspace"></a>Asociación de un área de trabajo

1. Una vez que haya detectado las máquinas que se van a evaluar, en **Servidores** > **Azure Migrate:Server Assessment**, haga clic en **Información general**.  
2. En **Azure Migrate:Server Assessment**, haga clic en **Información esencial**.
3. En **Área de trabajo de OMS**, haga clic en **Requiere configuración**.

     ![Configuración de un área de trabajo de Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. En **Configurar área de trabajo de OMS**, especifique si quiere crear un área de trabajo nueva o utilizar una existente.
    - Puede seleccionar un área de trabajo existente de todas las áreas de trabajo de la suscripción del proyecto de migración.
    - Necesita acceso de lectura al área de trabajo para asociarla.
5. Si crea una nueva área de trabajo, seleccione una ubicación para ella.

    ![Agregar una nueva área de trabajo](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Descarga e instalación de los agentes en máquinas virtuales

En cada máquina que desee analizar, instale los agentes.

> [!NOTE]
    > En el caso de las máquinas que supervisa System Center Operations Manager 2012 R2 o las versiones posteriores, no hay necesidad de instalar el agente MMA. Service Map se integra con Operations Manager. [Siga esta](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) guía de integración.

1. En **Azure Migrate: Server Assessment**, haga clic en **Servidores detectados**.
2. Para cada máquina que desee analizar con visualización de dependencias, en la columna **Dependencias**, haga clic en **Requiere la instalación del agente**.
3. En la página **Dependencias**, descargue Microsoft Monitoring Agent y Dependency Agent para Windows o Linux.
4. En **Configure MMA agent** (Configurar agente MMA), copie el id. del área de trabajo y la clave. Los necesitará para instalar el agente MMA.

    ![Instalación de los agentes](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalación de MMA

Instale Microsoft Monitoring Agent en cada máquina Windows o Linux que desee analizar.

### <a name="install-mma-on-a-windows-machine"></a>Instalación de Microsoft Monitoring Agent en una máquina Windows

Para instalar al agente en una máquina Windows, siga estos pasos:

1. Haga doble clic en el agente descargado.
2. En la página **principal**, haga clic en **Siguiente**. En la página **Términos de licencia**, haga clic en **Acepto** para aceptar la licencia.
3. En **Carpeta de destino**, mantenga o modifique la carpeta de instalación predeterminada y después haga clic en **Siguiente**.
4. En **Opciones de instalación del agente**, seleccione **Azure Log Analytics** > **Siguiente**.
5. Haga clic en **Agregar** para agregar un área de trabajo de Log Analytics nueva. Pegue la clave y el identificador de área de trabajo que ha copiado desde el portal. Haga clic en **Next**.

El agente se puede instalar desde la línea de comandos o mediante un método automatizado, como Configuration Manager o [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Obtenga más información](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre el uso de estos métodos para instalar el agente MMA.
- También se puede instalar el agente de MMA mediante este [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Más información](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sobre la compatibilidad de MMA con sistemas operativos Windows.

### <a name="install-mma-on-a-linux-machine"></a>Instalación de Microsoft Monitoring Agent en una máquina Linux

Para instalar Microsoft Monitoring Agent en una máquina Linux:

1. Transfiera el paquete adecuado (x86 o x x64) al equipo Linux mediante scp o sftp.
2. Instale el paquete mediante el argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Más información](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sobre la lista de compatibilidad de MMA con sistemas operativos Linux. 

## <a name="install-the-dependency-agent"></a>Instalación del agente de dependencia

1. Para instalar al agente de dependencia en una máquina Windows, haga doble clic en el archivo de instalación y siga los pasos del asistente.
2. Para instalar el agente de dependencia en una máquina Linux, instale como raíz mediante el siguiente comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Más información](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) acerca de cómo puede utilizar scripts para instalar el agente de dependencia.
- [Obtenga más información](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sobre los sistemas operativos que admite el agente de dependencia.


## <a name="create-a-group-using-dependency-visualization"></a>Creación de un grupo mediante la visualización de dependencias

Cree ahora un grupo para su evaluación. 


> [!NOTE]
> Los grupos de los que desea visualizar las dependencias no deben contener más de diez máquinas. Si tiene más de 10 máquinas, divídalas en grupos más pequeños.

1. En **Azure Migrate: Server Assessment**, haga clic en **Servidores detectados**.
2. En la columna **Dependencias**, haga clic en **Ver dependencias** para cada máquina que quiera revisar.
3. En el mapa de dependencias, puede ver lo siguiente:
    - Conexiones TCP de entrada (clientes) y de salida (servidores) hacia y desde la máquina.
    - Las máquinas dependientes que no tienen instalados agentes de dependencias se agrupan en función de los números de puerto.
    - Las máquinas dependientes con agentes de dependencias instalados se muestran como cuadros independientes.
    - Los procesos que se ejecutan en la máquina. Expanda el cuadro de cada máquina para ver los procesos.
    - Las propiedades de la máquina (incluido el FQDN, el sistema operativo y la dirección MAC). Haga clic en el cuadro de cada máquina para ver los detalles.

4. Puede consultar las dependencias de distintas duraciones; para ello, haga clic en la duración correspondiente en la etiqueta de intervalo de tiempo.
    - De forma predeterminada, el intervalo es una hora. 
    - Puede modificar el intervalo de tiempo o especificar las fechas de inicio y finalización, y la duración.
    - El intervalo de tiempo puede ser de hasta una hora. Si necesita un intervalo más amplio, use Azure Monitor para consultar los datos dependientes durante un período más prolongado.

5. Después de identificar máquinas dependientes que quiere agrupar, use Control + clic para seleccionar varias máquinas en el mapa y haga clic en **Agrupar máquinas**.
6. Especifique un nombre para el grupo.
7. Verifique que Azure Migrate puede detectar las máquinas dependientes.

    - Si Azure Migrate: Server Assessment no detecta una máquina dependiente, no puede agregarla al grupo.
    - Para agregar una máquina, vuelva a ejecutar la detección y verifique que se detectó.

8. Si desea crear una evaluación para este grupo, seleccione la casilla para crear una evaluación para el grupo.
8. Haga clic en **Aceptar** para guardar el grupo.

Después de crear el grupo, le recomendamos que instale los agentes en todas las máquinas del grupo y, a continuación, visualice las dependencias de todo el grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Consulta de datos de dependencia en Azure Monitor

Los datos de dependencia que captura Service Map se pueden consultar en el área de trabajo de Log Analytics asociada con el proyecto de Azure Migrate. Log Analytics se usa para escribir y ejecutar consultas de registros en Azure Monitor.

- [Obtenga información sobre cómo](../azure-monitor/insights/service-map.md#log-analytics-records) buscar datos de Service Map en Log Analytics.
- [Obtenga información general](../azure-monitor/log-query/get-started-queries.md) sobre la escritura de consultas de registro en [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Ejecute una consulta para los datos de dependencia como se indica a continuación:

1. Después de instalar los agentes, vaya al portal y haga clic en **Introducción**.
2. En **Azure Migrate: Server Assessment**, haga clic en **Información general**. Haga clic en la flecha abajo para expandir **Información esencial**.
3. En el **Área de trabajo de OMS**, haga clic en el nombre del área de trabajo.
3. En la página del área de trabajo de Log Analytics > **General**, haga clic en **Registros**.
4. Escriba la consulta y haga clic en **Ejecutar**.

### <a name="sample-queries"></a>Consultas de ejemplo

A continuación se muestran algunas consultas de ejemplo que puede utilizar para extraer datos de dependencia.

- Puede modificar las consultas para extraer los puntos de datos preferidos.
- [Revise](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) una lista completa de los registros de datos de dependencia.
- [Revise](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) ejemplos consultas adicionales.

#### <a name="sample-review-inbound-connections"></a>Sample: revisión de conexiones entrantes

Revise las conexiones entrantes para un conjunto de máquinas virtuales.

- Los registros de la tabla de métricas de conexión (VMConnection) no representan conexiones de red físicas individuales.
- Varias conexiones de red físicas se agrupan en una conexión lógica.
- [Obtenga más información](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre cómo se agregan los datos de conexión de red física en VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Sample: resumen de los datos enviados y recibidos

En este ejemplo se resume el volumen de datos enviados y recibidos en conexiones salientes entre un conjunto de máquinas.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Pasos siguientes

[Cree una evaluación](how-to-create-assessment.md) para un grupo.


