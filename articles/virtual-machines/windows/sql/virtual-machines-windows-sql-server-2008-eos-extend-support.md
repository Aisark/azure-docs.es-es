---
title: Ampliación de la compatibilidad con SQL Server 2008 y SQL Server 2008 R2
description: Amplíe la compatibilidad de SQL Server 2008 y SQL Server 2008 R2 mediante la migración de una instancia de SQL Server a Azure o mediante la compra de compatibilidad ampliada para mantener las instancias locales.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6b2546226d31334592034cfe0455fc32644a3900
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132780"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Ampliar la compatibilidad de SQL Server 2008 y SQL Server 2008 R2 con Azure

SQL Server 2008 y SQL Server 2008 R2 están llegando el [fin de ciclo de vida de soporte (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Dado que muchos de nuestros clientes siguen usando ambas versiones, ofrecemos varias opciones para seguir recibiendo soporte técnico. Puede migrar las instancias de SQL Server locales a máquinas virtuales (VM) de Azure, migrar a Azure SQL Database o mantener el entorno local y comprar actualizaciones de seguridad ampliadas.

A diferencia de una instancia administrada, la migración a una VM de Azure no requiere la recertificación de sus aplicaciones. Además, a diferencia de lo que ocurre al mantener un entorno local, al migrar a una máquina virtual de Azure, recibirá revisiones de seguridad ampliadas gratuitas.

En el resto de este artículo se proporcionan consideraciones sobre la migración de la instancia de SQL Server a una VM de Azure.

## <a name="provisioning"></a>Aprovisionamiento

Hay una imagen de pago por uso de **SQL Server 2008 R2 en Windows Server 2008 R2** disponible en Azure Marketplace.

Los clientes que usan SQL Server 2008 deberán instalarse automáticamente o actualizar a SQL Server 2008 R2. Del mismo modo, los clientes que usan Windows Server 2008 deberán implementar su máquina virtual desde un disco duro virtual personalizado o actualizar a Windows Server 2008 R2.

Las imágenes implementadas con Azure Marketplace tienen la extensión SQL IaaS preinstalada. La extensión SQL IaaS es un requisito para las licencias flexibles y la aplicación de revisiones automatizada. Los clientes que implementen máquinas virtuales autoinstaladas deberán instalar manualmente la extensión SQL IaaS. La extensión SQL IaaS no se admite en Windows Server 2008.

> [!NOTE]
> Si bien el servidor SQL Server y las hojas **Crear** y **Administrar** funcionarán con la imagen de SQL Server 2008 R2 en Azure Portal, las siguientes características _no se admiten_: Copias de seguridad automáticas, integración de Azure Key Vault, R Services y configuración de almacenamiento.

## <a name="licensing"></a>Licencias
Las implementaciones de SQL Server 2008 R2 de pago por uso se pueden convertir en [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para convertir una licencia basada en Software Assurance (SA) a una licencia de pago por uso, los clientes deben registrarse en el [proveedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) de máquina virtual con SQL. Después del registro, el tipo de licencia de SQL será intercambiable entre Ventaja híbrida de Azure y "de pago por uso".

Las instancias de SQL Server 2008 o SQL Server 2008 R2 autoinstaladas en la máquina virtual de Azure se pueden registrar con el proveedor de recursos de SQL y convertir su tipo de licencia a de pago por uso.

## <a name="migration"></a>Migración
Puede migrar las instancias de SQL Server con finalización del soporte a una máquina virtual de Azure con métodos manuales de copia de seguridad y restauración. Este es el método de migración más común del entorno local a una máquina virtual de Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para las migraciones masivas, se recomienda usar el servicio [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Con Azure Site Recovery, los clientes pueden replicar la máquina virtual completa, incluido SQL Server desde un entorno local a una máquina virtual de Azure.

SQL Server necesita instantáneas coherentes con la aplicación de Azure Site Recovery para garantizar la recuperación. Azure Site Recovery admite instantáneas coherentes con la aplicación con un intervalo mínimo de una hora. El objetivo de punto de recuperación (RPO) mínimo posible para SQL Server con las migraciones de Azure Site Recovery es de una hora. El objetivo de tiempo de recuperación (RTO) es de dos horas más el tiempo de recuperación de SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) es una opción para los clientes si migran desde un entorno local a una máquina virtual de Azure mediante la actualización de SQL Server a SQL Server 2012 y versiones posteriores.

## <a name="disaster-recovery"></a>Recuperación ante desastres

Las soluciones de recuperación ante desastres para SQL Server con finalización del soporte en una máquina virtual de Azure son las siguientes:

- **Copias de seguridad de SQL Server**: use Azure Backup para ayudar a proteger el servidor SQL Server con finalización del soporte contra ransomware, la eliminación accidental y daños. Actualmente, la solución se ofrece como versión preliminar para EOS SQL Server y admite SQL Server 2008 y 2008 R2 que se ejecutan en Windows 2008 R2 SP1. Para más información, consulte [este artículo](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support).
- **Trasvase de registros**: puede crear una réplica de trasvase de registros en otra zona o región de Azure con restauraciones continuas para reducir el RTO. Tiene que configurar manualmente el trasvase de registros.
- **Azure Site Recovery**: Puede replicar la VM entre zonas y regiones a través de la replicación de Azure Site Recovery. SQL Server necesita instantáneas coherentes con la aplicación para garantizar la recuperación en caso de desastre. Azure Site Recovery ofrece un RPO mínimo de una hora y un RTO de dos horas (más el tiempo de recuperación de SQL Server) para la recuperación ante desastres de SQL Server con finalización del soporte.

## <a name="security-patching"></a>Aplicación de revisiones de seguridad
Las actualizaciones de seguridad ampliadas para las máquinas virtuales con SQL Server se entregarán en los canales de Microsoft Update cuando la máquina virtual con SQL Server se haya registrado en el [proveedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) de máquina virtual con SQL. Las revisiones se pueden descargar manual o automáticamente.

*Automated patching* está habilitada de forma predeterminada. La aplicación de revisiones automatizada permite a Azure aplicar automáticamente las revisiones de SQL Server y del sistema operativo. Puede especificar un día de la semana, una hora y una duración para una ventana de mantenimiento si está instalada la extensión de SQL Server IaaS. Azure realiza la aplicación de revisión en esta ventana de mantenimiento. La programación de la ventana de mantenimiento utiliza la configuración regional de la máquina virtual para la hora.  Para más información, consulte [Aplicación de revisiones automatizada para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Pasos siguientes

Migración de la máquina virtual con SQL Server a Azure:

* [Migración de una base de datos SQL Server a SQL Server en una VM de Azure](virtual-machines-windows-migrate-sql.md)

Introducción a SQL Server en Azure Virtual Machines:

* [Creación de una máquina virtual con SQL Server en Azure Portal](quickstart-sql-vm-create-portal.md)

Obtenga respuestas a las preguntas más habituales acerca de las máquinas virtuales con SQL Server:

* [Preguntas más frecuentes para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
