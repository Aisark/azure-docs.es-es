---
title: 'Tutorial: Uso de la versión preliminar de Azure Firewall Manager para proteger la red en la nube mediante Azure Portal'
description: En este tutorial aprenderá a proteger la red en la nube con Azure Firewall Manager en Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510039"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Tutorial: Protección de la red en la nube con la versión preliminar de Azure Firewall Manager en Azure Portal

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Mediante la versión preliminar de Azure Firewall Manager puede crear centros protegidos y así proteger el tráfico de la red en la nube destinado a direcciones IP privadas, PaaS de Azure e Internet. El enrutamiento del tráfico al firewall es automático, por lo que no es necesario crear rutas definidas por el usuario (UDR).

![protección de la red en la nube](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> La versión preliminar de Azure Firewall Manager se debe habilitar explícitamente con el comando `Register-AzProviderFeature` de PowerShell.

Desde el símbolo del sistema de PowerShell, ejecute los siguientes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
El registro de la característica tarda un máximo de 30 minutos en completarse. Ejecute el siguiente comando para comprobar el estado del registro:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Creación de una arquitectura en estrella tipo hub-and-spoke

En primer lugar, cree una red virtual de radio donde pueda colocar los servidores.

### <a name="create-a-spoke-vnet-and-subnets"></a>Creación de una red virtual de radio y subredes

1. En la página principal de Azure Portal, seleccione **Crear un recurso**.
2. En **Redes**, seleccione **Red virtuales**.
4. En **Nombre**, escriba **Spoke-01**.
5. En **Espacio de direcciones**, escriba **10.0.0.0/16**.
6. En **Suscripción**, seleccione la suscripción.
7. En **Grupo de recursos**, seleccione **Crear nuevo**, escriba **FW-Manager** como nombre y seleccione **Aceptar**.
8. En **Ubicación**, seleccione **(EE. UU.) Este de EE. UU.**
9. En **Subred**, como **Nombre** escriba **Workload-SN**.
10. En **Intervalo de direcciones**, escriba **10.0.1.0/24**.
11. Acepte los restantes valores predeterminados y seleccione **Crear**.

A continuación, cree una subred para un servidor de salto.

1. En la página principal de Azure Portal, seleccione **Grupos de recursos** > **FW-Manager**.
2. Seleccione la red virtual **Spoke-01**.
3. Seleccione **Subredes** >  **+Subred**.
4. En **Nombre**, escriba **Jump-SN**.
5. En **Intervalo de direcciones**, escriba **10.0.2.0/24**.
6. Seleccione **Aceptar**.

### <a name="create-the-secured-virtual-hub"></a>Creación del centro virtual protegido

Cree el centro virtual protegido con Firewall Manager.

1. En la página principal de Azure Portal, seleccione **Todos los servicios**.
2. En el cuadro de búsqueda, escriba **Firewall Manager** y seleccione **Firewall Manager**.
3. En la página **Firewall Manager**, seleccione **Crear un centro virtual protegido**.
4. En la página **Crear un nuevo centro virtual protegido**, seleccione su suscripción y el grupo de recursos **FW-Manager**.
5. En **Nombre del centro virtual protegido**, escriba **Hub-01**.
6. En **Ubicación**, seleccione **Este de EE.UU.**
7. En **Espacio de direcciones del concentrador**, escriba **10.1.0.0/16**.
8. Como nombre de la nueva WAN virtual, escriba **vwan-01**.
9. Deje desactivada la casilla **Incluir VPN Gateway para habilitar asociados de seguridad de confianza**.
10. Seleccione **Siguiente: Azure Firewall**.
11. Acepte el valor predeterminado **Azure Firewall** **Habilitado** y, a continuación, seleccione **Siguiente: Asociados de seguridad de confianza**.
12. Acepte el valor predeterminado **Asociados de seguridad de confianza** **Deshabilitado** y seleccione **Siguiente: Review + create** (Revisar y crear).
13. Seleccione **Crear**. La implementación tardará unos 30 minutos.

### <a name="connect-the-hub-and-spoke-vnets"></a>Conexión de las redes virtuales en estrella tipo hub-and-spoke

Ahora puede emparejar las redes virtuales en estrella de tipo hub-and-spoke.

1. Seleccione el grupo de recursos **FW-Manager** y, después, seleccione la WAN virtual **vwan-01**.
2. En **Conectividad**, seleccione **Conexiones de red virtual**.
3. Seleccione **Agregar conexión**.
4. En **Nombre de conexión**, escriba **hub-spoke**.
5. En **Centros**, seleccione **Hub-01**.
6. En **Red virtual**, seleccione **Spoke-01**.
7. Seleccione **Aceptar**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Creación de una directiva de firewall y protección del centro

Una directiva de firewall define colecciones de reglas para dirigir el tráfico en uno o varios centros virtuales protegidos. Creará la directiva de firewall y, a continuación, protegerá el centro.

1. En Firewall Manager, seleccione **Crear una directiva de Azure Firewall**.
2. Seleccione la suscripción y, a continuación, seleccione el grupo de recursos **FW-Manager**.
3. En **Detalles de la directiva**, como **Nombre** escriba **Policy-01** y como **Región** seleccione **Este de EE. UU.**
4. Seleccione **Siguiente: Reglas**.
5. En la pestaña **Reglas**, seleccione **Agregar una colección de reglas**.
6. En la página **Agregar una colección de reglas**, escriba **RC-01** en **Nombre**.
7. En **Tipo de colección de reglas**, seleccione **Aplicación**.
8. En **Prioridad**, escriba **100**.
9. Asegúrese de que el valor de **Rule collection action** (Acción de la colección de reglas) es **Permitir**.
10. Como **Nombre** de la regla, escriba **Allow-msft**.
11. En **Dirección de origen**, escriba **\*** .
12. En **Protocolo**, escriba **http,https**.
13. Asegúrese de que **Tipo de destino es **FQDN**.
14. En **Destino**, escriba **\*.microsoft.com**.
15. Seleccione **Agregar**.
16. Seleccione **Siguiente: Centros virtuales protegidos**.
17. En la pestaña **Centros virtuales protegidos**, seleccione **Hub-01**.
19. Seleccione **Revisar + crear**.
20. Seleccione **Crear**.

Esta acción puede tardar unos cinco minutos o más en finalizar.

## <a name="route-traffic-to-your-hub"></a>Enrutamiento del tráfico al centro

Ahora debe asegurarse de que el tráfico de red se enruta a través del firewall.

1. En Firewall Manager, seleccione **Centros virtuales protegidos**.
2. Seleccione **Hub-01**.
3. En **Configuración**, seleccione **Configuración de la ruta**.
4. En **Tráfico de Internet**, **Tráfico de las redes virtuales**, seleccione **Send via Azure Firewall** (Enviar a través de Azure Firewall).
5. En **Tráfico privado de Azure**, **Tráfico a las redes virtuales**, seleccione **Send via Azure Firewall** (Enviar a través de Azure Firewall).
6. Seleccione **Editar los prefijos de dirección IP**.
7. Seleccione **Agregar un prefijo de dirección IP**.
8. Escriba **10.0.1.0/24** como dirección de la subred de carga de trabajo y seleccione **Guardar**.
9. En **Configuración**, seleccione **Conexiones**.
10. Seleccione la conexión **hub-spoke** y, después, seleccione **Proteger el tráfico de Internet**; a continuación, seleccione **Aceptar**.


## <a name="test-your-firewall"></a>Prueba del firewall

Para probar las reglas de firewall, debe implementar un par de servidores. Implementará Workload-Srv en la subred Workload-SN para probar las reglas de firewall y Jump-Srv para poder usar Escritorio remoto para conectarse desde Internet y, a continuación, conectarse a Workload-Srv.

### <a name="deploy-the-servers"></a>Implementación de los servidores

1. En Azure Portal, seleccione **Crear un recurso**.
2. Seleccione **Windows Server 2016 Datacenter** en la lista **Popular**.
3. Especifique estos valores para la máquina virtual:

   |Configuración  |Valor  |
   |---------|---------|
   |Resource group     |**FW-Manager**|
   |Nombre de la máquina virtual     |**Jump-Srv**|
   |Region     |**(EE. UU.) Este de EE. UU.**|
   |Nombre de usuario del administrador     |**azureuser**|
   |Contraseña     |**Azure123456!**|

4. En **Reglas de puerto de entrada**, en **Puertos de entrada públicos**, seleccione **Permitir los puertos seleccionados**.
5. En **Seleccionar puertos de entrada**, seleccione **RDP (3389)** .

6. Acepte los restantes valores predeterminados y seleccione **Siguiente: Discos**.
7. Acepte los valores predeterminados del disco y seleccione **Siguiente: Redes**.
8. Asegúrese de que **Spoke-01** está seleccionada como red virtual y de que la subred es **Jump-SN**.
9. En **IP pública**, acepte el nombre predeterminado de la nueva dirección IP pública (Jump-Srv-ip).
11. Acepte los restantes valores predeterminados y seleccione **Siguiente: Administración**.
12. Seleccione **Desactivar** para deshabilitar los diagnósticos de arranque. Acepte los restantes valores predeterminados y seleccione **Revisar y crear**.
13. Revise la configuración en la página de resumen y seleccione **Crear**.

Use la información de la tabla siguiente para configurar otra máquina virtual llamada **Workload-Srv**. El resto de la configuración es la misma que la de la máquina virtual Srv-Jump.

|Configuración  |Valor  |
|---------|---------|
|Subnet|**Workload-SN**|
|Dirección IP pública|**None**|
|Puertos de entrada públicos|**None**|

### <a name="add-a-route-table-and-default-route"></a>Incorporación de una tabla de rutas y una ruta predeterminada

Para permitir una conexión de Internet a Jump-Srv, debe crear una tabla de rutas y una ruta de puerta de enlace predeterminada a Internet desde la subred **Jump-SN**.

1. En Azure Portal, seleccione **Crear un recurso**.
2. Escriba **tabla de rutas** en el cuadro de búsqueda y, a continuación, seleccione **Tabla de rutas**.
3. Seleccione **Crear**.
4. Escriba **RT-01** en **Nombre**.
5. Seleccione la suscripción, el grupo de recursos **FW-Manager** y la región **(EE. UU.) Este de EE. UU.**
6. Seleccione **Crear**.
7. Una vez finalizada la implementación, seleccione la tabla de rutas **RT-01**.
8. Seleccione **Rutas** y después **Agregar**.
9. Escriba **jump-to-inet** como **Nombre de ruta**.
10. Escriba **0.0.0.0/0** como **Prefijo de dirección**.
11. Seleccione **Internet** como **Tipo del próximo salto**.
12. Seleccione **Aceptar**.
13. Una vez finalizada la implementación, seleccione **Subredes** y, a continuación, seleccione **Asociar**.
14. Seleccione **Spoke-01** como **Red virtual**.
15. Seleccione **Jump-SN** como **Subred**.
16. Seleccione **Aceptar**.

### <a name="test-the-rules"></a>Prueba de las reglas

Ahora, pruebe las reglas de firewall para confirmar que funcionan según lo previsto.

1. En Azure Portal, revise la configuración de red de la máquina virtual **Workload-Srv** y anote la dirección IP privada.
2. Conecte un escritorio remoto a la máquina virtual **Jump-Srv** e inicie sesión. Desde ahí, abra una conexión del escritorio remoto a la dirección IP privada de **Workload-Srv**.

3. Abra Internet Explorer y vaya a https://www.microsoft.com.
4. Seleccione **Aceptar** > **Cerrar** en las alertas de seguridad de Internet Explorer.

   Debería ver la página principal de Microsoft.

5. Vaya a https://www.google.com.

   El firewall debería bloquearle.

Con ello, ha comprobado que las reglas de firewall funcionan:

* Puede navegar al FQDN permitido pero no a ningún otro.



## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre los asociados de seguridad de confianza](trusted-security-partners.md)
