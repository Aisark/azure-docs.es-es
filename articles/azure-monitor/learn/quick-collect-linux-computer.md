---
title: Configuración de el agente de Azure Log Analytics para el equipo Linux híbrido | Microsoft Docs
description: Aprenda a implementar el agente de Log Analytics para Linux en equipos fuera de Azure y habilitar la colección de datos con Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 4484269194aa3d637101a6a0b83eacb268d4c16e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "67147278"
---
# <a name="configure-log-analytics-agent-for-linux-computers-in-a-hybrid-environment"></a>Configuración del agente de Log Analytics para los equipos Linux en un entorno híbrido
[Azure Log Analytics](../platform/agent-windows.md) puede recopilar datos directamente de los equipos Linux físicos o virtuales en un centro de datos o en otro entorno en la nube dentro de un único repositorio para llevar a cabo una correlación y un análisis detallados. En esta guía de inicio rápido se muestra cómo configurar y recopilar datos de equipos Linux con unos pasos sencillos.  Para máquinas virtuales Linux de Azure, vea el tema [Recopilación de datos acerca de máquinas virtuales de Azure](quick-collect-azurevm.md).  

Antes de continuar, debería revisar el artículo de [información general](../platform/log-analytics-agent.md) sobre el agente de Log Analytics para saber cuáles son las configuraciones admitidas, los [sistemas operativos Linux compatibles](../platform/log-analytics-agent.md#supported-linux-operating-systems) y la [configuración del firewall de red](../platform/log-analytics-agent.md#network-firewall-requirements). 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Crear un área de trabajo
1. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.

    ![Portal de Azure](media/quick-collect-linux-computer/azure-portal-01.png) 

2. Haga clic en **Crear** y, a continuación, seleccione opciones para los elementos siguientes:

   * Proporcione el nombre de la nueva **área de trabajo de Log Analytics** como, por ejemplo, *DefaultLAWorkspace*. Las áreas de trabajo de OMS ahora se conocen como áreas de trabajo de Log Analytics.   
   * Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   * Para **Grupo de recursos**, seleccione un grupo de recursos existente que contenga una o más máquinas virtuales de Azure.  
   * Seleccione la **Ubicación** en que están implementadas las VM.  Para obtener más información, consulte en qué [regiones está disponible Log Analytics](https://azure.microsoft.com/regions/services/).  
   * Si va a crear un área de trabajo en una nueva suscripción creada después del 2 de abril de 2018, esta utilizará automáticamente el plan de precios *Por GB* y la opción para seleccionar un plan de tarifas no estará disponible.  Si va a crear un área de trabajo para una suscripción existente creada antes del 2 de abril o para una suscripción asociada a una inscripción de EA existente, seleccione el plan de tarifa que prefiera.  Para obtener más información sobre planes concretos, consulte los [detalles de precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Creación de una hoja de recursos de Log Analytics](media/quick-collect-linux-computer/create-loganalytics-workspace-02.png)<br>  

3. Después de proporcionar la información necesaria en el panel **Área de trabajo de Log Analytics**, haga clic en **Aceptar**.  

Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

## <a name="obtain-workspace-id-and-key"></a>Obtención de la clave y el identificador de área de trabajo
Antes de instalar el agente Log Analytics para Linux, necesita la clave y el identificador de área de trabajo para el área de trabajo de Log Analytics.  El script contenedor del agente necesita esta información para configurar el agente de la forma adecuada y asegurarse de que puede comunicarse correctamente con Log Analytics.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione *DefaultLAWorkspace* (creada antes).
3. Seleccione **Configuración avanzada**.

    ![Configuración avanzada de Log Analytics](media/quick-collect-linux-computer/log-analytics-advanced-settings-01.png) 
 
4. Seleccione **Orígenes conectados** y **Servidores Linux**.   
5. Encontrará los valores a la derecha de **Id. del área de trabajo** y **Clave principal**. Copie y pegue ambos valores en el editor que prefiera.   

## <a name="install-the-agent-for-linux"></a>Instalación del agente para Linux
Los pasos siguientes configuran la instalación del agente de Log Analytics en Azure y en la nube de Azure Government.  

>[!NOTE]
>No se puede configurar el agente de Log Analytics para Linux para informar a varias áreas de trabajo de Log Analytics.  

Si el equipo Linux necesita comunicarse mediante un servidor proxy con Log Analytics, la configuración del proxy puede especificarse en la línea de comandos mediante la inclusión de `-p [protocol://][user:password@]proxyhost[:port]`.  La propiedad *proxyhost* acepta un nombre de dominio completo o la dirección IP del servidor proxy. 

Por ejemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar el equipo Linux de modo que se conecte con Log Analytics, ejecute el comando siguiente con el identificador del área de trabajo y la clave principal que ha copiado anteriormente. El siguiente comando descarga el agente, valida su suma de comprobación y lo instala. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    El siguiente comando incluye el parámetro de proxy `-p` y la sintaxis de ejemplo.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar el equipo Linux de modo que se conecte con Log Analytics en la nube de Azure Government, ejecute el comando siguiente con el identificador del área de trabajo y la clave principal que ha copiado anteriormente. El siguiente comando descarga el agente, valida su suma de comprobación y lo instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    El siguiente comando incluye el parámetro de proxy `-p` y la sintaxis de ejemplo.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Ejecute el comando siguiente para reiniciar el agente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recopilación de datos de eventos y rendimiento
Log Analytics puede recopilar eventos de Syslog de Linux, así como de los contadores de rendimiento que especifique para los informes y análisis a largo plazo, y actuar cuando se detecte una condición determinada.  Siga estos pasos para configurar la recopilación de eventos desde Syslog de Linux, así como desde varios contadores de rendimiento comunes, para empezar.  

1. Seleccione **Syslog**.  
2. Para agregar un registro de eventos, escriba el nombre del registro. Escriba **Syslog** y, a continuación, haga clic en el signo más **+** .  
3. En la tabla, desactive los niveles de gravedad **Información**, **Aviso** y **Depurar**. 
4. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.
5. Seleccione **Linux Performance Data** (Datos de rendimiento de Linux) para habilitar la recopilación de contadores de rendimiento en un equipo Linux. 
6. La primera vez que se configuran los contadores de rendimiento Linux para un área de trabajo de Log Analytics nueva, se ofrece la opción de crear rápidamente varios contadores comunes. Se muestran todos con una casilla junto a cada uno. 

    ![Contadores de rendimiento predeterminados de Windows seleccionados](media/quick-collect-linux-computer/linux-perfcounters-default.png)
    
    Haga clic en **Agregar los contadores de rendimiento seleccionados**. Se agregan con el valor preestablecido de un intervalo de ejemplo de recopilación de diez segundos.

7. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.

## <a name="view-data-collected"></a>Ver datos recopilados
Ahora que ya ha habilitado la recopilación de datos, vamos a ver un sencillo ejemplo de búsqueda de registros para consultar algunos datos del equipo de destino.  

1. En Azure Portal, vaya a Log Analytics y seleccione el área de trabajo que creó antes.
2. Haga clic en el icono **Búsqueda de registros**. A continuación, en el campo de consulta del panel Búsqueda de registros, escriba `Perf` y presione Entrar o haga clic en el botón de búsqueda situado a la derecha del campo de consulta.

    ![Ejemplo de consulta de búsqueda de registros de Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Por ejemplo, la consulta de la imagen siguiente ha devuelto 735 registros de rendimiento.

    ![Resultado de búsqueda de registros de Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede quitar el agente del equipo Linux y eliminar el área de trabajo de Log Analytics.  

Para quitar el agente, ejecute el siguiente comando en el equipo Linux. El argumento *--purge* quita completamente el agente y su configuración.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para eliminar el área de trabajo, seleccione el área de trabajo de Log Analytics que ha creado anteriormente y, en la página de recursos, haga clic en **Eliminar**.

![Eliminación de recursos de Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya recopila datos sobre el funcionamiento y el rendimiento del equipo Linux local, le resultará muy fácil empezar a explorar y analizar los datos que se recopilan, además de tomar las medidas correspondientes a partir de estos. Todo ello, *de forma gratuita*.  

Para obtener información sobre cómo ver y analizar los datos, continúe con el tutorial.   

> [!div class="nextstepaction"]
> [Ver o analizar datos en Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
