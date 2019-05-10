---
title: 'Inicio rápido: Creación de una instancia de Standard Load Balancer en Azure Portal'
titlesuffix: Azure Load Balancer
description: Este inicio rápido muestra cómo crear una instancia de Standard Load Balancer mediante Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 79ba86fd32248da240706fda2d8b5fcf8323263d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143189"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Inicio rápido: Cree mediante Azure Portal una instancia de Load Balancer Estándar para equilibrar la carga de máquinas virtuales

El equilibrio de carga proporciona un mayor nivel de disponibilidad y escala, ya que distribuye las solicitudes entrantes entre varias máquinas virtuales. Puede usar Azure Portal para crear un equilibrador de carga para las máquinas virtuales (VM). En este tutorial rápido se muestra cómo equilibrar la carga de máquinas virtuales mediante Load Balancer Estándar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Crear un equilibrador de carga estándar

En esta sección, se crea una instancia de Standard Load Balancer que ayuda a equilibrar la carga de las máquinas virtuales. La versión Estándar de Load Balancer solo admite direcciones IP públicas estándar. Cuando se crea una instancia de Load Balancer Estándar, también se debe crear una nueva dirección IP pública estándar que se configura como front-end (denominado *LoadBalancerFrontend* de forma predeterminada) para dicha instancia. 

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso** > **Redes** > **Load Balancer**.
2. En la pestaña **Datos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información, acepte los valores predeterminados del resto de la configuración y, luego, seleccione **Revisar y crear**:

    | Configuración                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Seleccione su suscripción.    |    
    | Grupos de recursos         | Seleccione **Crear nuevo** y escriba *MyResourceGroupSLB* en el cuadro de texto.|
    | NOMBRE                   | *myLoadBalancer*                                   |
    | Region         | Seleccione **Europa Occidental**.                                        |
    | Type          | Seleccione **Público**.                                        |
    | SKU           | Seleccione **Estándar**.                          |
    | Dirección IP pública | Seleccione **Crear nuevo**. |
    | Nombre de la dirección IP pública              | Escriba *myPublicIP* en el cuadro de texto.   |
    |Zona de disponibilidad| Seleccione **Redundancia de zona**.    |
3. En la pestaña **Revisar + crear**, seleccione **Crear**.   

    ![Crear un equilibrador de carga estándar](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Crear recursos de Load Balancer

En esta sección se configura Load Balancer para un grupo de direcciones de back-end y un sondeo de estado, y se especifica una regla del equilibrador de carga.

### <a name="create-a-backend-address-pool"></a>Creación de un grupo de direcciones de back-end

Para distribuir el tráfico a las máquinas virtuales, un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red (NIC) virtuales conectadas a Load Balancer. Cree el grupo de direcciones de back-end *myBackendPool* para incluir máquinas virtuales para el tráfico de Internet de equilibrio de carga.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.
2. En **Configuración**, seleccione **Grupos de back-end** y, a continuación, seleccione **Agregar**.
3. En la página **Agregar un grupo back-end**, en nombre, escriba *myBackEndPool*, como el nombre del grupo de back-end y, a continuación, seleccione **Aceptar**.

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado

Para permitir que Load Balancer supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación de Load Balancer en base a su respuesta a las comprobaciones de estado. Cree un sondeo de estado, *myHealthProbe*, para supervisar el estado de las máquinas virtuales.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.
2. En **Configuración**, seleccione **Sondeos de estado** y, a continuación, seleccione **Agregar**.
    
    | Configuración | Valor |
    | ------- | ----- |
    | NOMBRE | Escriba *myHealthProbe*. |
    | Protocolo | Seleccione **HTTP**. |
    | Port | Escriba *80*.|
    | Intervalo | Escriba *15* como número de **Intervalo**, en segundos, entre los intentos de sondeo. |
    | Umbral incorrecto | Seleccione **2** como número de **Umbral incorrecto** o errores de sondeo consecutivos que deben producirse para que una máquina virtual se considere que no funciona de manera correcta.|
    | | |
4. Seleccione **Aceptar**.

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de Load Balancer
Las reglas de Load Balancer se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Defina la configuración de la IP de front-end para el tráfico entrante y el grupo de IP de back-end para el tráfico entrante, junto con los puertos de origen y destino requeridos. Cree una regla de Load Balancer *myLoadBalancerRuleWeb* para escuchar el puerto 80 en el front-end *FrontendLoadBalancer* y enviar tráfico de red con equilibrio de carga al conjunto de direcciones de back-end, *myBackEndPool*, que también usan el puerto 80. 

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos, **myLoadBalancer**.
2. En **Configuración**, seleccione **Reglas de equilibrio de carga** y, a continuación, seleccione **Agregar**.
3. Use estos valores para configurar la regla de equilibrio de carga:
    
    | Configuración | Valor |
    | ------- | ----- |
    | NOMBRE | Escriba *myHTTPRule*. |
    | Protocolo | seleccione **TCP**. |
    | Port | Escriba *80*.|
    | Puerto back-end | Escriba *80*. |
    | Grupo back-end | Seleccione *MyBackendPool*.|
    | Sondeo de mantenimiento | Seleccione *myHealthProbe*. |
4. Deje el resto de valores predeterminados y después seleccione **Aceptar**.


## <a name="create-backend-servers"></a>Creación de servidores back-end

En esta sección, se crea una red virtual, luego tres máquinas virtuales para el grupo de back-end de la instancia de Load Balancer y, finalmente, se instala IIS en las máquinas virtuales para ayudar a probar el equilibrador de carga.

### <a name="create-a-virtual-network"></a>Creación de una red virtual
1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso** > **Redes** > **Red virtual**.

1. En **Creación de una red virtual**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | NOMBRE | Escriba *myVNet*. |
    | Espacio de direcciones | Escriba *10.1.0.0/16*. |
    | Subscription | Seleccione su suscripción.|
    | Grupos de recursos | Seleccione el recurso existente: *myResourceGroupSLB*. |
    | Ubicación | Seleccione **Europa Occidental**.|
    | Subred: nombre | Escriba *myBackendSubnet*. |
    | Subred: intervalo de direcciones | Escriba *10.1.0.0/24*. |
1. Deje el resto de valores predeterminados y seleccione **Crear**.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales
La instancia de Standard Load Balancer solo admite las máquinas virtuales con direcciones IP estándar en el grupo de back-end. En esta sección, creará tres máquinas virtuales (*myVM1*, *myVM2* y *myVM3*) con una dirección IP pública estándar en tres zonas diferentes (*Zona 1*, *Zona 2* y *Zona 3*) que se agregan más adelante al grupo de back-end de la instancia de Standard Load Balancer que se creó anteriormente.

1. En la parte superior izquierda del portal, seleccione **Crear un recurso** > **Proceso** > **Windows Server 2019 Datacenter**. 
   
1. En **Crear una máquina virtual**, escriba o seleccione los valores siguientes en la pestaña **Básico**:
   - **Suscripción** > **Grupo de recursos**: Seleccione **myResourceGroupSLB**.
   - **Detalles de instancia** > **Nombre de máquina virtual**: Escriba *myVM1*.
   - **Detalles de instancia** > **Región** > seleccione **Oeste de Europa**.
   - **Detalles de instancia** > **Opciones de disponibilidad** > seleccione **Zonas de disponibilidad**. 
   - **Detalles de instancia** > **Zona de disponibilidad** > seleccione **1**.
   - **Cuenta de administrador**> escriba la información de **Nombre de usuario**, **Contraseña** y **Confirmar contraseña**.
   - Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
1. En la pestaña **Redes**, asegúrese de que está seleccionado lo siguiente:
   - **Red virtual**: *myVnet*
   - **Subred**: *MyBackendSubnet*
   - **Dirección IP** > seleccione **Crear nuevo** y en la ventana **Crer dirección IP pública**; para **SKU**, seleccione **Estándar** y para **Zona de disponibilidad**, seleccione **Con redundancia de zona** y, a continuación, seleccione **Aceptar**.
   - Para crear un nuevo grupo de seguridad de red (NSG), un tipo de firewall, en **Grupo de seguridad de red**, seleccione **Avanzado**. 
       1. En el campo **Configurar grupo de seguridad de red**, seleccione **Crear nuevo**. 
       1. Escriba *MyNetworkSecurityGroup* y seleccione **Aceptar**.
   - Para que la máquina virtual forme parte del grupo de back-end de Load Balancer, complete los siguientes pasos:
        - En **Equilibrio de carga**, en **Place this virtual machine behind an existing load balancing solution?** (¿Colocar esta máquina virtual detrás de una solución de equilibrio de carga existente?), seleccione **Sí**.
        - En **Configuración de equilibrio de carga**, en **Opciones de equilibrio de carga**, seleccione **Azure Load Balancer**.
        - En **Seleccionar un equilibrador de carga**, *myLoadBalancer*.
        - Seleccione la pestaña **Administración** o seleccione **Siguiente** > **Administración**.
2. En la pestaña **Administración**, en **Supervisión**, establezca **Diagnósticos de arranque** en **Desactivado**. 
1. Seleccione **Revisar + crear**.   
1. Revise la configuración y, a continuación, seleccione **Crear**.
1. Siga los pasos 2 a 6 para crear dos máquinas virtuales adicionales con los siguientes valores, y todos los demás valores deben coincidir con los de *myVM1*:

    | Configuración | VM 2| VM 3|
    | ------- | ----- |---|
    | NOMBRE |  *myVM2* |*myVM3*|
    | Zona de disponibilidad | 2 |3|
    |Dirección IP pública| SKU **estándar**|SKU **estándar**|
    | IP pública: zona de disponibilidad| **Con redundancia de zona** |**Con redundancia de zona**|
    | Grupo de seguridad de red | Seleccionar el *grupo myNetworkSecurity* existente| Seleccionar el *grupo myNetworkSecurity* existente|

 ### <a name="create-nsg-rule"></a>Creación de la regla de grupo de seguridad de red

En esta sección, va a crear una regla de grupo de seguridad de red para permitir conexiones entrantes que usen HTTP.

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos **myNetworkSecurityGroup**, que se encuentra en el grupo de recursos **myResourceGroupSLB**.
2. En **Configuración**, seleccione **Reglas de seguridad de entrada** y, a continuación, seleccione **Agregar**.
3. Especifique estos valores para la regla de seguridad de entrada llamada *myHTTPRule* para permitir que las conexiones HTTP entrantes usen el puerto 80:
    - *Etiqueta de servicio*: en **Origen**.
    - *Internet*: en **Etiqueta de servicio de origen**
    - *80*: en **Intervalos de puerto de destino**
    - *TCP*: en **Protocolo**
    - *Permitir*: en **Acción**
    - *100* en **Prioridad**
    - *myHTTPRule* como nombre
    - *Permitir HTTP*: como descripción
4. Seleccione **Agregar**.
 
### <a name="install-iis"></a>Instalación de IIS

1. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, en la lista de recursos **myVM1**, que se encuentra en el grupo de recursos *myResourceGroupSLB*.
2. En la página **Información general**, seleccione **Conectar** para conectar mediante RDP con la máquina virtual.
5. Inicie sesión en la máquina virtual con las credenciales que proporcionó durante la creación de esta máquina virtual. Esto inicia una sesión de escritorio remoto con la máquina virtual *myVM1*.
6. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows**>**Windows PowerShell**.
7. En la ventana de PowerShell, ejecute los comandos siguientes para instalar el servidor IIS, eliminar el archivo iisstart.htm predeterminado y agregar uno nuevo que muestre el nombre de la máquina virtual:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Cierre la sesión de RDP con *myVM1*.
7. Repita los pasos 1 a 6 para instalar IIS y el archivo iisstart.htm actualizado en *myVM2* y *myVM3*.

## <a name="test-the-load-balancer"></a>Prueba de la instancia de Load Balancer
1. Busque la dirección IP pública de Load Balancer en la pantalla **Información general**. Seleccione **Todos los servicios** en el menú de la izquierda, **Todos los recursos** y, después, **myPublicIP**.

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. La página predeterminada del servidor web de IIS se muestra en el explorador.

   ![Servidor web de IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver el tráfico distribuido por Load Balancer entre las tres máquinas virtuales, puede personalizar la página predeterminada de cada servidor web de ISS de las máquinas virtuales y luego forzar una actualización del explorador web desde el equipo cliente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, Load Balancer y todos los recursos relacionados. Para ello, seleccione el grupo de recursos (*myResourceGroupSLB*) que contiene la instancia de Load Balancer y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Standard Load Balancer, le ha asociado máquinas virtuales, ha configurado la regla de tráfico de Load Balancer y el sondeo de estado y, después, ha probado la instancia de Load Balancer. Para más información acerca de Azure Load Balancer, diríjase a los tutoriales correspondientes.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
