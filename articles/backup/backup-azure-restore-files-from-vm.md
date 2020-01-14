---
title: Recuperación de archivos y carpetas desde una copia de seguridad de máquina virtual de Azure
description: En este artículo, aprenderá a recuperar archivos y carpetas desde un punto de recuperación de la máquina virtual de Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4fd5de0c199bfe104b8bb4f5b33b9ed8a86924f6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392558"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperación de archivos desde una copia de seguridad de máquina virtual de Azure

Azure Backup ofrece la funcionalidad de restauración de [discos y máquinas virtuales de Azure](./backup-azure-arm-restore-vms.md) desde copias de seguridad de máquina virtual de Azure, también conocidos como puntos de recuperación. En este artículo se explica cómo recuperar archivos y carpetas desde una copia de seguridad de máquina virtual de Azure. La restauración de archivos y carpetas está disponible unicamente para las máquinas virtuales de Azure implementadas con el modelo de Resource Manager y protegidas en un almacén de Recovery Services.

> [!Note]
> Esta característica está disponible en las máquinas virtuales de Azure implementadas con el modelo de Resource Manager y protegidas en un almacén de Recovery Services.
> No se pueden recuperar archivos a partir de una copia de seguridad de máquina virtual cifrada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montaje del volumen y copia de archivos

Para restaurar archivos o carpetas desde el punto de recuperación, vaya a la máquina virtual y elija el punto de recuperación deseado.

1. Inicie sesión en [Azure Portal](https://portal.Azure.com) y, en el panel izquierdo, haga clic en **Máquinas virtuales**. En la lista de máquinas virtuales, seleccione la que desee para abrir su panel.

2. En el menú de la máquina virtual, haga clic en **Backup** para abrir el panel de Backup.

    ![Apertura del elemento de copia de seguridad del almacén de Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. En el menú del panel de Backup, haga clic en **Recuperación de archivos**.

    ![Botón Recuperación de archivos](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Se abre el menú **Recuperación de archivos**.

    ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. En el menú desplegable **Seleccionar punto de recuperación**, elija el punto de recuperación que contiene los archivos que desee. De forma predeterminada, el punto de recuperación más reciente ya está seleccionado.

5. Si desea descargar el software para copiar archivos del punto de recuperación, haga clic en **Descargar ejecutable** (para máquinas virtuales Windows de Azure) o en **Descargar script** (para máquinas virtuales Linux de Azure). Se genera un script de Python.

    ![Contraseña generada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure descarga el archivo ejecutable o el script en el equipo local.

    ![mensaje de descarga del archivo ejecutable o del script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para ejecutar el archivo ejecutable o el script como administrador, se recomienda guardar el archivo descargado en el equipo.

6. El archivo ejecutable o el script están protegido con contraseña y es obligatoria. En el menú **Recuperación de archivos**, haga clic en el botón de copia para cargar la contraseña en la memoria.

    ![Contraseña generada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Desde la ubicación de descarga (normalmente, la carpeta Descargas), haga clic con el botón derecho en el archivo ejecutable o el script y ejecútelo con las credenciales del administrador. Cuando se le solicite, escriba la contraseña o péguela de la memoria y presione Entrar. Una vez que se escriba la contraseña válida, el script se conecta al punto de recuperación.

    ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Si lo hace en un equipo con acceso restringido, asegúrese de que hay acceso a los siguientes recursos:

    - download.Microsoft.com
    - Direcciones URL de Recovery Services (el nombre geográfico hace referencia a la región en la que reside el almacén de Recovery Services)       - <https://pod01-rec2.geo-name.backup.windowsazure.com> (para zonas geográficas públicas Azure)       - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (para Azure China 21Vianet)       - <https://pod01-rec2.geo-name.backup.windowsazure.us> (para Azure Gobierno de EE. UU.)       - <https://pod01-rec2.geo-name.backup.windowsazure.de> (para Azure Alemania)
    - Puerto de salida 3260

> [!Note]
>
> - El nombre de archivo de script descargado tendrá el **geo-nombre** que se va a rellenar en la dirección URL. Por ejemplo, El nombre de script descargado empieza por \'VMname\'\_\'geoname\'_\'GUID\', como ContosoVM_wcus_12345678
> - La dirección URL sería <https://pod01-rec2.wcus.backup.windowsazure.com>"

   En el caso de Linux, el script requiere los componentes "open-iscsi" e "lshw" para conectar con el punto de recuperación. Si los componentes no existen en el equipo donde se ejecuta el script, este solicita permiso para instalarlos. Otorgue el consentimiento para instalar los componentes necesarios.

   El acceso a download.microsoft.com es necesario para descargar los componentes que se utilizan para crear un canal seguro entre la máquina donde se ejecuta el script y los datos en el punto de recuperación.

   Puede ejecutar el script en cualquier máquina que tenga el mismo sistema operativo (o uno compatible) que la máquina virtual de la que se realiza la copia de seguridad. Consulte la [tabla de sistemas operativos compatibles](backup-azure-restore-files-from-vm.md#system-requirements) para ver cuáles son. Si la máquina virtual de Azure protegida usa espacios de almacenamiento de Windows (para máquinas virtuales Windows de Azure) o matrices LVM/RAID (para máquinas virtuales Linux), no puede ejecutar el archivo ejecutable o script en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="identifying-volumes"></a>Identificación de volúmenes

#### <a name="for-windows"></a>Para Windows

Al ejecutar el archivo ejecutable, el sistema operativo monta los nuevos volúmenes y asigna letras de unidad. Puede usar el Explorador de Windows o el Explorador de archivos para buscar esas unidades. Las letras de unidad asignadas a los volúmenes no pueden ser las mismas que las de la máquina virtual original; sin embargo, se conserva el nombre del volumen. Por ejemplo, si el volumen de la máquina virtual original era "Data Disk (E:`\`)", ese volumen se puede conectar al equipo local como "Data Disk ('cualquier letra':`\`)". Busque en todos los volúmenes que se mencionan en la salida del script hasta que encuentre sus archivos o carpeta.  

   ![Menú Recuperación de archivos](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Para Linux

En Linux, los volúmenes del punto de recuperación se montan en la carpeta en que se ejecuta el script. Los discos conectados, los volúmenes y las rutas de acceso de montaje correspondientes se muestran según corresponda. Los usuarios con acceso en el nivel raíz pueden ver estas rutas de acceso de montaje. Examine los volúmenes mencionados en la salida del script.

  ![Menú Recuperación de archivos de Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Cierre de la conexión

Después de identificar los archivos y copiarlos en una ubicación de almacenamiento local, quite (o desmonte) las unidades adicionales. Para desmontar las unidades, en el menú **Recuperación de archivos** de Azure Portal, haga clic en **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Cuando los discos estén desmontados, recibirá un mensaje. Puede tardar unos minutos en actualizarse la conexión para que pueda quitar los discos.

En Linux, cuando se corta la conexión con el punto de recuperación, el sistema operativo no elimina las rutas de acceso de montaje correspondientes automáticamente. Las rutas de acceso de montaje adoptan la forma de volúmenes "huérfanos" y se pueden ver, pero se genera un error al acceder a los archivos o al escribir en ellos. Se pueden quitar manualmente. Cuando el script se ejecuta, este identifica todos los volúmenes existentes desde todos los puntos de recuperación anteriores y los limpia, aunque con consentimiento previo.

## <a name="special-configurations"></a>Configuraciones especiales

### <a name="dynamic-disks"></a>Discos dinámicos

Si la máquina virtual de Azure protegida tiene volúmenes con una o ambas de las siguientes características, no puede ejecutar el script ejecutable en la misma máquina virtual.

- Volúmenes que abarquen varios discos (volúmenes distribuidos y seccionados)
- Volúmenes que toleren errores (volúmenes reflejados y RAID-5) en discos dinámicos

En su lugar, ejecútelo en otro equipo que tenga un sistema operativo compatible.

### <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows

Los espacios de Windows Storage son una tecnología de Windows que permite virtualizar el almacenamiento. Con los espacios de Windows Storage puede agrupar discos estándar del sector en grupos de almacenamiento. Después, use el espacio disponible en los grupos de almacenamiento para crear discos virtuales, denominados "espacios de almacenamiento".

Si la máquina virtual de Azure usa los espacios de Windows Storage, no podrá ejecutar el script ejecutable en la misma máquina virtual. En su lugar, ejecútelo en otra máquina que tenga un sistema operativo compatible.

### <a name="lvmraid-arrays"></a>Matrices LVM/RAID

En Linux, las matrices Logical Volume Manager (LVM)/RAID del software se usan para administrar volúmenes lógicos en varios discos. En caso de que la máquina virtual Linux protegida use las matrices LVM o RAID, no podrá ejecutar el script en la misma máquina virtual. En su lugar, ejecútelo en cualquier otra máquina que tenga un sistema operativo compatible y que admita el sistema de archivos de la máquina virtual protegida.

En la salida del script siguiente se muestran los volúmenes y los discos de las matrices LVM o RAID con el tipo de partición.

   ![Menú de salida de LVM en Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Para poner en línea estas particiones, ejecute los comandos de las secciones siguientes.

#### <a name="for-lvm-partitions"></a>Para particiones de LVM

Para enumerar los nombres de grupo de volúmenes en un volumen físico.

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Para enumerar todos los volúmenes lógicos, los nombres y sus rutas de acceso en un grupo de volúmenes.

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command’s results>
```

Para montar los volúmenes lógicos en la ruta de acceso de su elección.

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Para matrices RAID

El siguiente comando muestra los detalles sobre todos los discos RAID.

```bash
#!/bin/bash
mdadm –detail –scan
```

 El disco RAID pertinente se muestra como `/dev/mdm/<RAID array name in the protected VM>`.

Use el comando Montar si el disco RAID tiene volúmenes físicos.

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Si el disco RAID tiene otra LVM configurada, utilice el procedimiento anterior para las particiones de LVM, pero con el nombre de volumen en lugar del nombre de disco RAID.

## <a name="system-requirements"></a>Requisitos del sistema

### <a name="for-windows-os"></a>Para sistemas operativos Windows

En la siguiente tabla se muestra la compatibilidad entre los sistemas operativos de servidor y equipo. Al recuperar archivos, no podrá restaurar archivos a una versión anterior o posterior del sistema operativo. Por ejemplo, no puede restaurar un archivo de una máquina virtual Windows Server 2016 a una Windows Server 2012 o un equipo con Windows 8. Puede restaurar archivos de una máquina virtual al mismo sistema operativo de servidor o a uno de cliente compatible.

|Sistema operativo de servidor | Sistema operativo de cliente compatible  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para SO Linux

En Linux, el sistema operativo del equipo usado para restaurar archivos debe admitir el sistema de archivos de la máquina virtual protegida. Al seleccionar un equipo para ejecutar el script, asegúrese de que este tiene un sistema operativo compatible y utiliza una de las versiones de la tabla siguiente:

|SO Linux | Versiones  |
| --------------- | ---- |
| Ubuntu | 12.04 y posterior |
| CentOS | 6.5 y posterior  |
| RHEL | 6.7 y posterior |
| Debian | 7 y posterior |
| Oracle Linux | 6.4 y posterior |
| SLES | 12 y posterior |
| openSUSE | 42.2 y posterior |

> [!Note]
> Hemos descubierto algunos problemas al ejecutar el script de recuperación de archivos en máquinas con sistema operativo de SLES 12 SP4. Investigación con el equipo SLES.
> Actualmente, la ejecución del script de recuperación de archivos funciona en máquinas con versiones de sistema operativo de SLES 12 SP2 y SP3.
>

El script también requiere los componentes Python y Bash para realizar la ejecución y la conexión al punto de recuperación con seguridad.

|Componente | Versión  |
| --------------- | ---- |
| Bash | 4 y posterior |
| Python | 2.6.6 y posterior  |
| TLS | Se debe admitir 1.2  |

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Recuperación de archivos de copias de seguridad de máquinas virtuales con discos de gran tamaño

En esta sección se explica cómo recuperar archivos a partir de copias de seguridad de máquinas virtuales de Azure cuyo número de discos es superior a 16 y cada tamaño de disco tiene más de 32 TB.

Dado que el proceso de recuperación de archivos asocia todos los discos de la copia de seguridad, cuando se usa un gran número de discos (más de 16) o discos de gran tamaño (más de 32 TB cada uno), se recomiendan los siguientes puntos de acción:

- Mantenga un servidor de restauración independiente (máquinas virtuales D2v3 de Azure VM) para la recuperación de archivos. Puede usar esa única recuperación de archivos y, a continuación, apagarla cuando no sea necesaria. No se recomienda realizar la restauración en el equipo original, ya que tendrá un impacto significativo en la propia máquina virtual.
- Después, ejecute el script una vez para comprobar si la operación de recuperación de archivos se realiza correctamente.
- Si el proceso de recuperación de archivos se bloquea (los discos no se montan nunca o se montan, pero no aparecen los volúmenes), realice los pasos siguientes.
  - Si el servidor de restauración es una máquina virtual Windows
    - Asegúrese de que el sistema operativo sea Windows Server 2012 o posterior.
    - Asegúrese de que las claves del Registro se establecen como se sugiere a continuación en el servidor de restauración y asegúrese de reiniciar el servidor. El número situado junto al GUID puede oscilar entre 0001 y 0005. En el ejemplo siguiente, es 0004. Navegue por la ruta de acceso de la clave del Registro hasta la sección de parámetros.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Si el servidor de restauración es una máquina virtual Linux
  - En el archivo /etc/iscsi/iscsid.conf, cambie la configuración de
    - node.conn[0].timeo.noop_out_timeout = 5 a node.conn[0].timeo.noop_out_timeout = 30
- Después de realizar lo siguiente, vuelva a ejecutar el script. Con estos cambios, es muy probable que la recuperación de archivos se realice correctamente.
- Cada vez que el usuario descarga un script, Azure Backup inicia el proceso de preparación del punto de recuperación para su descarga. Con discos de gran tamaño, se tardará un tiempo considerable. Si hay ráfagas sucesivas de solicitudes, la preparación de destino pasará a un espiral de descarga. Por lo tanto, se recomienda descargar un script desde el portal, PowerShell o la CLI, esperar 20-30 minutos (una heurística) y, a continuación, ejecutarlo. En este momento, el destino debería estar listo para conectarse desde el script.
- Después de la recuperación de archivos, asegúrese de volver al portal para hacer clic en "Desmontar discos" en los puntos de recuperación en los que no se pudieron montar los volúmenes. En esencia, este paso limpiará cualquier proceso o sesión y aumentará la posibilidad de recuperación.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas al tratar de recuperar archivos de las máquinas virtuales, compruebe la siguiente tabla para obtener más información.

| Mensaje de error y escenario | Causa probable | Acción recomendada |
| ------------------------ | -------------- | ------------------ |
| Salida del ejecutable: *excepción relacionada con el destino*. |El script no puede acceder al punto de recuperación.    | Compruebe si la máquina cumple los requisitos de acceso anteriores. |  
| Salida del ejecutable: *el destino ya ha iniciado sesión mediante una sesión iSCSI.* | El script ya se ejecutó en la misma máquina y se ha conectado la unidad. | Ya se han conectado los volúmenes del punto de recuperación. Es posible que NO se monten con las mismas letras de unidad de la máquina virtual original. Examine todos los volúmenes disponibles en el Explorador de archivos para buscar su archivo. |
| Salida del ejecutable: *este script no es válido porque se han desmontado los discos a través del portal o se ha superado el límite de 12 horas. Descargue un nuevo script del portal.* |    Se han desmontado los discos desde el portal o se ha superado el límite de 12 horas. | Este archivo ejecutable ahora no es válido y no se puede ejecutar. Si desea acceder a los archivos de esa recuperación en un momento dado, visite el portal para descargar un nuevo archivo ejecutable.|
| En el equipo donde se ejecuta el archivo ejecutable: los nuevos volúmenes no se desmontan después de hacer clic en el botón Desmontar. | El iniciador iSCSI de la máquina no responde ni actualiza su conexión con el destino ni mantiene la caché. |  Espere unos minutos tras hacer clic **Desmontar**. Si los nuevos volúmenes no se han desmontado, examínelos todos. Al examinar todos los volúmenes se obliga al iniciador a actualizar la conexión y el volumen se desmonta con un mensaje de error que indica que el disco no está disponible.|
| Salida del ejecutable: el script se ejecuta correctamente, pero no se muestra en la salida del script el mensaje que indica que se han conectado nuevos volúmenes. |    Se trata de un problema transitorio.    | Los volúmenes ya deberían estar conectados. Abra el Explorador para examinarlos. Si usa siempre la misma máquina para ejecutar scripts, considere la posibilidad de reiniciarla; debería mostrarse la lista en las ejecuciones posteriores del ejecutable. |
| Específico de Linux: no se pueden ver los volúmenes deseados. | El sistema operativo de la máquina en que se ejecuta el script puede no reconocer el sistema de archivos subyacente de la máquina virtual protegida. | Compruebe si el punto de recuperación es coherente frente a bloqueos o coherente con archivo. En caso de coherencia con archivo, ejecute el script en otra máquina cuyo sistema operativo reconozca el sistema de archivos de la máquina virtual protegida. |
| Específico de Windows: no se pueden ver los volúmenes deseados. | Se han adjuntado los discos, pero no se han configurado los volúmenes | En la pantalla de administración de discos, identifique los discos adicionales relacionados con el punto de recuperación. Si cualquiera de estos discos está sin conexión, intente ponerlo en línea haciendo clic con el botón derecho en él y, luego, haciendo clic en "En línea"|

## <a name="security"></a>Seguridad

En esta sección se habla de las diversas medidas de seguridad tomadas para la implementación de recuperación de archivos de copias de seguridad de máquinas virtuales de Azure para que los usuarios tengan en cuenta los aspectos de seguridad de la característica.

### <a name="feature-flow"></a>Flujo de característica

Esta característica se ha creado para tener acceso a los datos de la máquina virtual sin la necesidad de restaurar toda la máquina virtual o discos de máquina virtual y en pocos pasos. Un script (que monta el volumen de recuperación durante la ejecución como se muestra a continuación) proporciona acceso a los datos de la base de datos y, por tanto, constituye la piedra angular de todas las implementaciones de seguridad.

  ![Flujo de característica de seguridad](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementaciones de seguridad

#### <a name="select-recovery-point-who-can-generate-script"></a>Selección del punto de recuperación (quién puede generar el script)

El script proporciona acceso a los datos de la máquina virtual y es importante regular quién puede generarlo primero. Es necesario iniciar sesión en Azure Portal y contar con la [autorización de RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) para poder generar el script.

La recuperación de archivos necesita el mismo nivel de autorización según sea necesario para la restauración de máquina virtual y de discos. En otras palabras, solo los usuarios autorizados que pueden ver los datos de la máquina virtual, pueden generar el script.

El script generado se firma con el certificado oficial de Microsoft para el servicio Azure Backup. Cualquier manipulación del script supondrá la inhabilitación de la firma y cualquier intento de ejecutarlo se resaltará como riesgo potencial del sistema operativo.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montaje del volumen de recuperación (quién puede ejecutar el script)

Solo un administrador puede ejecutar el script y debe hacerlo en modo elevado. El script solo ejecuta un conjunto de pasos generado previamente y no acepta la entrada desde ningún origen externo.

Para ejecutar el script, se requiere una contraseña que solo puede ver el usuario autorizado en el momento de generarse el script en Azure Portal, o bien en PowerShell o la CLI. De esta forma, se garantiza que el usuario autorizado que descarga el script sea también responsable de la ejecución del mismo.

#### <a name="browse-files-and-folders"></a>Examen de archivos y carpetas

Para examinar los archivos y carpetas, el script usa el iniciador iSCSI de la máquina y se conecta al punto de recuperación configurado como destino iSCSI. Aquí es posible suponer escenarios en los que se intenta imitar o suplantar todos los componentes o cualquiera de ellos.

Usamos el mecanismo de autenticación CHAP para que los componentes se autentiquen entre sí. Esto significa que un iniciador falso lo tiene muy difícil para conectarse al destino iSCSI, mientras que un destino falso lo tiene muy difícil para conectarse a la máquina donde se ejecuta el script.

El flujo de datos entre el servicio de recuperación y la máquina se protege mediante la creación de un túnel SSH seguro a través de TCP ([TLS 1.2 se debe admitir](#system-requirements) en la máquina donde se ejecuta el script).

Cualquier lista de control de acceso (ACL) a archivos presente en la máquina virtual principal o de la que se ha hecho una copia de seguridad se conserva también en el sistema de archivos montado.

El script proporciona acceso de solo lectura a un punto de recuperación y solo es válido durante 12 horas. Si el usuario desea quitar el acceso antes, inicie sesión en Azure Portal, PowerShell o CLI y realice el **desmontaje de discos** para ese punto de recuperación concreto. El script se invalidará inmediatamente.
