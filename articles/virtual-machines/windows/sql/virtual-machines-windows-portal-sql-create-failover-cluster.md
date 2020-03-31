---
title: 'FCI de SQL Server: Azure Virtual Machines | Microsoft Docs'
description: En este artículo se explica cómo crear una instancia de clúster de conmutación por error de SQL Server en Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "79226648"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configuración de una instancia de clúster de conmutación por error de SQL Server en Azure Virtual Machines

En este artículo se explica cómo crear una instancia de clúster de conmutación por error (FCI) de SQL Server en Azure Virtual Machines con el modelo de Azure Resource Manager. Esta solución usa [Espacios de almacenamiento directo de Windows Server 2016 Datacenter Edition ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como una SAN virtual de software que sincroniza el almacenamiento (discos de datos) entre los nodos (Azure Virtual Machines) de Windows Cluster. Espacios de almacenamiento directo era nuevo en Windows Server 2016.

El siguiente diagrama muestra la solución completa en máquinas virtuales de Azure:

![La solución completa](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

En este diagrama se muestra:

- Dos máquinas virtuales de Azure en un clúster de conmutación por error de Windows Server. Cuando una máquina virtual está en un clúster de conmutación por error también se denomina un *nodo de clúster* o *nodos*.
- Cada máquina virtual tiene dos, o más, discos de datos.
- Espacios de almacenamiento directo sincroniza los datos del disco de datos y presenta el almacenamiento sincronizado como grupo de almacenamiento.
- El grupo de almacenamiento presenta un volumen compartido de clúster (CSV) al clúster de conmutación por error.
- El rol de clúster de FCI de SQL Server usa el CSV para las unidades de datos.
- Un equilibrador de carga de Azure que almacene la dirección IP para la FCI de SQL Server.
- Un conjunto de disponibilidad de Azure contiene todos los recursos.

>[!NOTE]
>Todos los recursos de Azure en el diagrama se encuentran en el mismo grupo de recursos.

Para más información acerca de Espacios de almacenamiento directo, consulte [Espacios de almacenamiento directo de Windows Server 2016 Datacenter Edition](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Espacios de almacenamiento directo admite dos tipos de arquitecturas: convergidas e hiperconvergidas. La arquitectura de este documento es hiperconvergida. Las infraestructuras hiperconvergidas colocan el almacenamiento en los mismos servidores que hospedan la aplicación en clúster. En esta arquitectura, el almacenamiento se realiza en cada nodo de FCI de SQL Server.

## <a name="licensing-and-pricing"></a>Licencias y precios

En Azure Virtual Machines, puede obtener licencias de SQL Server mediante imágenes de VM de pago por uso (PAYG) o traiga su propia licencia (BYOL). El tipo de imagen que elija afecta a cómo se le cobra.

Con las licencias de pago por uso, una instancia de clúster de conmutación por error (FCI) de SQL Server en Azure Virtual Machines incurre en cargos para todos los nodos de FCI, incluidos los nodos pasivos. Para obtener más información, consulte [Precios de máquinas virtuales SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Si cuenta con un Contrato Enterprise con Software Assurance puede utilizar un nodo FCI pasivo gratuito por cada nodo activo. Para aprovechar esta ventaja en Azure, use imágenes de VM BYOL y, use la misma licencia en los nodos activos y pasivos de FCI. Para obtener más información, consulte [Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar las licencias de pago por uso y BYOL para SQL Server en Azure Virtual Machines, consulte [Introducción a máquinas virtuales con SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obtener información completa acerca de las licencias de SQL Server, consulte los [precios](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Plantilla de Azure de ejemplo

Puede crear toda la solución en Azure a partir de una plantilla. Un ejemplo de una plantilla está disponible en las [plantillas de inicio rápido de Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) de GitHub. Este ejemplo no se ha diseñado para ninguna carga de trabajo específica ni se ha probado para ella. Puede ejecutar la plantilla para crear una FCI de SQL Server con almacenamiento de Espacios de almacenamiento directo conectado a su dominio. Puede evaluar la plantilla y modificarla para adecuarla a sus fines.

## <a name="before-you-begin"></a>Antes de empezar

Hay algunas cosas que debe conocer y tener en cuenta antes de empezar.

### <a name="what-to-know"></a>Lo que necesita saber
Debe estar familiarizado con el funcionamiento de estas tecnologías:

- [Tecnologías de clúster de Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instancias del clúster de conmutación por error de SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Hay que tener en cuenta, en un clúster de conmutación por error invitado de VM de IaaS de Azure, se recomienda una sola NIC por servidor (nodo de clúster) y una sola subred. La red de Azure tiene redundancia física, que hace que las NIC y subredes adicionales sean innecesarias en un clúster invitado de VM de IaaS de Azure. El informe de validación de clúster le avisará de que solo se puede tener acceso a los nodos en una sola red. Puede omitir esta advertencia en los clústeres de conmutación por error invitados de máquinas virtuales de IaaS de Azure.

También debe tener conocimientos generales de estas tecnologías:

- [Solución hiperconvergida con Espacios de almacenamiento directo en Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupos de recursos de Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Actualmente, las instancias del clúster de conmutación por error de SQL Server de las máquinas virtuales de Azure solo se admiten con el [modo de administración ligera](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) de la [extensión del agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para cambiar del modo de extensión total a extensión ligera, elimine el recurso **Máquina virtual SQL** de las máquinas virtuales correspondientes y regístrelas con el proveedor de recursos de VM con SQL en el modo ligero. Al eliminar el recurso **Máquina virtual SQL** desde Azure Portal, **desactive la casilla de la máquina virtual correcta**. La extensión completa admite características como la copia de seguridad automatizada, y la aplicación de revisiones y la administración avanzada del portal. Estas características no funcionarán para las máquinas virtuales de SQL si se vuelve a instalar el agente en modo de administración lightweight.

### <a name="what-to-have"></a>Lo que debe tener

Antes de completar los pasos de este artículo, ya debe tener:

- Una suscripción de Microsoft Azure.
- Un dominio de Windows en máquinas virtuales de Azure.
- Una cuenta que tenga permisos para crear objetos en máquinas virtuales de Azure y en Active Directory.
- Una red virtual de Azure y una subred con espacio de direcciones IP suficiente para estos componentes:
   - Ambas máquinas virtuales.
   - La dirección IP del clúster de conmutación por error.
   - Una dirección IP para cada FCI.
- DNS configurado en la red de Azure que señala a los controladores de dominio.

Una vez que cumpla los requisitos previos, puede comenzar la creación de un clúster de conmutación por error. El primer paso es crear las máquinas virtuales.

## <a name="step-1-create-the-virtual-machines"></a>Paso 1: Creación de las máquinas virtuales

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su suscripción.

1. [Cree un conjunto de disponibilidad de Azure](../tutorial-availability-sets.md).

   El conjunto de disponibilidad agrupa las máquinas virtuales en dominios de error y dominios de actualización. Sirve para garantizar que la aplicación no resulta afectada por puntos únicos de error, como el conmutador de red o la fuente de alimentación de un conjunto de servidores.

   Si no ha creado el grupo de recursos para las máquinas virtuales, hágalo al crear un conjunto de disponibilidad de Azure. Si va a usar Azure Portal para crear el conjunto de disponibilidad, siga estos pasos:

   1. En Azure Portal, seleccione **Crear un recurso** para abrir Azure Marketplace. Busque **Conjunto de disponibilidad**.
   1. Seleccione **Conjunto de disponibilidad**.
   1. Seleccione **Crear**.
   1. En **Crear conjunto de disponibilidad**, proporcione estos valores:
      - **Name**: nombre del conjunto de disponibilidad.
      - **Suscripción**: Su suscripción de Azure.
      - **Grupo de recursos**: si quiere utilizar un grupo existente, haga clic en **Seleccionar existente** y, luego, seleccione el grupo de la lista. De lo contrario, seleccione **Crear nuevo** y escriba el nombre del grupo.
      - **Ubicación**: establezca la ubicación en la que planea crear las máquinas virtuales.
      - **Dominios de error**: use el valor predeterminado (**3**).
      - **Dominios de actualización**: use el valor predeterminado (**5**).
   1. Seleccione **Crear** para crear el conjunto de disponibilidad.

1. Cree las máquinas virtuales en el conjunto de disponibilidad.

   Aprovisionar dos máquinas virtuales de SQL Server en el conjunto de disponibilidad de Azure. Para conocer las instrucciones, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque ambas máquinas virtuales:

   - En el mismo grupo de recursos de Azure en el que se encuentra su conjunto de disponibilidad.
   - En la misma red que el controlador de dominio.
   - En una subred que tenga suficiente espacio de direcciones IP para ambas máquinas virtuales y todas las FCI que finalmente puede utilizar en este clúster.
   - En el conjunto de disponibilidad de Azure.

      >[!IMPORTANT]
      >Una vez creada una máquina virtual el conjunto de disponibilidad no se puede establecer o cambiar.

   Elija una imagen en Azure Marketplace. Puede usar una imagen de Azure Marketplace que incluya Windows Server y SQL Server, o use una que solo incluya Windows Server. Para más información, consulte [Información general de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

   Las imágenes oficiales de SQL Server de la galería de Azure incluyen una instancia de SQL Server instalada, el software de instalación de SQL Server y la clave necesaria.

   Elija la imagen correcta en función de cómo desea pagar la licencia de SQL Server:

   - **Licencias de pago por uso**. el costo por segundo de estas imágenes incluye la licencia de SQL Server:
      - **SQL Server 2016 Enterprise en Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard en Windows Server 2016 Datacenter**
      - **SQL Server 2016 Developer en Windows Server 2016 Datacenter**

   - **Traiga su propia licencia (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise en Windows Server 2016 Datacenter**
      - **(BYOL) SQL Server 2016 Standard en Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Después de crear la máquina virtual, quite la instancia de SQL Server independiente preinstalada. Tras configurar el clúster de conmutación por error y Espacios de almacenamiento directo, tendrá que usar los medios de SQL Server preinstalado para crear la FCI de SQL Server.

   Como alternativa, puede utilizar imágenes de Azure Marketplace que solo tienen el sistema operativo. Elija una imagen de **Windows Server 2016 Datacenter** e instale la FCI de SQL Server después de configurar el clúster de conmutación por error y Espacios de almacenamiento directo. Esta imagen no contiene los medios de instalación de SQL Server. Coloque los medios de instalación de SQL Server en una ubicación en la que pueda ejecutarlos para cada servidor.

1. Una vez que Azure cree las máquinas virtuales, conéctese a cada una de ellas con RDP.

   La primera vez que se conecte a una máquina virtual con RDP, un mensaje le pregunta si desea permitir que el equipo se pueda detectar en la red. Seleccione **Sí**.

1. Si utiliza una de las imágenes de máquina virtual basada en SQL Server, quite la instancia de SQL Server.

   1. En **Programas y características**, haga clic con el botón derecho en **Microsoft SQL Server 2016 (64 bits)** y seleccione **Desinstalar o cambiar**.
   1. Seleccione **Quitar**.
   1. Seleccione la instancia predeterminada.
   1. Quite todas las características en **Servicios de motor de base de datos**. No quite **Características compartidas**. Verá algo parecido a la siguiente captura de pantalla:

      ![Selección de características](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Seleccione **Siguiente** y después **Quitar**.

1. <a name="ports"></a>Abra los puertos del firewall.

   En cada máquina virtual, abra estos puertos de Firewall de Windows:

   | Propósito | Puerto TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Puerto normal para las instancias predeterminadas de SQL Server. Si usó una imagen de la galería, este puerto se abre automáticamente.
   | Sondeo de mantenimiento | 59999 | Cualquier puerto TCP abierto. En un paso posterior, configure el [sondeo de mantenimiento](#probe) del equilibrador de carga y el clúster para usar este puerto.  

1. Agregue almacenamiento a la máquina virtual. Para más información, consulte [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../disks-types.md).

   Ambas máquinas virtuales necesitan al menos dos discos de datos.

   Conecte discos sin procesar, discos cuyo formato no es NTFS.
      >[!NOTE]
      >Si conecta discos con formato NTFS, solo puede habilitar Espacios de almacenamiento directo sin comprobación de la idoneidad del disco.  

   Conecte un mínimo de dos discos SSD Premium a cada máquina virtual. Se recomienda usar, como mínimo, discos P30 (1 TB).

   Establezca el almacenamiento en caché de host como **de solo lectura**.

   La capacidad de almacenamiento que se utiliza en los entornos de producción depende de la carga de trabajo. Los valores que se describen en este artículo son para demostración y pruebas.

1. [Agregue las máquinas virtuales a un dominio preexistente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Después de crear y configurar las máquinas virtuales, puede configurar el clúster de conmutación por error.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Paso 2: Configurar el clúster de conmutación por error de Windows Server con Espacios de almacenamiento directo

El siguiente paso es configurar el clúster de conmutación por error con Espacios de almacenamiento directo. En este paso va a completar estos subpasos:

1. Adición de la característica de clústeres de conmutación por error de Windows Server.
1. Validación del clúster.
1. Creación del clúster de conmutación por error.
1. Creación del testigo en la nube.
1. Adición de almacenamiento.

### <a name="add-windows-server-failover-clustering"></a>Agregar Clústeres de conmutación por error de Windows Server

1. Conéctese a la primera máquina virtual con RDP mediante una cuenta de dominio que sea miembro de los administradores locales y tenga permisos para crear objetos en Active Directory. Utilice esta cuenta para el resto de la configuración.

1. [Agregue Clústeres de conmutación por error a todas las máquinas virtuales](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar la característica Clústeres de conmutación por error desde la interfaz de usuario, realice estos pasos en las dos máquinas virtuales:
   1. En el **Administrador del servidor**, seleccione **Administrar** y, a continuación, seleccione **Agregar roles y características**.
   1. En el **Asistente para agregar roles y características**, seleccione **Siguiente** hasta llegar a **Seleccionar características**.
   1. En **Seleccionar características**, seleccione **Clúster de conmutación por error**. Incluya todas las características y herramientas de administración requeridas. Seleccione **Agregar características**.
   1. Seleccione **Siguiente** y, después, **Finalizar** para instalar las características.

   Para instalar Clústeres de conmutación por error con PowerShell, ejecute el siguiente script en una sesión de PowerShell de administrador de una de las máquinas virtuales:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para obtener más información sobre los siguientes pasos, consulte las instrucciones del paso 3 de [Solución hiperconvergida con Espacios de almacenamiento directo en Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validación del clúster

Valide el clúster en la interfaz de usuario o con PowerShell.

Para validar el clúster con la interfaz de usuario, realice los pasos siguientes en una de las máquinas virtuales:

1. En **Administrador del servidor**, seleccione **Herramientas** y, después, seleccione **Administrador de clústeres de conmutación por error**.
1. En **Administrador de clústeres de conmutación por error**, seleccione **Acción**y, a continuación, seleccione **Validar configuración**.
1. Seleccione **Next** (Siguiente).
1. En **Seleccionar servidores o un clúster**, escriba el nombre de ambas máquinas virtuales.
1. En **Opciones de pruebas**, seleccione **Ejecutar solo las pruebas que seleccione**. Seleccione **Next** (Siguiente).
1. En **Selección de pruebas**, seleccione todas las pruebas excepto **Almacenamiento**, como se muestra aquí:

   ![Seleccionar pruebas de validación de clústeres](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Seleccione **Next** (Siguiente).
1. En **Confirmación**, seleccione **Siguiente**.

El Asistente para validar una configuración ejecuta las pruebas de validación.

Para validar el clúster con PowerShell, ejecute el siguiente script en una sesión de PowerShell de administrador de una de las máquinas virtuales:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Después de validar el clúster, cree el clúster de conmutación por error.

### <a name="create-the-failover-cluster"></a>Creación del clúster de conmutación por error

Para crear el clúster de conmutación por error, necesita:
- Los nombres de las máquinas virtuales que se convertirán en nodos del clúster.
- Un nombre para el clúster de conmutación por error
- Una dirección IP para el clúster de conmutación por error. Puede usar una dirección IP que no se utilice en la misma red virtual de Azure y subred como nodos del clúster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 a Windows Server 2016

El siguiente script de PowerShell crea un clúster de conmutación por error para Windows Server 2008 a través de Windows Server 2016. Actualice el script con los nombres de los nodos (los nombres de las máquinas virtuales) y una dirección IP disponible desde la red virtual de Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

El siguiente script de PowerShell crea un clúster de conmutación por error para Windows Server 2019. Para más información, consulte [Clúster de conmutación por error: objeto de red en clúster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Actualice el script con los nombres de los nodos (los nombres de las máquinas virtuales) y una dirección IP disponible desde la red virtual de Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Creación de un testigo en la nube

Testigo en la nube es un nuevo tipo de testigo de quórum de clúster que está almacenado en una instancia de Azure Storage Blob. Esto elimina la necesidad de que una máquina virtual independiente hospede un recurso compartido testigo.

1. [Cree un testigo en la nube para el clúster de conmutación por error](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Cree un contenedor de blobs.

1. Guarde las claves de acceso y la dirección URL del contenedor.

1. Configure el testigo de cuórum de clúster de conmutación por error. Consulte [Configurar el testigo de cuórum en la interfaz de usuario](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Agregue almacenamiento

Los discos Premium para Espacios de almacenamiento directo deben estar vacíos. No pueden contener particiones u otros datos. Para limpiar los discos, siga [los pasos de esta guía](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Habilite Espacios de almacenamiento directo ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   El siguiente script de PowerShell habilita los Espacios de almacenamiento directo:  

   ```powershell
   Enable-ClusterS2D
   ```

   En **Administrador de clústeres de conmutación por error**, puede ver el grupo de almacenamiento.

1. [Cree un volumen](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Espacios de almacenamiento directo crea automáticamente un grupo de almacenamiento cuando se habilita. Ya está listo para crear un volumen. El cmdlet de PowerShell `New-Volume` automatiza el proceso de creación de volúmenes. Este proceso incluye el formato, la adición del volumen al clúster y la creación de un Volumen compartido de clúster (CSV). En el ejemplo se crea un CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Una vez que se completa este comando, se monta un volumen de 800 GB como recurso del clúster. El volumen está en `C:\ClusterStorage\Volume1\`.

   En esta captura de pantalla se muestra un Volumen compartido de clúster con Espacios de almacenamiento directo:

   ![Volumen compartido de clúster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Paso 3: Probar la conmutación por error del clúster de conmutación por error

En **Administrador de clústeres de conmutación por error**, compruebe que puede mover el recurso de almacenamiento al otro nodo del clúster. Si se puede conectar al clúster de conmutación por error con **Administrador de clústeres de conmutación por error** y mover el almacenamiento de un nodo al otro, está listo para configurar la FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Paso 4: Crear la FCI de SQL Server

Después de haber configurado el clúster de conmutación por error y todos los componentes del clúster, incluido el almacenamiento, puede crear la FCI de SQL Server.

1. Conéctese a la primera máquina virtual con RDP.

1. En **Administrador de clústeres de conmutación por error**, asegúrese de que todos los recursos principales de clúster estén en la primera máquina virtual. Si es necesario, mueva todos los recursos a esa máquina virtual.

1. Localice los medios de instalación. Si la máquina virtual usa una de las imágenes de Azure Marketplace, los medios se encuentran en `C:\SQLServer_<version number>_Full`. Seleccione **Setup** (Configuración).

1. En **Centro de instalación de SQL Server**, seleccione **Instalación**.

1. Seleccione **Nueva instalación de clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar la FCI de SQL Server.

   Es preciso que los directorios de datos de FCI estén en el almacenamiento en clúster. Con Espacios de almacenamiento directo, no es un disco compartido, sino un punto de montaje a un volumen en cada servidor. Espacios de almacenamiento directo sincroniza el volumen entre ambos nodos. El volumen se presenta al clúster como un Volumen compartido de clúster. Utilice el punto de montaje de CSV para los directorios de datos.

   ![Directorios de datos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Después de completar las instrucciones en el asistente, el programa de instalación instalará una FCI de SQL Server en el primer nodo.

1. Una vez que el programa de instalación instale la FCI en el primer nodo, conéctese al segundo nodo con RDP.

1. Abra **Centro de instalación de SQL Server**. Seleccione **Instalación**.

1. Seleccione **Agregar nodo a clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar el servidor de SQL Server y agregarlo a la FCI.

   >[!NOTE]
   >Si usó una imagen de la galería de Azure Marketplace con SQL Server, las herramientas de SQL Server estaban incluidas en la imagen. Si no usó alguna de estas imágenes, instale las herramientas de SQL Server por separado. Consulte [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Paso 5: Creación de una instancia de Azure Load Balancer

En máquinas virtuales de Azure, los clústeres usan un equilibrador de carga para mantener una dirección IP que es preciso que esté en los nodos de clúster de uno en uno. En esta solución, el equilibrador de carga que retiene la dirección IP para la FCI de SQL Server.

Para más información, consulte [Creación y configuración de un equilibrador de carga de Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creación del equilibrador de carga en Azure Portal

Para crear el equilibrador de carga:

1. En Azure Portal, vaya al grupo de recursos que contiene las máquinas virtuales.

1. Seleccione **Agregar**. Busque **Equilibrador de carga** en Azure Marketplace. Seleccione **Equilibrador de carga**.

1. Seleccione **Crear**.

1. Configure el equilibrador de carga con:

   - **Suscripción**: Su suscripción de Azure.
   - **Grupo de recursos**: El grupo de recursos que contiene las máquinas virtuales.
   - **Name**: un nombre que identifica el equilibrador de carga.
   - **Región**: La ubicación de Azure que contiene las máquinas virtuales.
   - **Tipo**: Pública o privada. A los equilibradores de carga privados se puede acceder desde la red virtual. La mayoría de las aplicaciones de Azure pueden usar un equilibrador de carga privado. Si la aplicación necesita acceder a SQL Server directamente a través de Internet, utilice un equilibrador de carga público.
   - **SKU**: Estándar.
   - **Red virtual**: la misma red que la de las máquinas virtuales.
   - **Asignación de dirección IP**: Estática. 
   - **Dirección IP privada**: la dirección IP que asignó al recurso de red de clúster de la FCI de SQL Server.

 En la captura de pantalla siguiente se muestra la interfaz de usuario **Crear un equilibrador de carga**:

   ![Configuración del equilibrador de carga](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configuración del grupo backend del equilibrador de carga

1. Vuelva al grupo de recursos de Azure que contiene las máquinas virtuales y busque el equilibrador de carga nuevo. Es posible que tenga que actualizar la vista en el grupo de recursos. Seleccione el equilibrador de carga.

1. Seleccione **Grupos de back-end** y, a continuación, seleccione **Agregar**.

1. Asocie el grupo de back-end con el conjunto de disponibilidad que contiene las máquinas virtuales.

1. En **Configuraciones IP de red de destino**, active **MÁQUINA VIRTUAL** y seleccione las máquinas virtuales que participarán como nodos de clúster. No olvide incluir todas las máquinas virtuales que hospedarán el FCI.

1. Seleccione **Aceptar** para crear el grupo de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configuración de un sondeo de mantenimiento de un equilibrador de carga

1. En la hoja del equilibrador de carga, seleccione **Health probes** (Sondeos de mantenimiento).

1. Seleccione **Agregar**.

1. En la hoja **Add health probe** (Agregar sonda de mantenimiento), <a name="probe"></a>establezca los parámetros del sondeo de mantenimiento.

   - **Name**: nombre del sondeo de estado.
   - **Protocolo**: TCP.
   - **Puerto**: establezca este parámetro en el puerto que creó en el firewall para el sondeo de estado en [este paso](#ports). En este artículo, el ejemplo usa el puerto TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Umbral incorrecto**: 2 errores consecutivos.

1. Seleccione **Aceptar**.

### <a name="set-load-balancing-rules"></a>Establecimiento de reglas de equilibrio de carga

1. En la hoja del equilibrador de carga, seleccione **Reglas de equilibrio de carga**.

1. Seleccione **Agregar**.

1. Establezca los parámetros de la regla de equilibrio de carga:

   - **Name**: nombre de las reglas de equilibrio de carga.
   - **Dirección IP de front-end**: la dirección IP del recurso de red del clúster de la FCI de SQL Server.
   - **Puerto**: el puerto TCP de la FCI de SQL Server. El puerto de la instancia predeterminado es 1433.
   - **Puerto back-end**: utiliza el mismo puerto que el valor **Puerto** cuando se habilita **IP flotante (Direct Server Return)** .
   - **Grupo de back-end**: el nombre del grupo de back-end que configuró anteriormente.
   - **Sondeo de mantenimiento**: el sondeo de estado que configuró anteriormente.
   - **Persistencia de la sesión**: Ninguno.
   - **Tiempo de espera de inactividad (minutos)** : 4.
   - **IP flotante (Direct Server Return)** : Habilitado.

1. Seleccione **Aceptar**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Paso 6: Configuración del clúster para el sondeo

Establezca el parámetro del puerto de sondeo de clúster en PowerShell.

Para establecer el parámetro del puerto de sondeo de clúster, actualice las variables del siguiente script con los valores del entorno. Quite los corchetes angulares (`<` y `>`) del script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

En la lista siguiente se describen los valores que debe actualizar:

   - `<Cluster Network Name>`: el nombre del clúster de conmutación por error de Windows Server para la red. En **Administrador de clústeres de conmutación por error** > **Redes**, haga clic con el botón derecho en la red y después seleccione **Propiedades**. El valor correcto está debajo del campo **Nombre** en la pestaña **General**.

   - `<SQL Server FCI IP Address Resource Name>`: nombre de recurso de la dirección IP de FCI de SQL Server. En **Administrador de clústeres de conmutación por error** > **Roles**, en el rol FCI de SQL Server, en **Nombre del servidor**, haga clic con el botón derecho en el recurso de la dirección IP y después seleccione **Propiedades**. El valor correcto está debajo del campo **Nombre** en la pestaña **General**. 

   - `<ILBIP>`: dirección IP del ILB. Esta dirección se configura en Azure Portal como la dirección front-end de ILB. También es la dirección IP de FCI de SQL Server. Puede encontrarla en **Administrador de clústeres de conmutación por error** en la misma página de propiedades donde encuentra `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: el puerto de sondeo configurado en el sondeo de estado del equilibrador de carga. Cualquier puerto TCP no utilizado es válido.

>[!IMPORTANT]
>La máscara de subred para los parámetros del clúster debe ser la dirección de difusión TCP/IP: `255.255.255.255`.

Después de establecer el sondeo de clúster puede ver todos los parámetros del clúster en PowerShell. Ejecute este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Paso 7: Probar la conmutación por error de la FCI

Probar la conmutación por error de la FCI para validar la funcionalidad del clúster. Siga estos pasos.

1. Conéctese a uno de los nodos de clúster de la FCI de SQL Server con RDP.

1. Abra el **Administrador de clústeres de conmutación por error**. Seleccione **Roles**. Observe qué nodo posee el rol de FCI de SQL Server.

1. Haga clic con el botón derecho en el rol de FCI de SQL Server.

1. Seleccione **Mover** y, después, seleccione **Mejor nodo posible**.

El **Administrador de clústeres de conmutación por error** muestra el rol y sus recursos pierden su conexión. Después, los recursos se mueven y vuelven a conectarse en el otro nodo.

### <a name="test-connectivity"></a>Comprobación de la conectividad

Para probar la conectividad, inicie sesión en otra máquina virtual de la misma red virtual. Abra **SQL Server Management Studio** y conéctese al nombre de la FCI de SQL Server.

>[!NOTE]
>Si es necesario, puede [descargar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitaciones

Azure Virtual Machines admiten el Coordinador de transacciones distribuidas de Microsoft (MSDTC) en Windows Server 2019 con almacenamiento en volúmenes compartidos en clúster (CSV) y un [equilibrador de carga estándar](../../../load-balancer/load-balancer-standard-overview.md).

En máquinas virtuales de Azure, MSDTC no se admite en Windows Server 2016 y versiones anteriores porque:

- El recurso MSDTC en clúster no puede configurarse para usar almacenamiento compartido. En Windows Server 2016, si crea un recurso MSDTC, no mostrará ningún almacenamiento compartido disponible para su uso, incluso si el almacenamiento está disponible. Este problema se ha corregido en Windows Server 2019.
- El equilibrador de carga básico no controla los puertos RPC.

## <a name="see-also"></a>Consulte también

[Configuración de Espacios de almacenamiento directo con escritorio remoto (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solución hiperconvergida con Espacios de almacenamiento directo](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Introducción a Espacios de almacenamiento directo](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Compatibilidad de SQL Server con Espacios de almacenamiento directo](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
