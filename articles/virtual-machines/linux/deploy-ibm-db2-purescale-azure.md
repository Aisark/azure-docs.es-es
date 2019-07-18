---
title: Implementación de IBM DB2 pureScale en Azure
description: Obtenga información sobre cómo implementar un ejemplo de arquitectura utilizado recientemente para migrar una empresa de su entorno de IBM DB2 con z/OS a IBM DB2 pureScale en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 68fde09b1ee5f18aa784793cc19e9f547b19ed43
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871877"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implementación de IBM DB2 pureScale en Azure

En este artículo, se muestra cómo implementar un [ejemplo de arquitectura](ibm-db2-purescale-azure.md) que un cliente empresarial usó recientemente para migrar de su entorno de IBM DB2 con z/OS a IBM DB2 pureScale en Azure.

Para seguir los pasos que se usan para la migración, vea los scripts de instalación en el repositorio [DB2onAzure](https://aka.ms/db2onazure) de GitHub. Estos scripts están basados en la arquitectura usada para una carga de trabajo de procesamiento de transacciones en línea (OLTP) de tamaño mediano.

## <a name="get-started"></a>Primeros pasos

Para implementar esta arquitectura, descargue y ejecute el script deploy.sh que puede encontrar en el repositorio [DB2onAzure](https://aka.ms/db2onazure) de GitHub.

El repositorio también tiene scripts para configurar un panel de Grafana. Puede usar el panel para consultar Prometheus, el sistema de supervisión y creación de alertas de código abierto que incluye DB2.

> [!NOTE]
> El script deploy.sh en el cliente crea las claves SSH privadas y las pasa a la plantilla de implementación a través de HTTPS. Para mayor seguridad, se recomienda usar [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para almacenar secretos, claves y contraseñas.

## <a name="how-the-deployment-script-works"></a>Cómo funciona el script de implementación

El script deploy.sh crea y configura los recursos de Azure de esta arquitectura. El script le solicita tanto la suscripción de Azure como las máquinas virtuales utilizadas en el entorno de destino y, a continuación, realiza las siguientes operaciones:

-   Configura el grupo de recursos, la red virtual y las subredes en Azure para la instalación.

-   Configura los grupos de seguridad de red y SSH para el entorno

-   Configura NIC en las máquinas virtuales de GlusterFS y DB2 pureScale.

-   Crea las máquinas virtuales de almacenamiento de GlusterFS.

-   Crea la máquina virtual del servidor de salto.

-   Crea las máquinas virtuales de DB2 pureScale.

-   Crea la máquina virtual testigo a la que DB2 pureScale envía pings.

-   Crea una máquina virtual de Windows para efectuar las pruebas, pero no instala nada en ella.

A continuación, los scripts de implementación configuran una red de área de almacenamiento virtual (vSAN) de iSCSI para el almacenamiento compartido en Azure. En este ejemplo, iSCSI se conecta a GlusterFS. Esta solución también le ofrece la opción de instalar los destinos iSCSI como un solo nodo de Windows. iSCSI proporciona una interfaz de almacenamiento de bloque compartido a través de TCP/IP que permite que el procedimiento de configuración de DB2 pureScale utilice una interfaz de dispositivo para conectarse al almacenamiento compartido. Para conocer los aspectos básicos de GlusterFS, consulte el tema [Architecture: Types of volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) (Arquitectura: tipos de volúmenes) en Gluster Docs.

Los scripts de implementación ejecutan los siguientes pasos generales:

1.  Use GlusterFS para configurar un clúster de almacenamiento compartido en Azure. Este paso implica al menos dos nodos de Linux. Para obtener más información sobre la configuración, vea [Setting up Red Hat Gluster Storage in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) (Configuración del almacenamiento de Red Hat Gluster en Microsoft Azure) en la documentación de Red Hat Gluster.

2.  Configuran una interfaz directa de iSCSI en servidores de Linux de destino para GlusterFS. Para obtener más detalles sobre la configuración, consulte [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) en la Guía de administración de GlusterFS.

3.  Configure el iniciador iSCSI en las máquinas virtuales de Linux. El iniciador tendrá acceso al clúster de GlusterFS mediante el uso de un destino iSCSI. Para obtener más detalles sobre el programa de instalación, consulte [How To Configure An iSCSI Target And Initiator In Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) (Cómo configurar un destino y un iniciador de iSCSI en Linux) en la documentación de RootUsers.

4.  Instale GlusterFS como la capa de almacenamiento de la interfaz de iSCSI.

Después de que los scripts creen el dispositivo iSCSI, el último paso es instalar DB2 pureScale. Como parte de la configuración de DB2 pureScale, [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (conocido anteriormente como GPFS) se compila y se instala en el clúster GlusterFS. Este sistema de archivos en clúster permite que DB2 pureScale comparta datos entre varias máquinas virtuales que ejecutan el motor de DB2 pureScale. Para obtener más información, consulte la documentación de [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) en el sitio web de IBM.

## <a name="db2-purescale-response-file"></a>Archivo de respuesta de DB2 pureScale

El repositorio de GitHub incluye DB2server.rsp, un archivo de respuesta (.rsp) que le permite generar un script automatizado para la instalación de DB2 pureScale. En la tabla siguiente se enumeran las opciones de DB2 pureScale que usa el archivo de respuesta para la instalación. Puede personalizar el archivo de respuesta según sea necesario para su entorno.

> [!NOTE]
> Se incluye un archivo de respuesta de ejemplo, DB2server.rsp, en el repositorio de GitHub [DB2onAzure](https://aka.ms/db2onazure). Si usa este archivo, debe editarlo para que funcione en su entorno.

| Nombre de pantalla               | Campo                                        | Valor                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bienvenido                   |                                              | Nueva instalación                                                                                           |
| Elija un producto          |                                              | DB2 versión 11.1.3.3. Ediciones de servidor con DB2 pureScale                                              |
| Configuración             | Directorio                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Seleccione el tipo de instalación                 | Habitual                                                                                               |
|                           | Acepto los términos de IBM                     | Activado                                                                                               |
| Propietario de la instancia            | Usuario existente para la instancia, nombre de usuario        | DB2sdin1                                                                                              |
| Usuario delimitado               | Usuario existente, nombre de usuario                     | DB2sdfe1                                                                                              |
| Sistema de archivos en clúster       | Ruta de acceso del dispositivo de partición de disco compartida            | /dev/dm-2                                                                                             |
|                           | Punto de montaje                                  | /DB2sd\_1804a                                                                                         |
|                           | Disco compartido para los datos                         | /dev/dm-1                                                                                             |
|                           | Punto de montaje (datos)                           | /DB2fs/datafs1                                                                                        |
|                           | Disco compartido para el registro                          | /dev/dm-0                                                                                             |
|                           | Punto de montaje (registro)                            | /DB2fs/logfs1                                                                                         |
|                           | Elemento diferenciador de los servicios de clúster de DB2. Ruta de acceso del dispositivo | /dev/dm-3                                                                                             |
| Lista de hosts                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF principal preferido                         | cf1                                                                                                   |
|                           | CF secundario preferido                       | cf2                                                                                                   |
| Archivo de respuesta y resumen | primera opción                                 | Instalar la edición de servidor de DB2 con la característica IBM DB2 pureScale y guardar mi configuración en un archivo de respuesta |
|                           | Nombre del archivo de respuesta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Notas sobre esta implementación

- Los valores de /dev-dm0, /dev-dm1, /dev-dm2 y /dev-dm3 pueden cambiar tras un reinicio de la máquina virtual donde realiza la configuración (d0 en el script automatizado). Para encontrar los valores correctos, puede emitir el siguiente comando antes de completar el archivo de respuesta en el servidor en el que se va a ejecutar el programa de instalación:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Los scripts de instalación utilizan alias para los discos de iSCSI, con el fin de que los nombres reales se puedan encontrar fácilmente.

- Cuando el script de instalación se ejecuta en d0, los valores de **/dev/dm-\*** pueden ser distintos en d1, cf0 y cf1. La diferencia en los valores no afecta a la configuración de DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Solución de problemas conocidos

El repositorio de GitHub incluye una instancia de Knowledge Base que los autores mantienen. Enumera los posibles problemas que pueden producirse y las soluciones que puede probar. Por ejemplo, pueden producirse problemas conocidos cuando:

-   Intenta acceder a la dirección IP de la puerta de enlace.

-   Compila la licencia pública general (GPL).

-   Cuando se produce un error en el protocolo de enlace de seguridad entre hosts.

-   Cuando el instalador de DB2 detecta un sistema de archivos existente.

-   Instala manualmente la instancia de IBM Spectrum Scale.

-   Cuando, después de crear IBM Spectrum Scale, instala DB2 pureScale.

-   Quita DB2 pureScale y IBM Spectrum Scale.

Para obtener más información sobre estos y otros problemas conocidos, consulte el archivo kb.md en el repositorio de [DB2onAzure](https://aka.ms/DB2onAzure).

## <a name="next-steps"></a>Pasos siguientes

-   [iSCSI en GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Creación de los usuarios necesarios para una instalación de DB2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [Comando dB2icrt - Crear instancia](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale Clusters Data Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1) (Solución de datos para los clústeres de DB2 pureScale)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance: IBM DB2 en Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/) (Centro de datos virtual de Azure: guía para una migración lift-and-shift)
