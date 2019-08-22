---
title: Uso de las plantillas de inicio rápido de Azure para configurar un grupo de disponibilidad Always On para SQL Server en una máquina virtual de Azure
description: Use las plantillas de inicio rápido de Azure para crear el clúster de conmutación por error de Windows, unir las máquinas virtuales con SQL Server al clúster, crear el cliente de escucha y configurar el equilibrador de carga interno en Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f95d3487adecb17e0f4b79e81a08e16bafe4594f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855249"
---
# <a name="use-azure-quickstart-templates-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Uso de las plantillas de inicio rápido de Azure para configurar un grupo de disponibilidad Always On para SQL Server en una máquina virtual de Azure
En este artículo se describe cómo usar las plantillas de inicio rápido de Azure para automatizar parcialmente la implementación de una configuración de grupo de disponibilidad Always On para máquinas virtuales con SQL Server en Azure. Son dos las plantillas de inicio rápido de Azure que se usan en este proceso: 

   | Plantilla | DESCRIPCIÓN |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crea el clúster de conmutación por error de Windows y une a él las máquinas virtuales con SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea la escucha de grupo de disponibilidad y configura el equilibrador de carga interno. Esta plantilla solo se puede usar si se creó el clúster de conmutación por error de Windows con la plantilla **101-sql-vm-ag-setup**. |
   | &nbsp; | &nbsp; |

Otras partes de la configuración del grupo de disponibilidad deben realizarse manualmente, como la creación del grupo de disponibilidad y del equilibrador de carga interno. En este artículo se proporciona la secuencia de pasos manuales y automatizados.
 

## <a name="prerequisites"></a>Requisitos previos 
Para automatizar la configuración de un grupo de disponibilidad Always On mediante plantillas de inicio rápido, debe cumplir los siguientes requisitos previos: 
- Una [suscripción de Azure](https://azure.microsoft.com/free/).
- Un grupo de recursos con un controlador de dominio. 
- Una o varias [máquinas virtuales en Azure, unidas a un dominio, con SQL Server 2016 (o superior) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) en el mismo conjunto o zona de disponibilidad que se hayan [registrado con el proveedor de recursos de máquina virtual con SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Dos direcciones IP disponibles (que no use ninguna entidad), una para el equilibrador de carga interno y otra para la escucha de grupo de disponibilidad, dentro de la misma subred que el grupo de disponibilidad. Si se utiliza un equilibrador de carga existente, solo será necesaria una dirección IP disponible.  

## <a name="permissions"></a>Permisos
Los siguientes permisos son necesarios para configurar el grupo de disponibilidad Always On mediante las plantillas de inicio rápido de Azure: 

- Una cuenta de usuario de dominio existente que tenga el permiso **Create Computer Object** en el dominio.  Por ejemplo, una cuenta de administrador de dominio normalmente tiene permisos suficientes (p. ej.: account@domain.com). _Esta cuenta también debe formar parte del grupo de administradores local en cada máquina virtual para crear el clúster._
- La cuenta de usuario del dominio que controla el servicio SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Paso 1: Crear el clúster de conmutación por error y unir máquinas virtuales con SQL Server al clúster mediante una plantilla de inicio rápido 
Una vez que se han registrado las máquinas virtuales con SQL Server con el nuevo proveedor de recursos de máquina virtual con SQL, puede unirlas a *SqlVirtualMachineGroups*. Este recurso define los metadatos del clúster de conmutación por error de Windows. Los metadatos incluyen la versión, la edición, el nombre de dominio completo, las cuentas de Active Directory para administrar tanto el clúster como el servicio SQL Server, y la cuenta de almacenamiento como el testigo en la nube. 

La adición de las VM con SQL Server al grupo de recursos *SqlVirtualMachineGroups* arranca el servicio de clúster de conmutación por error de Windows para crear el clúster y luego une las máquinas virtuales con SQL Server al clúster. Este paso se automatiza con la plantilla de inicio rápido **101-sql-vm-ag-setup**. Puede implementarlo siguiendo estos pasos:

1. Vaya a la plantilla de inicio rápido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup). Después, seleccione **Implementar en Azure** para abrir la plantilla de inicio rápido en Azure Portal.
1. Rellene los campos obligatorios para configurar los metadatos del clúster de conmutación por error de Windows. Puede dejar los campos opcionales en blanco.

   En la tabla siguiente se muestran los valores necesarios para la plantilla: 

   | **Campo** | Valor |
   | --- | --- |
   | **Suscripción** |  La suscripción donde se encuentran sus máquinas virtuales con SQL Server. |
   |**Grupos de recursos** | El grupo de recursos donde residen sus máquinas virtuales con SQL Server. | 
   |**Failover Cluster Name** (Nombre del clúster de conmutación por error) | El nombre que desea para el nuevo clúster de conmutación por error de Windows. |
   | **Existing Vm List** (Lista de máquinas virtuales existentes) | Las máquinas virtuales con SQL Server que quiere que participen en el grupo de disponibilidad y que, por lo tanto, formen parte de este nuevo clúster. Separe estos valores con una coma y un espacio (por ejemplo: *SQLVM1, SQLVM2*). |
   | **SQL Server Version** (Versión de SQL Server) | La versión de SQL Server de las máquinas virtuales con SQL Server. Selecciónela en la lista desplegable. Actualmente, solo se admiten imágenes de SQL Server 2016 y SQL Server 2017. |
   | **Existing Fully Qualified Domain Name** (Nombre de dominio completo existente) | El FQDN existente del dominio en el que residen sus máquinas virtuales con SQL Server. |
   | **Existing Domain Account** (Cuenta de dominio existente) | Una cuenta de usuario de dominio existente que tenga el permiso **Create Computer Object** en el dominio cuando el [CNO](/windows-server/failover-clustering/prestage-cluster-adds) se crea durante la implementación de la plantilla. Por ejemplo, una cuenta de administrador de dominio normalmente tiene permisos suficientes (p. ej.: account@domain.com). *Esta cuenta también debe formar parte del grupo de administradores local en cada máquina virtual para crear el clúster.*| 
   | **Domain Account Password** (Contraseña de la cuenta de dominio) | La contraseña de la cuenta de usuario de dominio mencionada anteriormente. | 
   | **Existing Sql Service Account** (Cuenta del servicio SQL existente) | La cuenta de usuario de dominio que controla el [servicio SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante la implementación del grupo de disponibilidad (p. ej.: account@domain.com). |
   | **Sql Service Password** (Contraseña del servicio SQL) | La contraseña usada por la cuenta de usuario del dominio que controla el servicio SQL Server. |
   | **Cloud Witness Name** (Nombre del testigo en la nube) | Se trata de una nueva cuenta de almacenamiento de Azure que se creará y usará para el testigo en la nube. Este nombre se puede modificar. |
   | **\_artifacts Location** (Ubicación de artefactos) | Este campo se configura de forma predeterminada y no debe modificarse. |
   | **\_artifacts Location Sas Token** (Token de Sas de ubicación de artefactos) | Este campo se deja en blanco intencionadamente. |
   | &nbsp; | &nbsp; |

1. Si acepta los términos y condiciones, active la casilla **Acepto los términos y condiciones indicados anteriormente**. Después, seleccione **Comprar** para finalizar la implementación de la plantilla de inicio rápido. 
1. Para supervisar la implementación, seleccione la implementación en el icono de la campana **Notificaciones** del banner de navegación superior o vaya a **Grupo de recursos** en Azure Portal. Seleccione **Implementaciones** en **Configuración** y elija la implementación **Microsoft.Template**. 

>[!NOTE]
> Las credenciales proporcionadas durante la implementación de la plantilla se almacenan solo mientras dure dicha implementación. Una vez finalizada la implementación, se quitan esas contraseñas. Se le pedirá que las proporcione de nuevo si agrega más máquinas virtuales con SQL Server al clúster. 


## <a name="step-2-manually-create-the-availability-group"></a>Paso 2: Crear el grupo de disponibilidad de forma manual 
Cree el grupo de disponibilidad manualmente del modo habitual, ya sea mediante [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *No* cree un cliente de escucha en este momento porque lo hace automáticamente la plantilla de inicio rápido **101-sql-vm-aglistener-setup** en el paso 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Paso 3: Crear el equilibrador de carga interno de forma manual
La escucha de grupo de disponibilidad Always On requiere una instancia interna de Azure Load Balancer. El equilibrador de carga interno proporciona una dirección IP "flotante" para la escucha de grupo de disponibilidad que permite conmutar por error y volver a conectarse de manera más rápida. Si las máquinas virtuales con SQL Server de un grupo de disponibilidad forman parte del mismo conjunto de disponibilidad, puede usar un equilibrador de carga básico. De lo contrario, debe usar uno estándar. 

> [!IMPORTANT]
> El equilibrador de carga interno debe estar en la misma red virtual que las instancias de máquina virtual con SQL Server. 

Solo tiene que crear el equilibrador de carga interno. En el paso 4, la plantilla de inicio rápido **101-sql-vm-aglistener-setup** controla el resto de la configuración (por ejemplo, el grupo de back-end, el sondeo de estado y las reglas de equilibrio de carga). 

1. En el Portal de Azure, abra el grupo de recursos que contiene las máquinas virtuales de SQL Server. 
2. En el grupo de recursos, seleccione **Agregar**.
3. Busque el **equilibrador de carga**. En los resultados de la búsqueda, seleccione **Load Balancer**, publicado por **Microsoft**.
4. En la hoja **Load Balancer**, haga clic en **Crear**.
5. En el cuadro de diálogo **Crear equilibrador de carga**, configure el equilibrador de carga tal y como se explica a continuación:

   | Configuración | Valor |
   | --- | --- |
   | **Nombre** |Escriba un nombre de texto que represente el equilibrador de carga. Por ejemplo, **sqlLB**. |
   | **Tipo** |**Internas**: en la mayoría de las implementaciones se usa un equilibrador de carga interno que permite que las aplicaciones dentro de la misma red virtual se conecten al grupo de disponibilidad.  </br> **Externas**: permite que las aplicaciones se conecten al grupo de disponibilidad mediante una conexión a Internet pública. |
   | **Red virtual** | Seleccione la red virtual en la que se encuentran las instancias de SQL Server. |
   | **Subred** | Seleccione la subred en la que se encuentran las instancias de SQL Server. |
   | **Asignación de dirección IP** |**Estática** |
   | **Dirección IP privada** | Especifique una dirección IP disponible de la subred. |
   | **Suscripción** |Si tiene varias suscripciones, puede aparecer este campo. Seleccione la suscripción que quiere asociar con este recurso. Normalmente, será la misma suscripción que se utiliza con todos los recursos del grupo de disponibilidad. |
   | **Grupos de recursos** |Seleccione el grupo de recursos en el que se encuentran las instancias de SQL Server. |
   | **Ubicación** |Seleccione la ubicación de Azure en la que se encuentran las instancias de SQL Server. |
   | &nbsp; | &nbsp; |

6. Seleccione **Crear**. 


>[!IMPORTANT]
> El recurso de IP pública de cada máquina virtual con SQL Server debe tener una SKU estándar para que sea compatible con el equilibrador de carga estándar. Para determinar la SKU del recurso de IP pública de la máquina virtual, vaya a **Grupo de recursos**, seleccione su recurso **Dirección IP pública** para la máquina virtual con SQL Server y busque el valor que aparece debajo de **SKU** en el panel **Información general**. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Paso 4: Crear la escucha de grupo de disponibilidad y configurar el equilibrador de carga interno mediante la plantilla de inicio rápido

Cree la escucha de grupo de disponibilidad y configure el equilibrador de carga interno automáticamente mediante la plantilla de inicio rápido **101-sql-vm-aglistener-setup**. La plantilla aprovisiona el recurso Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. La plantilla de inicio rápido  **101-sql-vm-aglistener-setup** realiza las siguientes acciones mediante el proveedor de recursos de máquina virtual con SQL:

- Crea un nuevo recurso IP de front-end (según el valor de dirección IP proporcionado durante la implementación) para el agente de escucha. 
- Ajusta la configuración de red del clúster y el equilibrador de carga interno. 
- Configura el grupo de back-end del equilibrador de carga interno, el sondeo de estado y las reglas de equilibrio de carga.
- Crea la escucha de grupo de disponibilidad con el nombre y la dirección IP especificados.
 
>[!NOTE]
> Puede usar la plantilla **101-sql-vm-aglistener-setup** solo si se creó el clúster de conmutación por error de Windows con la plantilla **101-sql-vm-ag-setup**.
   
   
Para configurar el equilibrador de carga interno y crear la escucha de grupo de disponibilidad, haga lo siguiente:
1. Vaya a la plantilla de inicio rápido [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) y seleccione **Implementar en Azure** para iniciar la plantilla de inicio rápido en Azure Portal.
1. Rellene los campos obligatorios para configurar el equilibrador de carga interno y crear la escucha de grupo de disponibilidad. Puede dejar los campos opcionales en blanco. 

   En la tabla siguiente se muestran los valores necesarios para la plantilla: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupos de recursos** | El grupo de recursos donde residen sus máquinas virtuales con SQL Server y el grupo de disponibilidad. | 
   |**Failover Cluster Name** (Nombre del clúster de conmutación por error) | El nombre del clúster al que se han unido sus máquinas virtuales con SQL Server. |
   | **Existing Sql Availability Group** (Grupo de disponibilidad de SQL existente)| El nombre del grupo de disponibilidad del que forman parte las máquinas virtuales con SQL Server. |
   | **Existing Vm List** (Lista de máquinas virtuales existentes) | Los nombres de las máquinas virtuales con SQL Server que forman parte del grupo de disponibilidad mencionado anteriormente. Separe los nombres con una coma y un espacio (por ejemplo: *SQLVM1, SQLVM2*). |
   | **Agente de escucha** | El nombre DNS que desea asignar al cliente de escucha. De forma predeterminada, esta plantilla especifica el nombre "aglistener", pero puede cambiarse. El nombre no debe superar los 15 caracteres. |
   | **Listener Port** (Puerto de escucha) | El puerto que quiere que use el cliente de escucha. Normalmente, este puerto debe ser el valor predeterminado 1433. Este es el número de puerto que especifica la plantilla. Sin embargo, si el puerto predeterminado ha cambiado, el puerto de escucha debe usar en su lugar ese valor. | 
   | **IP de agente de escucha**: | La dirección IP que desea que use el cliente de escucha. Esta dirección se creará durante la implementación de la plantilla, por lo que debe proporcionar una que no esté en uso.  |
   | **Existing Subnet** (Subred existente) | El nombre de la subred interna de sus máquinas virtuales con SQL Server (p. ej.: *el predeterminado*). Puede determinar este valor yendo a **Grupo de recursos**, seleccionando la red virtual, eligiendo **Subredes** en el panel **Configuración** y copiando el valor en **Nombre**. |
   | **Existing Internal Load Balancer** (Load Balancer interno existente) | El nombre del equilibrador de carga interno que creó en el paso 3. |
   | **Puerto de sondeo** | El puerto de sondeo que desea que utilice el equilibrador de carga interno. La plantilla usa 59999 de forma predeterminada, pero este valor se puede cambiar. |
   | &nbsp; | &nbsp; |

1. Si acepta los términos y condiciones, active la casilla **Acepto los términos y condiciones indicados anteriormente**. Seleccione **Comprar** para finalizar la implementación de la plantilla de inicio rápido. 
1. Para supervisar la implementación, seleccione la implementación en el icono de la campana **Notificaciones** del banner de navegación superior o vaya a **Grupo de recursos** en Azure Portal. Seleccione **Implementaciones** en **Configuración** y elija la implementación **Microsoft.Template**. 

>[!NOTE]
>Si la implementación genera errores a mitad del proceso, deberá [quitar el cliente de escucha recién creado](#remove-the-availability-group-listener) de forma manual mediante PowerShell antes de volver a implementar la plantilla de inicio rápido **101-sql-vm-aglistener-setup**. 

## <a name="remove-the-availability-group-listener"></a>Eliminación de la escucha de grupo de disponibilidad
Si posteriormente necesita quitar la escucha de grupo de disponibilidad configurada por la plantilla, debe pasar por el proveedor de recursos de máquina virtual con SQL. Puesto que el cliente de escucha se registra mediante este proveedor de recursos, no basta con eliminarlo mediante SQL Server Management Studio. 

El mejor método consiste en eliminarlo a través del proveedor de recursos de máquina virtual con SQL mediante el siguiente fragmento de código en PowerShell. Al hacerlo, se quitan los metadatos del agente de escucha de grupo de disponibilidad del proveedor de recursos de la máquina virtual con SQL. También elimina físicamente el cliente de escucha del grupo de disponibilidad. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Errores comunes
En esta sección se describen algunos problemas conocidos y sus posibles soluciones. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>La escucha del grupo de disponibilidad "\<AG-Name>" ya existe
El grupo de disponibilidad seleccionado que se usa en la plantilla de inicio rápido de Azure para la escucha de grupo de disponibilidad ya contiene un cliente de escucha. Se encuentra físicamente dentro del grupo de disponibilidad, o bien sus metadatos permanecen en el proveedor de recursos de máquina virtual con SQL. Quite la escucha mediante [PowerShell](#remove-the-availability-group-listener) antes de volver a implementar la plantilla de inicio rápido **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>La conexión solo funciona desde la réplica principal
Este comportamiento puede deberse a una implementación incorrecta de la plantilla **101-sql-vm-aglistener-setup** que deja la configuración del equilibrador de carga interno con un estado incoherente. Compruebe que el grupo de servidores back-end muestra el conjunto de disponibilidad y que existen reglas para el sondeo de estado y el equilibrio de carga. Si falta algo, la configuración del equilibrador de carga interno tendrá un estado incoherente. 

Para resolver este comportamiento, quite el cliente de escucha mediante [PowerShell](#remove-the-availability-group-listener), elimine el equilibrador de carga interno mediante Azure Portal y comience de nuevo en el paso 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest: solo se puede actualizar la lista de máquinas virtuales de SQL
Este error puede producirse al implementar la plantilla **101-sql-vm-aglistener-setup** si la escucha se eliminó mediante SQL Server Management Studio (SSMS), pero no se ha eliminado del proveedor de recursos de máquina virtual con SQL. Al eliminar el cliente de escucha a través de SSMS, no se quitan los metadatos del cliente de escucha del proveedor de recursos de máquina virtual con SQL. El cliente de escucha debe eliminarse del proveedor de recursos a través de [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>La cuenta de dominio no existe
Este error puede tener dos causas. O bien la cuenta de dominio especificada no existe o faltan los datos del [nombre principal de usuario (UPN)](/windows/desktop/ad/naming-properties#userprincipalname). La plantilla **101-sql-vm-ag-setup** espera una cuenta de dominio con el formato de nombre principal de usuario (es decir, *user@domain.com* ), pero es posible que falte en algunas cuentas de dominio. Esto suele suceder cuando un usuario local se ha migrado para que sea la primera cuenta de administrador de dominio cuando el servidor se promovió a un controlador de dominio, o bien cuando se creó un usuario a través de PowerShell. 

Compruebe que la cuenta existe. Si es así, puede que se encuentre en la segunda situación. Para resolver este problema, haga lo siguiente:

1. En el controlador de dominio, abra la ventana **Usuarios y equipos de Active Directory** desde la opción **Herramientas** del **Administrador del servidor**. 
2. Vaya a la cuenta seleccionando **Usuarios** en el panel izquierdo.
3. Haga clic con el botón derecho en la cuenta y seleccione **Propiedades**.
4. Seleccione la pestaña **Cuenta**. Si el cuadro **Nombre de inicio de sesión de usuario** está en blanco, esta es la causa del error. 

    ![Una cuenta de usuario en blanco indica que falta el nombre principal de usuario](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Rellene el cuadro **Nombre de inicio de sesión de usuario** para que coincida con el nombre del usuario y seleccione el dominio adecuado en la lista desplegable. 
6. Seleccione **Aplicar** para guardar los cambios y cierre el cuadro de diálogo seleccionando **Aceptar**. 

Una vez realizados estos cambios, trate de implementar la plantilla de inicio rápido de Azure una vez más. 



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos. 

* [Introducción a las máquinas virtuales con SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Preguntas más frecuentes sobre las máquinas virtuales con SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guía de precios para máquinas virtuales con SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de la versión de las máquinas virtuales con SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Cambio de los modelos de licencia para una VM con SQL Server](virtual-machines-windows-sql-ahb.md)



