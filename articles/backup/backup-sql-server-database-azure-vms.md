---
title: Copia de seguridad de bases de datos de SQL Server en máquinas virtuales de Azure | Microsoft Docs
description: En este artículo, aprenderá a realizar copias de seguridad de bases de datos de SQL Server en máquinas virtuales de Azure con Azure Backup.
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: dacurwin
ms.openlocfilehash: a6752ffcf434b81c3013a2bd43c784bc92a8c1fe
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747177"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Copia de seguridad de bases de datos de SQL Server en máquinas virtuales de Azure

Las bases de datos SQL Server son cargas de trabajo críticas que requieren un bajo objetivo de punto de recuperación (RPO) y retención a largo plazo. Puede hacer una copia de seguridad de las bases de datos de SQL Server que se ejecutan en máquinas virtuales de Azure mediante [Azure Backup](backup-overview.md).

En este artículo se muestra cómo hacer una copia de seguridad de una base de datos de SQL Server que se ejecuta en una máquina virtual de Azure en un almacén de Azure Backup Recovery Services.

En este artículo, aprenderá a:

> [!div class="checklist"]
>
> * Crear y configurar un almacén.
> * Detectar bases de datos y configurar copias de seguridad.
> * Configurar la protección automática de las bases de datos.

## <a name="prerequisites"></a>Requisitos previos

Para poder realizar copias de seguridad de la base de datos de SQL Server, primero debe comprobar si reúne los siguientes criterios:

1. Identifique o cree un [almacén de Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) en la misma región o configuración regional que la máquina virtual que hospeda la instancia de SQL Server.
2. Compruebe que la máquina virtual tenga [conectividad de red](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Asegúrese de que las bases de datos de SQL Server siguen las [directrices de nomenclatura de bases de datos para Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Compruebe que no dispone de otras soluciones de copia de seguridad habilitadas para la base de datos. Deshabilite todas las demás copias de seguridad de SQL Server antes de hacer una copia de seguridad de la base de datos.

> [!NOTE]
> Puede habilitar Azure Backup para una máquina virtual de Azure y también para una base de datos de SQL Server que se ejecute en la máquina virtual sin que se produzcan conflictos.

### <a name="establish-network-connectivity"></a>Establecimiento de conectividad de red

Para todas las operaciones, la VM con SQL Server necesita conectividad a las direcciones IP públicas de Azure. Las operaciones de máquinas virtuales (detección de bases de datos, configuración de copias de seguridad o restauración de puntos de recuperación, entre otras) producirán errores en las direcciones IP públicas de Azure.

Establezca la conectividad con una de las siguientes opciones:

* **Allow the Azure datacenter IP ranges** (Permitir los intervalos IP del centro de datos de Azure). Esta opción permite los [intervalos IP](https://www.microsoft.com/download/details.aspx?id=41653) en la descarga. Para acceder a un grupo de seguridad de red (NSG), use el cmdlet Set-AzureNetworkSecurityRule. Si está en la lista de destinatarios seguros de las direcciones IP específicas de la región, también tendrá que actualizar la lista de destinatarios seguros con la etiqueta de servicio de Azure Active Directory (Azure AD) para habilitar la autenticación.

* **Allow access using NSG tags** (Permitir el acceso mediante etiquetas NSG).  Si usa NSG para restringir la conectividad, debe usar la etiqueta de servicio AzureBackup para permitir el acceso saliente a Azure Backup. Además, debe permitir la conectividad para la autenticación y la transferencia de datos mediante [reglas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de Azure AD y Azure Storage. Esto se puede hacer desde el portal o desde PowerShell.

    Para crear una regla mediante el portal:

  * En **Todos los servicios**, vaya a **Grupos de seguridad de red** y seleccione el grupo de seguridad de red.
  * En **Configuración**, seleccione **Reglas de seguridad de salida**.
  * Seleccione **Agregar**. Escriba todos los detalles necesarios para crear una nueva regla, como se explica en [Configuración de reglas de seguridad](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Asegúrese de que la opción **Destino** esté establecida en **Etiqueta de servicio** y de que **Etiqueta de servicio de destino** esté establecida en **AzureBackup**.
  * Haga clic en **Agregar** para guardar la regla de seguridad de salida recién creada.

   Para crear una regla mediante PowerShell:

  * Agregue las credenciales de la cuenta de Azure y actualice las nubes nacionales.<br/>
    ``Add-AzureRmAccount``
  * Seleccione la suscripción a NSG.<br/>
    ``Select-AzureRmSubscription "<Subscription Id>"``
  * Seleccione el NSG.<br/>
    ```$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"```
  * Agregue el permiso de la regla de salida para la etiqueta de servicio de Azure Backup.<br/>
   ```Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"```
  * Guarde el NSG.<br/>
    ```Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg```

* **Allow access by using Azure Firewall tags** (Permitir el acceso mediante el uso de etiquetas de Azure Firewall). Si usa Azure Firewall, cree una regla de aplicación mediante la [etiqueta de nombre de dominio completo](https://docs.microsoft.com/azure/firewall/fqdn-tags) AzureBackup. Esto permite el acceso de salida a Azure Backup.
* **Deploy an HTTP proxy server to route traffic** (Implementar un servidor proxy HTTP para enrutar el tráfico). Cuando hace copia de seguridad de una base de datos de SQL Server en una máquina virtual de Azure, la extensión de copia de seguridad en la máquina virtual usa las API HTTPS para enviar comandos de administración a Azure Backup y datos a Azure Storage. La extensión de copia de seguridad también usa Azure AD para la autenticación. Enrute el tráfico de extensión de copia de seguridad de estos tres servicios a través del proxy HTTP. Las extensiones son el único componente configurado para el acceso a la red pública de Internet.

Las opciones de conectividad incluyen las siguientes ventajas y desventajas:

**Opción** | **Ventajas** | **Desventajas**
--- | --- | ---
Permitir intervalos IP | Sin costos adicionales. | Este escenario es complejo de administrar, ya que los intervalos de direcciones IP cambian con el tiempo. <br/><br/> Proporciona acceso a la totalidad de Azure, no solo a Azure Storage.
Uso de las etiquetas de servicio de NSG | Más fácil de administrar ya que los cambios de intervalo se combinan automáticamente <br/><br/> Sin costos adicionales. <br/><br/> | Solo se puede usar con grupos de seguridad de red. <br/><br/> Proporciona acceso a todo el servicio.
Uso de las etiquetas de nombre de dominio completo de Azure Firewall | Más fácil de administrar ya que los nombres de dominio completo se administran automáticamente. | Solo se puede usar con Azure Firewall.
Usar un servidor proxy HTTP | Se permite un control detallado en el proxy sobre las direcciones URL de almacenamiento. <br/><br/> Un único punto de acceso a Internet para las máquinas virtuales. <br/><br/> No están sujetas a cambios de direcciones IP de Azure. | Costos adicionales de ejecutar una máquina virtual con el software de proxy.

### <a name="database-naming-guidelines-for-azure-backup"></a>Instrucciones de nomenclatura de la base de datos para Azure Backup

Evite el uso de los elementos siguientes en los nombres de las bases de datos:

* Espacios iniciales o finales
* Signos de exclamación (!)
* Corchetes de cierre (])
* Punto y coma ';'
* Barra diagonal '/'

El establecimiento de alias está permitido para los caracteres no admitidos, pero se recomienda evitarlos. Para obtener más información, consulte [Descripción del modelo de datos del servicio Tabla](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Detección de bases de datos SQL Server

Detección de las bases de datos que se ejecutan en la máquina virtual:

1. En [Azure Portal](https://portal.azure.com), abra el almacén de Recovery Services que usa para la copia de seguridad de la base de datos.

2. En el panel **Almacén de Recovery Services**, seleccione **Copia de seguridad**.

   ![Seleccionar Copia de seguridad para abrir el menú Objetivo de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. En **Objetivo de Backup**, establezca **¿Dónde se ejecuta su carga de trabajo?** en **Azure**.

4. En **What do you want to backup** (¿De qué quiere realizar una copia de seguridad?), seleccione **SQL Server in Azure VM** (SQL Server en la máquina virtual de Azure).

    ![Seleccionar SQL Server en una máquina virtual de Azure para la copia de seguridad](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. En **Backup Goal** > **Discover DBs in VMs** (Objetivo de Backup > Detectar bases de datos en máquinas virtuales), seleccione **Start Discovery** (Iniciar detección) para buscar las máquinas virtuales sin protección en la suscripción. Puede que la búsqueda tarde un rato, según el número de máquinas virtuales sin protección en la suscripción.

   * Tras la detección, las máquinas virtuales no protegidas deben aparecer en la lista por nombre y grupo de recursos.
   * Si una máquina virtual no aparece en la lista como cabría esperar, compruebe que no se haya copiado ya en un almacén.
   * Varias máquinas virtuales pueden tener el mismo nombre pero pertenecer a distintos grupos de recursos.

     ![La copia de seguridad queda pendiente durante la búsqueda de bases de datos en máquinas virtuales](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. En la lista de máquinas virtuales, seleccione la que ejecuta la base de datos de SQL Server > **Discover DBs** (Detectar bases de datos).

7. Realice el seguimiento de la detección de las bases de datos en **Notificaciones**. El tiempo necesario para esta acción depende del número de bases de datos de máquina virtual. Cuando se detectan las bases de datos seleccionadas, aparece un mensaje de operación correcta.

    ![Mensaje de implementación correcta](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecta todas las bases de datos de SQL Server en la máquina virtual. Durante la detección, los siguientes elementos tienen lugar en segundo plano:

    * Azure Backup registra la máquina virtual en el almacén para la copia de seguridad de la carga de trabajo. Todas las bases de datos de la máquina virtual registrada solo se pueden copiar en este almacén.
    * Azure Backup instala la extensión AzureBackupWindowsWorkload en la máquina virtual. No se instala ningún agente en la base de datos SQL.
    * Azure Backup crea la cuenta de servicio NT Service\AzureWLBackupPluginSvc en la máquina virtual.
      * Todas las operaciones de copia de seguridad y restauración utilizan la cuenta de servicio.
      * NT Service\AzureWLBackupPluginSvc necesita permisos de administrador del sistema de SQL. Todas las máquinas virtuales de SQL Server creadas en Marketplace llevan instalado SqlIaaSExtension. La extensión AzureBackupWindowsWorkload usa SQLIaaSExtension para obtener automáticamente los permisos necesarios.
    * Si no ha creado la máquina virtual desde Marketplace o si tiene SQL 2008 y 2008 R2, es posible que la máquina virtual no tenga instalado SqlIaaSExtension y que la operación de detección produzca el mensaje de error UserErrorSQLNoSysAdminMembership. Para solucionar este problema, siga las instrucciones de [Establecer permisos de máquina virtual](backup-azure-sql-database.md#set-vm-permissions).

        ![Seleccionar la máquina virtual y la base de datos](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configuración de la copia de seguridad  

1. En **Objetivo de Backup** > **Paso 2: Configurar copia de seguridad**, seleccione **Configurar copia de seguridad**.

   ![Seleccionar Configurar copia de seguridad](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. En **Seleccionar los elementos de los que se debe realizar una copia de seguridad**, verá todos los grupos de disponibilidad registrados y las instancias de SQL Server independientes. Seleccione la flecha situada a la izquierda de una fila para expandir la lista de todas las bases de datos no protegidas en esa instancia o el grupo de disponibilidad Always On.  

    ![Mostrar todas las instancias de SQL Server con bases de datos independientes](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Elija todas las bases de datos que quiere proteger y, después, seleccione **Aceptar**.

   ![Protección de la base de datos](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para optimizar las cargas de copia de seguridad, el número máximo de bases de datos en un trabajo de copia de seguridad en Azure Backup se establece en 50.

     * Para proteger más de 50 bases de datos, configure varias copias de seguridad.
     * Para [habilitar](#enable-auto-protection) toda la instancia o el grupo de disponibilidad Always On, en la lista desplegable **PROTECCIÓN AUTOMÁTICA**, seleccione **ACTIVADA** y luego **ACEPTAR**.

    > [!NOTE]
    > La característica de [protección automática](#enable-auto-protection) no solo habilita la protección en todas las bases de datos existentes a la vez, sino que también protege automáticamente las nuevas bases de datos que se agregan a esa instancia o al grupo de disponibilidad.  

4. Seleccione **Aceptar** para abrir **Directiva de copia de seguridad**.

    ![Habilitación de la protección automática para el grupo de disponibilidad Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. En **Directiva de copia de seguridad**, elija una directiva y seleccione **Aceptar**.

   * Seleccione la directiva predeterminada como HourlyLogBackup.
   * Elegir una directiva de copia de seguridad existente creada previamente para SQL.
   * Defina una nueva directiva basada en el objetivo de punto de recuperación (RPO) y en la duración de retención.

     ![Seleccionar directiva de copia de seguridad](./media/backup-azure-sql-database/select-backup-policy.png)

6. En **Copia de seguridad**, seleccione **Habilitar copia de seguridad** .

    ![Habilitar la directiva de copia de seguridad elegida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Realice el seguimiento del progreso de la configuración en el área de **notificaciones** del portal.

    ![Área de notificaciones](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad

Una directiva de copia de seguridad define cuándo se realizan las copias de seguridad y cuánto tiempo se conservan.

* Una directiva se crea en el nivel de almacén.
* Varios almacenes pueden usar la misma directiva de copia de seguridad, pero debe aplicar la directiva de copia de seguridad a cada almacén.
* Al crear una directiva de copia de seguridad, la copia de seguridad completa diaria es la predeterminada.
* Puede agregar una copia de seguridad diferencial, pero solo si configura las copias de seguridad completas para que se realicen semanalmente.
* Más información sobre los [diferentes tipos de directivas de copia de seguridad](backup-architecture.md#sql-server-backup-types).

Para crear una directiva de copia de seguridad:

1. En el almacén, seleccione **Directivas de copia de seguridad** > **Agregar**.
2. En **Agregar**, seleccione **SQL Server en una máquina virtual de Azure** para definir el tipo de directiva.

   ![Elección de un tipo de directiva para la nueva directiva de copia de seguridad](./media/backup-azure-sql-database/policy-type-details.png)

3. En **Nombre de la directiva**, escriba un nombre para la nueva directiva.
4. En el menú **Full Backup policy** (Directiva de copia de seguridad completa), seleccione un valor para **Frecuencia de copia de seguridad**. Elija **Diario** o **Semanal**.

   * En **Diariamente**, seleccione la hora y zona horaria en la que comienza el trabajo de copia de seguridad.
   * Para **Semanalmente**, seleccione el día de la semana, la hora y la zona horaria, en la que se inicia el trabajo de copia de seguridad.
   * Ejecute una copia de seguridad completa, porque no se puede desactivar la opción **Copia de seguridad completa**.
   * Seleccione **Copia de seguridad completa** para ver la directiva.
   * No puede crear copias de seguridad diferenciales para copias de seguridad completas diarias.

     ![Nuevos campos de directiva de copia de seguridad](./media/backup-azure-sql-database/full-backup-policy.png)  

5. De forma predeterminada, todas las opciones de **DURACIÓN DE RETENCIÓN** están seleccionadas. Borre cualquier límite de la duración de retención que no desee y, a continuación, establezca los intervalos que se van a usar.

    * El período de retención mínimo para cualquier tipo de copia de seguridad (completa, diferencial o de registro) es de siete días.
    * Los puntos de recuperación se etiquetan para la retención en función de su duración de retención. Por ejemplo, si selecciona la frecuencia diaria, solo se desencadena una copia de seguridad completa cada día.
    * La copia de seguridad de un día específico se etiqueta y se retiene según la duración de la retención semanal y la configuración de esta.
    * La duración de retención mensual y anual se comporta de forma similar.

       ![Configuración del intervalo de la duración de retención](./media/backup-azure-sql-database/retention-range-interval.png)

6. En el menú de la **directiva de copia de seguridad completa**, seleccione **Aceptar** para aceptar la configuración.
7. Para agregar una directiva de copia de seguridad diferencial, seleccione **Copia de seguridad diferencial**.

   ![Configuración del intervalo de la duración de retención](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Apertura del menú de directiva de copia de seguridad diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. En **Differential Backup policy** (Directiva de copia de seguridad diferencial), seleccione **Enable** (Habilitar) para abrir los controles de retención y frecuencia.

    * Puede desencadenar una copia de seguridad diferencial al día.
    * Como máximo, las copias de seguridad diferenciales se pueden retener durante 180 días. Para un mayor tiempo de retención, use copias de seguridad completas.

9. Seleccione **Aceptar** para guardar la directiva y volver al menú principal de la **directiva de copia de seguridad**.

10. Para agregar una directiva de copia de seguridad del registro de transacciones, seleccione **Copia de seguridad de registros**.
11. En el menú **Log Backup** (Copia de seguridad de registros), seleccione **Enable** (Habilitar) y, luego, establezca los controles de retención y frecuencia. Las copia de seguridad de registros pueden realizarse cada 15 minutos y se pueden retener durante un período máximo de 35 días.
12. Seleccione **Aceptar** para guardar la directiva y volver al menú principal de la **directiva de copia de seguridad**.

    ![Editar la directiva de copia de seguridad de registros](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. En el menú **Directiva de copia de seguridad**, elija si desea habilitar la **compresión de copia de seguridad de SQL**.
    * La compresión está deshabilitada de manera predeterminada.
    * En el back-end, Azure Backup usa la compresión de copia de seguridad nativa de SQL.

14. Después de completar las modificaciones en la directiva de copia de seguridad, seleccione **Aceptar**.

## <a name="enable-auto-protection"></a>Habilitación de la protección automática  

Puede habilitar la protección automática para hacer una copia de seguridad automática de todas las bases de datos existentes y futuras en una instancia de SQL Server independiente o en un grupo de disponibilidad de AlwaysOn.

* No hay límite en el número de bases de datos que se pueden seleccionar para la protección automática en una sola operación.
* No puede proteger o excluir selectivamente las bases de datos de la protección en una instancia en el momento en que habilita la protección automática.
* Si la instancia ya incluye algunas bases de datos protegidos, permanecerán protegidos en sus respectivas directivas incluso después de activar la protección automática. Todas las bases de datos no protegidas que se agreguen posteriormente tendrán una única directiva que se define en el momento de habilitar la protección automática, indicada en **Configurar copia de seguridad**. Sin embargo, puede cambiar la directiva asociada a una base de datos protegida automáticamente más adelante.  

Para habilitar la protección automática:

  1. En **Items to backup** (Elementos para copia de seguridad), seleccione la instancia para la que quiere habilitar la protección automática.
  2. Seleccione la lista desplegable en **AUTOPROTECT** (Protección automática), elija **ACTIVAR** y, a continuación, seleccione **Aceptar**.

      ![Habilitación de la protección automática en el grupo de disponibilidad](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. La copia de seguridad se configura para todas las bases de datos juntas y se puede realizar un seguimiento de ella en **Backup Jobs** (Trabajos de copia de seguridad).

Si tiene que deshabilitar la protección automática, haga clic en el nombre de la instancia en **Configurar copia de seguridad** y seleccione **Deshabilitar la protección automática**. Se seguirán haciendo copias de seguridad de todas las bases de datos, pero las bases de datos futuras no estarán protegidas automáticamente.

![Deshabilitación de la protección automática en dicha instancia](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo:

* [Restauración de bases de datos de SQL Server con copia de seguridad](restore-sql-database-azure-vm.md)
* [Administración de bases de datos de SQL Server con copia de seguridad](manage-monitor-sql-database-backup.md)
