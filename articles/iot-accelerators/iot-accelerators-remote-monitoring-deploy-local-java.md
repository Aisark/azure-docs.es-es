---
title: 'Implementación local de la solución de supervisión remota (mediante el IDE de IntelliJ): Azure | Microsoft Docs'
description: En esta guía paso a paso se muestra cómo implementar el acelerador de soluciones de supervisión remota en la máquina local mediante IntelliJ para pruebas y desarrollo.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: ccdb3e16ea60cf85ae28e533e3b2d9f473cc90c8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316395"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implementación local del acelerador de soluciones de supervisión remota: IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

En este artículo, se explica cómo implementar el acelerador de soluciones de supervisión remota en la máquina local para pruebas y desarrollo. Aprenderá a ejecutar los microservicios en IntelliJ. Una implementación local de microservicios utiliza los siguientes servicios en la nube: servicios de IoT Hub, Cosmos DB, Azure Streaming Analytics y Azure Time Series Insights en la nube.

Si quiere ejecutar el acelerador de soluciones de supervisión remota en Docker en el equipo local, consulte [Deploy the Remote Monitoring solution accelerator locally - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md) (Implementación local del acelerador de soluciones de supervisión remota: Docker).

## <a name="prerequisites"></a>Requisitos previos

Para implementar los servicios de Azure que usa el acelerador de soluciones de supervisión remota, necesita una suscripción de Azure activa.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuración de la máquina

Para completar la implementación local, necesita que las herramientas siguientes estén instaladas en la máquina de desarrollo local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Plugin Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ Plugin SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/): este software es un requisito previo para la CLI de PCS que usan los scripts para crear recursos de Azure. No use Node.js v10.

> [!NOTE]
> El IDE de IntelliJ está disponible para Windows y Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Ejecución de los microservicios

En esta sección, ejecutará los microservicios de Supervisión remota. Ejecute la interfaz de usuario web de forma nativa, el servicio de simulación de dispositivo, Auth y ASA Manager en Docker y los microservicios en IntelliJ.

### <a name="run-the-device-simulation-service"></a>Ejecución del servicio de simulación de dispositivos

Abra una nueva ventana del símbolo del sistema para asegurarse de tener acceso a las variables de entorno establecidas por el script **start.cmd** en la sección anterior.

Ejecute el siguiente comando para iniciar el contenedor de Docker para el servicio de simulación de dispositivos. El servicio simula dispositivos para la solución de supervisión remota.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Ejecución del servicio Auth

Abra una nueva ventana del símbolo del sistema y ejecute el siguiente comando para iniciar el contenedor de Docker para el servicio Auth. El servicio permite administrar los usuarios autorizados para acceder a las soluciones de Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Ejecución del servicio ASA Manager

Abra una nueva ventana del símbolo del sistema y ejecute el siguiente comando para iniciar el contenedor de Docker para el servicio ASA Manager. El servicio permite la administración de trabajos de Azure Stream Analytics (ASA), lo que incluye el establecimiento de la configuración y el inicio, la detención y la supervisión de sus estados.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Implementación de los demás microservicios en la máquina local

En los pasos siguientes se muestra cómo ejecutar los microservicios de Supervisión remota en IntelliJ:

#### <a name="import-project"></a>Importar proyecto

1. Inicie el IDE de IntelliJ
1. Seleccione **Import Project** (Importar proyecto) y elija **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Creación de configuraciones de ejecución

1. Seleccione **Run > Edit Configurations** (Ejecutar > Editar configuraciones)
1. Seleccione **Add New Configuration > sbt task** (Agregar configuración nueva > tarea sbt) 
1. Escriba el **nombre** y escriba las **tareas** que se ejecutan 
1. Seleccione el **directorio de trabajo** en función del servicio que desee ejecutar
1. Haga clic en **Apply > OK** (Aplicar > Aceptar) para guardar las opciones.
1. Cree configuraciones de ejecución de los siguientes servicios:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Como ejemplo la siguiente imagen muestra cómo agregar la configuración de un servicio:

[![Agregar configuración](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Creación de una configuración compuesta

1. Para ejecutar todos los servicios conjuntamente, seleccione **Add new Configuration > Compound** (Agregar nueva configuración > Compuesta)
1. Escriba el **nombre** y **agregue tareas sbt**
1. Haga clic en **Apply > OK** (Aplicar > Aceptar) para guardar las opciones.

Como ejemplo la siguiente imagen muestra cómo se agregan todas las tareas sbt a una sola configuración:


[![Agregar todos los servicios](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Haga clic en **Run** (Ejecutar) para compilar y ejecutar los servicios web en el equipo local.

Cada servicio web abre una ventana del explorador web y del símbolo del sistema. En el símbolo del sistema, verá el resultado del servicio en ejecución y la ventana del explorador le permitirá supervisar el estado. No cierre el símbolo del sistema ni las páginas web, ya que esta acción detiene el servicio web.


Para acceder al estado de los servicios, puede navegar a las siguientes direcciones URL:
* iothubmanager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* deviceTelemetry  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storageAdapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Inicio del trabajo de Stream Analytics

Siga estos pasos para iniciar el trabajo de Stream Analytics:

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Vaya al **grupo de recursos** creado para la solución. El nombre del grupo de recursos es el nombre que eligió para la solución al ejecutar el script** **start.cmd**.
1. Haga clic en el **trabajo de Stream Analytics** de la lista de recursos.
1. En la página de **información general** del trabajo de Stream Analytics, haga clic en el botón **Inicio**. A continuación, haga clic en **Inicio** para iniciar ya el trabajo.

### <a name="run-the-web-ui"></a>Ejecución de la interfaz de usuario web

En este paso, inicie la interfaz de usuario web. Abra una nueva ventana del símbolo del sistema para asegurarse de tener acceso a las variables de entorno establecidas por el script **start.cmd**. Navegue hasta la carpeta **webui** de la copia local del repositorio y ejecute los siguientes comandos:

```cmd
npm install
npm start
```

Una vez completado el inicio, el explorador muestra la página **http:\//localhost:3000 / panel**. Los errores de esta página son de esperar. Para ver la aplicación sin errores, complete el paso siguiente.

### <a name="configure-and-run-nginx"></a>Configuración y ejecución de NGINX

Configure un servidor proxy inverso para vincular la aplicación web y los microservicios que se ejecutan en el equipo local:

* Copie el archivo **nginx.conf** de la carpeta **webui\scripts\localhost** de la copia local del repositorio en el directorio **nginx\conf** de instalación.
* Ejecute **nginx**.

Para obtener más información acerca de cómo ejecutar **nginx**, consulte [nginx for Windows](https://nginx.org/en/docs/windows.html) (nginx para Windows).

### <a name="connect-to-the-dashboard"></a>Conexión al panel

Para acceder al panel de solución de supervisión remota, vaya a http:\//localhost:9000 en el explorador.

## <a name="clean-up"></a>Limpieza

Para evitar cargos innecesarios, cuando haya terminado las pruebas, elimine los servicios en la nube de su suscripción a Azure. Para quitar los servicios, vaya a [Azure Portal](https://ms.portal.azure.com) y elimine el grupo de recursos que creó el script **start.cmd**.

También puede eliminar la copia local del repositorio de Supervisión remota que se ha creado al clonar el código fuente de GitHub.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](quickstart-remote-monitoring-deploy.md).
