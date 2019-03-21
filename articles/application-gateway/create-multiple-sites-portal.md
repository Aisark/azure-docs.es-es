---
title: 'Crear una puerta de enlace de aplicaciones que hospede varios sitios web: Azure Portal'
description: Aprenda a crear una puerta de enlace de aplicaciones que hospede varios sitios web mediante Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/20/2019
ms.author: victorh
ms.openlocfilehash: 86be94404e7ab492beeebd6a467d23e68e7bce6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080174"
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Creación y configuración de una puerta de enlace de aplicaciones que hospede varios sitios web mediante Azure Portal

Puede usar Azure Portal para configurar el [hospedaje de varios sitios web](multiple-site-overview.md) al crear una [puerta de enlace de aplicaciones](overview.md). En este artículo se definen grupos de direcciones de back-end mediante máquinas virtuales. Después, configurará agentes de escucha y reglas basados en los dominios que posee para asegurarse de que el tráfico web llega a los servidores adecuados en los grupos. En este artículo se da por supuesto que posee varios dominios y se van a utilizar los ejemplos de *www.contoso.com* y *www.fabrikam.com*.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una puerta de enlace de aplicaciones
> * Crear máquinas virtuales para servidores back-end
> * Crear grupos de back-end con los servidores back-end
> * Crear un agente de escucha de back-end
> * Crear reglas de enrutamiento
> * Creación de un registro CNAME en el dominio

![Ejemplo de enrutamiento de varios sitios](./media/create-multiple-sites-portal/scenario.png)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Se necesita una red virtual para la comunicación entre los recursos que se crean. En este ejemplo se crean dos subredes: una para la puerta de enlace de aplicaciones y la otra para los servidores back-end. Puede crear una red virtual a la vez que crea la puerta de enlace de aplicaciones.

1. Haga clic en **Nuevo** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Application Gateway** en la lista de destacados.
3. Especifique estos valores para la puerta de enlace de aplicaciones:

   - *myAppGateway*: como nombre de la puerta de enlace de aplicaciones.
   - *myResourceGroupAG*: como nuevo grupo de recursos.

     ![Creación de una nueva puerta de enlace de aplicaciones](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual**, luego en **Crear nueva** y, después, especifique estos valores para la red virtual:

   - *myVNet*: como nombre de la red virtual.
   - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
   - *myAGSubnet*: como nombre de subred.
   - *10.0.0.0/24*: como espacio de direcciones de la subred.

     ![Creación de una red virtual](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
7. Haga clic en **Elegir una dirección IP pública** y en **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Acepte los valores predeterminados para la configuración del agente de escucha, deje el firewall de aplicaciones web deshabilitado y haga clic en **Aceptar**.
9. Revise la configuración en la página de resumen y, a continuación, haga clic en **Aceptar** para crear los recursos de red y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar varios minutos, espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** y en **Subred**.

    ![Creación de una subred](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Escriba *myBackendSubnet* como nombre de la subred y, a continuación, haga clic en **Aceptar**.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En este ejemplo, se crean dos máquinas virtuales que se usarán como servidores back-end para la puerta de enlace de aplicaciones. También va a instalar IIS en las máquinas virtuales para comprobar que el tráfico se enruta correctamente.

1. Haga clic en **Nuevo**.
2. Haga clic en **Compute** y, después, seleccione **Windows Server 2016 Datacenter** en la lista de destacados.
3. Especifique estos valores para la máquina virtual:

    - *contosoVM*: como nombre de la máquina virtual.
    - *azureuser*: como nombre del usuario administrador.
    - *Azure123456!* como contraseña.
    - Seleccione **Usar existente** y *myResourceGroupAG*.

4. Haga clic en **OK**.
5. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
6. Asegúrese de que **myVNet** está seleccionada como red virtual y que la subred es **myBackendSubnet**. 
7. Haga clic en **Deshabilitado** para deshabilitar los diagnósticos de arranque.
8. Haga clic en **Aceptar**, revise la configuración en la página de resumen y haga clic en **Crear**.

### <a name="install-iis"></a>Instalación de IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra el shell interactivo y asegúrese de que está establecido en **PowerShell**.

    ![Instalación de la extensión personalizada](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Ejecute el siguiente comando para instalar IIS en la máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Cree la segunda máquina virtual e instale IIS siguiendo los pasos que acaba de finalizar. Escriba los nombres de *fabrikamVM* para el nombre y el valor de VMName en Set-AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Creación de grupos de servidores back-end con las máquinas virtuales

1. Haga clic en **Todos los recursos** y, a continuación, haga clic en **myAppGateway**.
2. Haga clic en **Grupos de back-end** y en **Agregar**.
3. Escriba el nombre *contosoPool* y agregue *contosoVM* utilizando **Agregar destino**.

    ![Incorporación de servidores back-end](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Haga clic en **OK**.
5. Haga clic en **Grupos de back-end** y, a continuación, haga clic en **Agregar**.
6. Cree el grupo *fabrikamPool* con *fabrikamVM* siguiendo los pasos que acaba de finalizar.

## <a name="create-backend-listeners"></a>Crear un agente de escucha de back-end

1. Haga clic en **Agentes de escucha** y en **Multisitio**.
2. Especifique estos valores para el agente de escucha:
    
   - *contosoListener*: como nombre del agente de escucha.
   - *www.contoso.com*: sustituya este nombre de host de ejemplo por el nombre del dominio.

3. Haga clic en **OK**.
4. Cree otro agente de escucha con el nombre *fabrikamListener* y utilice el segundo nombre de dominio. En este ejemplo, vamos a utilizar *www.fabrikam.com*.

![agentes de escucha de varios sitios](media/create-multiple-sites-portal/be-listeners.png)

## <a name="create-routing-rules"></a>Crear reglas de enrutamiento

Las reglas se procesan en el orden en que aparecen y el tráfico se dirige utilizando la primera regla que dé una coincidencia, sin tener en cuenta su especificidad. Por ejemplo, si tiene una regla que usa un agente de escucha básico y una regla que usa un agente de escucha multisitio en el mismo puerto, la regla con el agente de escucha multisitio debe aparecer antes que la regla con el agente de escucha básico para que la regla multisitio funcione según lo previsto. 

En este ejemplo, va a crear dos reglas nuevas y a eliminar la regla predeterminada que se creó junto con la puerta de enlace de aplicaciones. 

1. Haga clic en **Reglas** y en **Básica**.
2. Escriba *contosoRule* como nombre.
3. Seleccione *contosoListener* como agente de escucha.
4. Seleccione *contosoPool* como grupo de back-end.

    ![Creación de una regla basada en ruta de acceso](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Haga clic en **OK**.
6. Cree una segunda regla usando los nombres *fabrikamRule*, *fabrikamListener* y *fabrikamPool*.
7. Elimine la regla predeterminada *rule1*; para ello, selecciónela y haga clic después en **Eliminar**.

## <a name="create-a-cname-record-in-your-domain"></a>Creación de un registro CNAME en el dominio

Después de crear la puerta de enlace de aplicaciones con la dirección IP pública, puede obtener la dirección DNS y usarla para crear un registro CNAME en el dominio. No se recomienda el uso de registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

1. Haga clic en **Todos los recursos** y, a continuación, haga clic en **myAGPublicIPAddress**.

    ![Registro de la dirección DNS de la puerta de enlace de aplicaciones](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Copie la dirección DNS y utilícela como valor en un nuevo registro CNAME del dominio.

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

1. Escriba el nombre de dominio en la barra de direcciones del explorador. Por ejemplo, http://www.contoso.com.

    ![Prueba del sitio de contoso en la puerta de enlace de aplicaciones](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Cambie la dirección al otro dominio. Debería ver algo parecido al ejemplo siguiente:

    ![Prueba del sitio de fabrikam en la puerta de enlace de aplicaciones](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Pasos siguientes

[Configuración de App Service con Application Gateway](create-web-app.md)