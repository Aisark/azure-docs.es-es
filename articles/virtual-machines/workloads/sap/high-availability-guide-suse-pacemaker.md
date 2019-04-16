---
title: Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure | Microsoft Docs
description: Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 62356ee35631373b5a5d38ed356bbb2fb489807b
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577802"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configuración de Pacemaker en SUSE Linux Enterprise Server en Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-not-requiring-a-reboot
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#maintenance-not-requiring-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Existen dos opciones para configurar un clúster de Pacemaker en Azure. Se puede usar un agente de vallado, que se encarga de reiniciar un nodo con error a través de las API de Azure, o un dispositivo SBD.

El dispositivo SBD requiere al menos una máquina virtual adicional que actúe como servidor de destino iSCSI y proporcione un dispositivo SBD. Estos servidores de destino iSCSI, sin embargo, pueden compartirse con otros clústeres de Pacemaker. La ventaja de usar un dispositivo SBD es que el tiempo de conmutación por error es menor y, si utiliza dispositivos SBD en un entorno local, no se necesitan cambios en el modo de operar con el clúster de Pacemaker. Puede utilizar hasta tres dispositivos SBD para un clúster de Pacemaker para permitir que un dispositivo SBD deje de estar disponible, por ejemplo durante la aplicación de revisiones del sistema operativo del servidor de destino iSCSI. Si desea utilizar más de un dispositivo SBD por Pacemaker, asegúrese de implementar varios servidores de destino iSCSI y conectar un SBD desde cada servidor de destino iSCSI. Se recomienda usar un dispositivo SBD o tres. Pacemaker no podrá vallar automáticamente un nodo de clúster si solo configura dos dispositivos SBD y uno de ellos no está disponible. Si desea poder vallar cuando un servidor de destino iSCSI esté inactivo, deberá usar tres dispositivos SBD y, por tanto, tres servidores de destino iSCSI.

Si no desea invertir en otra máquina virtual, también puede utilizar el agente de barrera de Azure. El inconveniente es que una conmutación por error puede tardar entre 10 y 15 minutos si se produce un error en una detención de recursos o se interrumpe la comunicación entre los nodos del clúster.

![Información general de Pacemaker en SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Cuando planifique e implemente nodos de clúster de Linux Pacemaker y dispositivos SBD, es esencial para la confiabilidad general de la configuración de clúster completa que el enrutamiento entre las máquinas virtuales involucradas y las máquinas virtuales que hospedan el dispositivo SBD no pasen a través de cualquier otro dispositivo como [NVA](https://azure.microsoft.com/solutions/network-appliances/). En caso contrario, los problemas y eventos de mantenimiento con el NVA pueden tener un impacto negativo en la estabilidad y confiabilidad de la configuración general del clúster. Para evitar estos obstáculos, no defina reglas de enrutamiento de NVA o [Reglas de enrutamiento definidas por el usuario](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) que enruten el tráfico entre nodos en clúster y los dispositivos SBD a través de NVA y dispositivos similares al planear e implementar nodos de clúster de Linux Pacemaker y dispositivos SBD. 
>

## <a name="sbd-fencing"></a>Vallado de SBD

Siga estos pasos si desea usar un dispositivo SBD como vallado.

### <a name="set-up-iscsi-target-servers"></a>Configuración de servidores de destino iSCSI

En primer lugar, debe crear las máquinas virtuales de destino iSCSI. Los servidores de destino iSCSI pueden compartirse con varios clústeres de Pacemaker.

1. Implemente nuevas máquinas virtuales SLES 12 SP1 o superior y conéctelas mediante SSH. No es necesario que las máquinas sean grandes. Un tamaño de máquina virtual como Standard_E2s_v3 o Standard_D2s_v3 es suficiente. Asegúrese de usar almacenamiento Premium en el disco del sistema operativo.

Ejecute los siguientes comandos en todas las **máquinas virtuales de destino iSCSI**.

1. Actualice SLES.

   <pre><code>sudo zypper update
   </code></pre>

1. Quitar paquetes

   Para evitar un problema conocido con targetcli y SLES 12 SP3, desinstale los siguientes paquetes. Puede omitir los errores acerca de los paquetes que no se encuentran.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instale paquetes de destino iSCSI.

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Habilite el servicio de destino iSCSI.

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Creación de dispositivo iSCSI en el servidor de destino iSCSI.

Ejecute los siguientes comandos en todas las **máquinas virtuales de destino iSCSI** para crear los discos iSCSI para los clústeres utilizados por los sistemas SAP. En el ejemplo siguiente, se crean dispositivos SBD para varios clústeres. Se muestra cómo se podría usar un servidor de destino iSCSI para varios clústeres. Los dispositivos SBD se colocan en el disco del sistema operativo. Asegúrese de que dispone de suficiente espacio.

**`nfs`** se usa para identificar el clúster NFS, **ascsnw1** se usa para identificar el clúster ASCS de **NW1**, **dbnw1** se usa para identificar el clúster de base de datos de **NW1** , **nfs 0** y **nfs 1** son los nombres de host de los nodos del clúster NFS **nw1-xscs-0** y **nw1-xscs-1**son los nombres de host de la **NW1** , los nodos de clúster de ASCS y **nw1-db-0** y **nw1-db-1** son los nombres de host de la base de datos de nodos del clúster. Sustitúyalos por los nombres de host de los nodos del clúster y el SID del sistema SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Puede comprobar si todo se ha configurado correctamente con

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Configuración de un dispositivo SBD

Conéctese al dispositivo iSCSI que se creó en el último paso desde el clúster.
Ejecute los siguientes comandos en los nodos del clúster nuevo que desea crear.
Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2.

1. **[A]**  Conexión a los dispositivos iSCSI

   En primer lugar, habilite los servicios iSCSI y SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Cambio del nombre del iniciador en el primer nodo

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Cambie el contenido del archivo para que coincida con las ACL que utilizó al crear el dispositivo iSCSI en el servidor de destino iSCSI, por ejemplo para el servidor NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Cambio del nombre del iniciador en el segundo nodo

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Cambie el contenido del archivo para que coincida con las ACL que utilizó al crear el dispositivo iSCSI en el servidor de destino iSCSI.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Reinicio del servicio iSCSI

   Reinicie ahora el servicio iSCSI para aplicar el cambio.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Conecte los dispositivos iSCSI. En el ejemplo siguiente, 10.0.0.17 es la dirección IP del servidor de destino iSCSI y 3260 es el puerto predeterminado. <b>iqn.2006-04.nfs.local:nfs</b> es uno de los nombres de destino que aparece cuando se ejecuta el primer comando siguiente (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Asegúrese de que los dispositivos iSCSI están disponibles y anote el nombre del dispositivo (en el ejemplo siguiente/dev/sde).

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Ahora, recupere los identificadores de los dispositivos iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   El comando presenta tres identificadores de dispositivo para cada dispositivo SBD. Se recomienda utilizar el identificador que empieza por scsi-3, en el ejemplo anterior, esto es

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Creación del dispositivo SBD

   Use el identificador de dispositivo de los dispositivos iSCSI para crear dispositivos SBD en el primer nodo del clúster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Adaptación de la configuración de SBD

   Abra el archivo de configuración de SBD.

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Cambie la propiedad del dispositivo SBD, habilite la integración de Pacemaker y cambie el modo de inicio del SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Crear el `softdog` archivo de configuración

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Ahora cargue el módulo.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalación del clúster

Los elementos siguientes tienen el prefijo **[A]**: aplicable a todos los nodos, **[1]**: aplicable solo al nodo 1 o **[2]**: aplicable solo al nodo 2.

1. **[A]** Actualice SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]**  Configurar el sistema operativo

   En algunos casos, Pacemaker crea muchos procesos y, por tanto, agota al número de procesos permitidos. En tal caso, un latido entre los nodos del clúster podría producir un error y dar lugar a la conmutación por error de los recursos. Se recomienda aumentar los procesos máximos permitidos al establecer el parámetro siguiente.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Reduzca el tamaño de la caché de datos incorrectos. Para más información, consulte [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Bajo rendimiento de escritura en servidores SLES 11/12 con RAM grande).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Configurar netconfig-en la nube de azure para el clúster de alta disponibilidad

   Cambiar el archivo de configuración para la interfaz de red, como se muestra a continuación para evitar que el complemento de red en la nube de quitar la dirección IP virtual (Pacemaker debe controlar la asignación de VIP). Para obtener más información, consulte [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Habilite el acceso de SSH

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Habilite el acceso de SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Habilite el acceso de SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Instale agentes de barrera
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]** Configure la resolución nombres de host

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos. La ventaja de usar /etc/hosts es que el clúster es independiente de DNS, lo que también podría representar un único punto de error.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instale el clúster

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Agregue un nodo al clúster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Haga que la contraseña de hacluster coincida

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Configure corosync para usar otro tipo de transporte y agregue nodelist. De lo contrario, el clúster no funciona.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Agregue el siguiente contenido en negrita al archivo si los valores no están ahí o son diferentes. No olvide cambiar el token a 30000 para permitir el mantenimiento con conservación de memoria. Para obtener más información, consulte [este artículo para Linux][virtual-machines-linux-maintenance] o [Windows][virtual-machines-windows-maintenance]. Además, asegúrese de quitar el parámetro mcastaddr.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Después, reinicie el servicio corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Creación de un dispositivo STONITH de agente de barrera de Azure

El dispositivo STONITH usa una entidad de servicio para la autorización de Microsoft Azure. Siga estos pasos para crear una entidad de servicio.

1. Vaya a [https://portal.azure.com](https://portal.azure.com)
1. Abra la hoja Azure Active Directory  
   Vaya a Propiedades y anote el identificador del directorio. Se trata del **id. de inquilino**.
1. Haga clic en Registros de aplicaciones
1. Haga clic en Agregar
1. Escriba un nombre, seleccione el tipo de aplicación "Aplicación web o API", escriba una dirección URL de inicio de sesión (por ejemplo, http\://localhost) y haga clic en Crear
1. La dirección URL de inicio de sesión no se usa y puede ser cualquier dirección URL válida
1. Seleccione la nueva aplicación y haga clic en las llaves de la pestaña Configuración
1. Escriba una descripción para la nueva clave, seleccione "Nunca expira" y haga clic en Guardar
1. Anote el valor. Se utiliza como **contraseña** para la entidad de servicio
1. Anote el identificador de la aplicación. Se utiliza como nombre de usuario (**Id. de inicio de sesión** en los pasos siguientes) de la entidad de servicio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Creación de un rol personalizado para el agente de barrera

La entidad de servicio no tiene permiso para tener acceso a los recursos de Azure de forma predeterminada. Debe concedérselos para iniciar y detener (desasignar) todas las máquinas virtuales del clúster. Si no ha creado aún el rol personalizado, puede crearlo mediante [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) o la [CLI de Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).

Utilice el siguiente contenido para el archivo de entrada. Debe adaptar el contenido a sus suscripciones; esto es, reemplace c276fc76-9cd4-44c9-99a7-4fd71546436e y e91d47c4-76f3-4271-a796-21b4ecfe3624 por los identificadores de su suscripción. Si solo tiene una suscripción, quite la segunda entrada en AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Asignación del rol personalizado a la entidad de servicio

Asigne el rol personalizado "Rol del agente de barrera de Linux" que se creó en el último capítulo a la entidad de servicio. Deje de utilizar el rol de propietario.

1. Vaya a [https://portal.azure.com](https://portal.azure.com)
1. Abra la hoja Todos los recursos
1. Seleccione la máquina virtual del primer nodo de clúster.
1. Haga clic en Control de acceso (IAM)
1. Haga clic en Agregar asignación de roles.
1. Seleccione el rol "Rol de agente de barrera de Linux".
1. Escriba el nombre de la aplicación que creó anteriormente
1. Haga clic en Guardar

Repita los pasos anteriores para el segundo nodo de clúster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Cree los dispositivos STONITH

Después de editar los permisos para las máquinas virtuales, puede configurar los dispositivos STONITH en el clúster.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configuración predeterminada de Pacemaker para SBD

1. **[1]**  Habilite el uso de un dispositivo STONITH y establezca el retraso de la barrera

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Configuración de pacemaker para Azure eventos programados

Azure ofrece [eventos programados](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/scheduled-events). Eventos programados se proporcionan a través del servicio de metadatos y deje tiempo para la aplicación para prepararse para los eventos, como apagado de máquina virtual, la reimplementación de máquina virtual, etcetera. Agente de recursos **[eventos de azure](https://github.com/ClusterLabs/resource-agents/pull/1161)** supervisa si hay eventos programados de Azure. Si se detectan los eventos, el agente intentará detener todos los recursos en la máquina virtual afectada y moverlos a otro nodo del clúster. Debe configurarse lograr que más recursos de Pacemaker. 

1. **[A]**  Instalar el **eventos de azure** agente. 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]**  Configurar los recursos de Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Después de configurar los recursos de Pacemaker para el agente de eventos de azure, al colocar el clúster o no en modo de mantenimiento, es posible recibir mensajes de advertencia como:  
     Advertencia: cib-bootstrap-options: atributo desconocido ' hostName_  <strong>hostname</strong>'  
     Advertencia: cib-bootstrap-options: atributo desconocido 'azure-events_globalPullState'  
     Advertencia: cib-bootstrap-options: atributo desconocido ' hostName_ <strong>hostname</strong>'  
   > Estos mensajes de advertencia pueden ignorarse.

## <a name="next-steps"></a>Pasos siguientes

* [Planeación e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* [Alta disponibilidad para NFS en máquinas virtuales de Azure en SUSE Linux Enterprise Server][sles-nfs-guide]
* [Alta disponibilidad para SAP NetWeaver en máquinas virtuales de Azure en SUSE Linux Enterprise Server para SAP Applications][sles-guide]
* Para información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure Virtual Machines, consulte [Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure][sap-hana-ha]
