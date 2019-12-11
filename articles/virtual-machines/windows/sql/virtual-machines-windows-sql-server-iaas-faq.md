---
title: Preguntas más frecuentes sobre SQL Server en Windows Virtual Machines de Azure | Microsoft Docs
description: Este artículo brinda respuestas a las preguntas más frecuentes sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: c3b4fabb319a3ea76ee62c8c699d4613184a4e76
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791052"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Preguntas más frecuentes para SQL Server en máquinas virtuales de Windows en Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

En este artículo se ofrecen respuestas a algunas de las preguntas más comunes sobre la ejecución de [SQL Server en máquinas virtuales de Windows en Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artículo se centra en los problemas específicos de SQL Server en máquinas virtuales Windows. Si ejecuta SQL Server en máquinas virtuales Linux, consulte las [preguntas más frecuentes sobre Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imágenes

1. **¿Qué imágenes de la galería de máquinas virtuales de SQL Server están disponibles?** 

   Azure mantiene imágenes de máquinas virtuales para todas las versiones principales de SQL Server admitidas en todas las ediciones para Windows y Linux. Para obtener más información, consulte la lista completa de [imágenes de VM de Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imágenes de VM de Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **¿Están actualizadas las imágenes existentes de la galería de máquinas virtuales de SQL Server?**

   Cada dos meses, las imágenes de SQL Server de la galería de máquinas virtuales se actualizan con las últimas actualizaciones de Windows y Linux. En el caso de las imágenes de Windows, esto incluye cualquier actualización que se marque como importante en Windows Update, incluidas las actualizaciones de seguridad importantes de SQL Server y los Service Pack. En el caso de las imágenes de Linux, esto incluye las últimas actualizaciones del sistema. Las actualizaciones acumulativas de SQL Server se tratan de forma diferente para Linux y Windows. En Linux, las actualizaciones acumulativas de SQL Server también se incluyen en la actualización. Pero en este momento, las máquinas virtuales de Windows no se actualizan con las actualizaciones acumulativas de SQL Server o Windows Server.

1. **¿Se pueden quitar las imágenes de máquinas virtuales de SQL Server de la galería?**

   Sí. Azure solo mantiene una imagen por edición y versión principal. Por ejemplo, cuando se lanza un nuevo Service Pack de SQL Server, Azure agrega una nueva imagen a la galería para dicho Service Pack. La imagen SQL Server para el Service Pack anterior se quita inmediatamente de Azure Portal. Sin embargo, todavía está disponible desde PowerShell para su aprovisionamiento durante los tres meses siguientes. Transcurridos tres meses, la imagen del Service Pack anterior dejará de estar disponible. Esta directiva de eliminación también se aplicaría si una versión de SQL Server no recibe soporte técnico cuando llega al final de su ciclo de vida.


1. **¿Es posible implementar una imagen anterior de SQL Server que no sea visible en Azure Portal?**

   Sí, mediante PowerShell. Para obtener más información sobre cómo implementar VM con SQL Server mediante PowerShell, consulte [Aprovisionamiento de máquinas virtuales de SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **¿Puedo crear una imagen de Marketplace de Azure SQL Server generalizada de mi máquina virtual con SQL Server y usarla para implementar máquinas virtuales?**

   Sí, pero debe [registrar todas las máquinas virtuales con SQL Server con el proveedor de recursos de máquina virtual con SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para administrarlas en el portal y poder usar características como la aplicación automática de revisiones y las copias de seguridad automáticas. Al registrar con el proveedor de recursos, también deberá especificar el tipo de licencia para cada máquina virtual con SQL Server. 

1. **¿Puedo usar mi propio disco duro virtual para implementar una máquina virtual con SQL Server?**

   Sí, pero debe [registrar todas las máquinas virtuales con SQL Server con el proveedor de recursos de máquina virtual con SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para administrarlas en el portal y poder usar características como la aplicación automática de revisiones y las copias de seguridad automáticas.

1. **¿Es posible configurar configuraciones que no aparecen en la galería de máquinas virtuales (por ejemplo, Windows 2008 R2 + SQL Server 2012)?**

   No. En el caso de las imágenes de la galería de máquinas virtuales que incluyen SQL Server, debe seleccionar una de las imágenes que se proporcionan a través de Azure Portal o mediante [PowerShell](virtual-machines-windows-ps-sql-create.md). No obstante, tiene la posibilidad de implementar una máquina virtual Windows e instalar automáticamente SQL Server en ella. Debe [registrar su máquina virtual con SQL Server con el proveedor de recursos de máquina virtual con SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para administrarla en el portal y poder usar características como la aplicación automática de revisiones y las copias de seguridad automáticas. 


## <a name="creation"></a>Creación

1. **¿Cómo puedo crear una máquina virtual de Azure con SQL Server?**

   El método más simple es crear una máquina virtual que incluya SQL Server. Para ver un tutorial sobre cómo suscribirse a Azure y crear una máquina virtual con SQL Server desde el portal, consulte [Aprovisionamiento de una máquina virtual con SQL Server en Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Puede seleccionar una imagen de máquina virtual que use licencias de SQL Server de pago por segundo o puede utilizar una imagen que le permita traer su propia licencia de SQL Server. También tiene la opción de instalar manualmente SQL Server en una máquina virtual con una edición de licencia libre (Developer o Express) o reutilizando una licencia local. Asegúrese de [registrar su máquina virtual con SQL Server con el proveedor de recursos de máquina virtual con SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para administrarla en el portal y poder usar características como la aplicación automática de revisiones y las copias de seguridad automáticas. Si trae su propia licencia, debe disponer de [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). Para más información, vea [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server Azure).

1. **¿Cómo puedo migrar mi base de datos local de SQL Server a la nube?**

   En primer lugar, cree una máquina virtual de Azure con una instancia de SQL Server. Luego, migre las bases de datos locales a esa instancia. Para conocer las estrategias de migración de datos, consulte [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencias

1. **¿Cómo puedo instalar mi copia de SQL Server con licencia en una máquina virtual de Azure?**

   Existen tres formas de hacerlo. Si tiene un Contrato Enterprise (EA), puede aprovisionar una de las [imágenes de máquina virtual que admita licencias](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), lo que también se conoce como traiga su propia licencia (BYOL). Si tiene [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), puede habilitar la [Ventaja híbrida de Azure](virtual-machines-windows-sql-ahb.md) en una imagen de pago por uso existente. También puede copiar los medios de instalación de SQL Server en una máquina virtual de Windows Server y, luego, instalar SQL Server en la máquina virtual. Asegúrese de registrar la máquina virtual con SQL Server en el [proveedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) para usar características como la administración de portal, las copias de seguridad automáticas y la aplicación de revisiones automatizada. 

1. **¿Tengo que pagar para obtener una licencia de SQL Server en una máquina virtual de Azure si solo se usa para conmutación por error o en modo en espera?**

   Para tener una licencia pasiva gratuita para un grupo de disponibilidad secundario en espera o una instancia de clúster de conmutación por error, debe cumplir todos los criterios siguientes, tal como se describe en el [PDF de la guía de licencias](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf):

   1. Tiene [movilidad de licencias](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) a través de [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. La instancia de SQL Server pasiva no proporciona datos de SQL Server a los clientes ni ejecuta cargas de trabajo de SQL Server activas. Solo se utiliza para sincronizar con el servidor principal y mantener la base de datos pasiva en un estado de espera semiactiva. Si está proporcionando datos, como informes a clientes que ejecutan cargas de trabajo de SQL Server activas, o si realiza cualquier "trabajo", como copias de seguridad adicionales del servidor secundario, debe ser una instancia de SQL Server con licencia de pago. 
   1. La licencia activa de SQL Server está incluida en Software Assurance y permite **una** instancia de SQL Server secundaria pasiva, con hasta la misma cantidad de proceso que el servidor activo con licencia únicamente. 
   1. La máquina virtual con SQL Server secundaria utiliza el [modelo de licencia](virtual-machines-windows-sql-ahb.md) traiga su propia licencia (BYOL) o Ventaja híbrida de Azure (AHB). 

1. **¿Puedo cambiar una máquina virtual para usar mi propia licencia de SQL Server si se ha creado desde una de las imágenes de la galería de pago por uso?**

   Sí. Puede cambiar fácilmente una imagen de la galería de pago por uso a traiga su propia licencia (BYOL) habilitando la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Para más información, consulte [Modificación del modelo de licencia para una máquina virtual de SQL Server en Azure](virtual-machines-windows-sql-ahb.md). Actualmente, esta función solo está disponible para clientes de nube pública.

1. **¿El cambio de modelos de licencia requerirá un tiempo de inactividad para SQL Server?**

   No. El [cambio del modelo de licencias](virtual-machines-windows-sql-ahb.md) no requiere ningún tiempo de inactividad para SQL Server, ya que el cambio es efectivo de inmediato y no requiere un reinicio de la VM. Sin embargo, para registrar la máquina virtual con SQL Server con el proveedor de recursos de máquina virtual con SQL Server, la [extensión IaaS de SQL](virtual-machines-windows-sql-server-agent-extension.md) es un requisito previo y la instalación de la extensión IaaS de SQL en el modo _completo_ reinicia el servicio SQL Server. Por lo tanto, si es necesario instalar la extensión IaaS de SQL, instálela en modo _ligero_ para una funcionalidad limitada o instálela en modo _completo_ durante una ventana de mantenimiento. La extensión IaaS de SQL instalada en modo _ligero_ se puede actualizar al modo _completo_ en cualquier momento, pero requiere el reinicio del servicio SQL Server. 

1. **¿Puedo usar Azure Portal para administrar varias instancias en la misma máquina virtual?**

   No. La administración desde el portal es una característica proporcionada por el proveedor de recursos de máquina virtual con SQL Server, que se basa en la extensión Agente de IaaS de SQL Server. Como tal, se aplican las mismas limitaciones al proveedor de recursos que a la extensión. El portal solo puede administrar una instancia predeterminada o una instancia con nombre, siempre y cuando se haya configurado correctamente. Para más información sobre estas limitaciones, consulte la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). 

1. **¿Las suscripciones de CSP pueden activar la Ventaja híbrida de Azure?**

   Sí, Ventaja híbrida de Azure está disponible para suscripciones de CSP. En primer lugar, los clientes de CSP deben implementar una imagen de pago por uso y, después, [cambiar el modelo de licencias](virtual-machines-windows-sql-ahb.md) a traiga su propia licencia.

1. **¿El registro de mi máquina virtual en el nuevo proveedor de recursos de máquina virtual con SQL conlleva costos adicionales?**

   No. El proveedor de recursos de máquina virtual con SQL Server habilita funcionalidades de administración adicionales para SQL Server en las máquinas virtuales de Azure sin cargos adicionales. 

1. **¿El proveedor de recursos de máquina virtual con SQL Server está disponible para todos los clientes?**
 
   Sí, siempre que se haya implementado la máquina virtual con SQL Server en la nube pública con el modelo de Resource Manager y no con el modelo clásico. Todos los demás clientes pueden registrarse con el nuevo proveedor de recursos de máquina virtual con SQL Server. Sin embargo, solo los clientes con la ventaja de [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) pueden usar su propia licencia activando la [Ventaja híbrida de Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) en una máquina virtual con SQL Server. 

1. **¿Qué ocurre con el proveedor de recursos (_Microsoft.SqlVirtualMachine_) si se mueve o se quita el recurso de la máquina virtual?** 

   Cuando el recurso Microsoft.Compute/VirtualMachine se coloca o se mueve, se envía una notificación al recurso Microsoft.SqlVirtualMachine asociado para replicar de forma asincrónica la operación.

1. **¿Qué ocurre con la máquina virtual si se quita el recurso del proveedor de recursos (_Microsoft.SqlVirtualMachine_)?**

    El recurso Microsoft.Compute/VirtualMachine no se ve afectado cuando se coloca el recurso Microsoft.SqlVirtualMachine. Sin embargo, los cambios en las licencias devolverán el valor predeterminado al origen de la imagen original. 

1. **¿Es posible registrar máquinas virtuales con SQL Server implementadas automáticamente con el proveedor de recursos de máquina virtual con SQL Server?**

    Sí. Si ha implementado SQL Server desde sus propios medios e instalado la extensión IaaS de SQL, puede registrar la VM con SQL Server con el proveedor de recursos para obtener las ventajas de capacidad de administración proporcionadas por la extensión IaaS de SQL. Sin embargo, no es posible convertir una máquina virtual con SQL Server implementada automáticamente a pago por uso.

1. **¿Es posible cambiar el modelo de licencias en una VM con SQL Server implementada con el modelo clásico?**

   No. No es posible cambiar el modelo de licencias en una máquina virtual clásica. Puede migrar la máquina virtual al modelo de Azure Resource Manager y registrarla con el proveedor de recursos de máquina virtual con SQL Server. Una vez que la máquina virtual está registrada con el proveedor de recursos de máquina virtual con SQL Server, los cambios en el modelo de licencias estarán disponibles en la máquina virtual. 
   


## <a name="administration"></a>Administración

1. **¿Puedo instalar una segunda instancia de SQL Server en la misma máquina virtual? ¿Puedo cambiar las características instaladas de la instancia predeterminada?**

   Sí. Los medios de instalación de SQL Server están ubicados en una carpeta en la unidad **C** . Ejecute **Setup.exe** desde esa ubicación para agregar instancias de SQL Server nuevas o para cambiar otras características instaladas de SQL Server en la máquina. Tenga en cuenta que algunas características, como Copia de seguridad automatizada, Aplicación de revisión automatizada e integración de Azure Key Vault, solo funcionan con la instancia predeterminada o con una instancia con nombre que se haya configurado correctamente (consulte la pregunta 3). 

1. **¿Puedo desinstalar la instancia predeterminada de SQL Server?**

   Sí, pero debe tener en cuenta algunas consideraciones. En primer lugar, es posible que sigan aplicándose cargos por facturación asociada con SQL Server en función del modelo de licencia de la máquina virtual. En segundo lugar, tal como se ha mencionado en la respuesta anterior, hay características que dependen de la [extensión Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Si desinstala la instancia predeterminada sin haber eliminado la extensión IaaS, la extensión continuará buscando la instancia predeterminada y puede generar errores de registro de eventos. Estos errores proceden de los dos orígenes siguientes: **Administración de credenciales de Microsoft SQL Server** y **Agente de IaaS de Microsoft SQL Server**. Es posible que uno de los errores sea similar al siguiente:

      Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible.

   Si decide desinstalar la instancia predeterminada, desinstale también la [Extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). 

1. **¿Puedo usar una instancia con nombre de SQL Server con la extensión IaaS?**
   
   Sí, en caso de que la instancia con nombre sea la única instancia de SQL Server y que la instancia predeterminada original se haya [desinstalado correctamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Si no hay ninguna instancia predeterminada y hay varias instancias con nombre en una única máquina virtual con SQL Server, no se podrá instalar la extensión Agente de IaaS de SQL Server. 

1. **¿Puedo quitar SQL Server por completo de una máquina virtual con SQL Server?**

   Sí, pero se le seguirá cobrando por la máquina virtual con SQL Server, tal y como se describe en [Orientación de precios de SQL Server para máquinas virtuales de Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Si ya no necesita SQL Server, puede implementar una nueva máquina virtual y migrar los datos y aplicaciones a la nueva máquina virtual. Después, puede quitar la máquina virtual de SQL Server.
   
## <a name="updating-and-patching"></a>Actualizaciones y revisiones

1. **¿Cómo cambio a una versión o edición de SQL Server diferente en una máquina virtual de Azure?**

   Para cambiar su versión o edición de SQL Server, los clientes pueden utilizar los medios de instalación que contengan la versión o edición de SQL Server que deseen. Una vez que se ha cambiado la edición, utilice Azure Portal para modificar la propiedad edition de la máquina virtual para que refleje con precisión la facturación de la máquina virtual. Para más información, consulte cómo [cambiar la edición de una máquina virtual de SQL Server](virtual-machines-windows-sql-change-edition.md). No hay ninguna diferencia de facturación para las distintas versiones de SQL Server, por lo que una vez que se ha cambiado la versión de SQL Server, no es necesario realizar ninguna otra acción.

1. **¿Dónde puedo obtener el soporte de instalación para cambiar la edición o la versión de SQL Server?**

  Los clientes que cuenten con [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) pueden obtener el soporte de instalación desde el [centro de licencias por volumen](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Los clientes que no tengan Software Assurance pueden usar el soporte de instalación de una imagen de máquina virtual con SQL Server de Marketplace que tenga la edición que deseen.

1. **¿Cómo se aplican las actualizaciones y los Service Packs en una máquina virtual de SQL Server?**

   Las máquinas virtuales brindan control sobre la máquina virtual, incluido el momento en que se aplican las actualizaciones y la manera de hacerlas. En el caso del sistema operativo, puede aplicar manualmente las actualizaciones de Windows, o bien puede habilitar un servicio de programación llamado [Aplicación de revisiones automatizada](virtual-machines-windows-sql-automated-patching.md). Aplicación de revisión automatizada instala las actualizaciones marcadas como importantes, incluidas las actualizaciones de SQL Server que tienen esa categoría. Otras actualizaciones opcionales a SQL Server se deben instalar manualmente.

1. **¿Puedo actualizar mi instancia de SQL Server 2008/2008 R2 después de registrarla con el proveedor de recursos de máquina virtual con SQL Server?**

   Sí. Puede usar cualquier soporte de instalación para actualizar la versión y edición de SQL Server y, a continuación, puede actualizar el [modo de la extensión IaaS de SQL](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) de _sin agente_ a _completo_. Esto le dará acceso a todas las ventajas de la extensión IaaS de SQL, como la capacidad de administración de portal, las copias de seguridad automáticas y la aplicación de revisiones automatizada. 

## <a name="general"></a>General

1. **¿Son compatibles las instancias del clúster de conmutación por error (FCI) de SQL Server con Azure Virtual Machines?**

   Sí. También puede [crear un clúster de conmutación por error de Windows en Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) y usar Espacios de almacenamiento directo (S2D) para el almacenamiento del clúster. También puede usar soluciones de agrupación en clústeres o almacenamiento de terceros como se describe en [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > En este momento, la _extensión Agente de IaaS de SQL Server_ [completa](virtual-machines-windows-sql-server-agent-extension.md) no es compatible con FCI de SQL Server en Azure. Recomendamos que desinstale la extensión _completa_ de las máquinas virtuales que participan en la FCI y que, en su lugar, instale la extensión en modo _ligero_. Esta extensión admite características, como las copias de seguridad automáticas y la aplicación de revisiones automatizada y algunas características del portal de SQL Server. Estas características no funcionarán para las máquinas virtuales con SQL Server después de desinstalar el agente _completo_.

1. **¿Cuál es la diferencia entre las máquinas virtuales con SQL Server y el servicio SQL Database?**

   De manera conceptual, ejecutar SQL Server en una máquina virtual de Azure no es diferente a ejecutar SQL Server en un centro de datos remoto. En cambio, [SQL Database](../../../sql-database/sql-database-technical-overview.md) ofrece base de datos como servicio. Con SQL Database, no se tiene acceso a las máquinas que hospedan las bases de datos. Para una comparación completa, consulte [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) (Selección de una opción Azure SQL en la nube: Azure SQL (PaaS) Database o SQL Server en máquinas virtuales de Azure (IaaS).

1. **¿Cómo instalo herramientas de datos de SQL en mi máquina virtual de Azure?**

    Descargue e instale las herramientas de datos de SQL desde [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **¿Las transacciones distribuidas con MSDTC se admiten en las VM con SQL Server?**
   
    Sí. El DTC local es compatible con SQL Server 2016 SP2 y versiones posteriores. Sin embargo, las aplicaciones deben probarse cuando se utilicen los grupos de disponibilidad Always On, ya que las transacciones en proceso durante una conmutación por error producirán un error y se deberán intentar de nuevo. El DTC en clúster está disponible a partir de Windows Server 2019. 

## <a name="resources"></a>Recursos

**Máquinas virtuales Windows**:

* [Introducción a SQL Server en máquinas virtuales Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionamiento de una máquina virtual Windows con SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Prácticas recomendadas para mejorar el rendimiento para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Estrategias de desarrollo y patrones de aplicación de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Máquinas virtuales Linux**:

* [Introducción a SQL Server en máquinas virtuales Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionamiento de una máquina virtual Linux con SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Preguntas más frecuentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
