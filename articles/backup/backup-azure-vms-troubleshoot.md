---
title: Solución de errores de copia de seguridad con máquinas virtuales de Azure
description: Solución de problemas de copia de seguridad y restauración de máquinas virtuales de Azure
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 1b3d02d5cfdae2f196f2f35f075dd8c250b5ece1
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860333"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Solución de errores de copia de seguridad en las máquinas virtuales de Azure

Puede solucionar los errores detectados al usar Azure Backup Server con la información que aparece a continuación:

## <a name="backup"></a>Copia de seguridad

En esta sección se trata el error en la operación de copia de seguridad de la máquina virtual de Azure.

### <a name="basic-troubleshooting"></a>Pasos básicos para solucionar problemas

* Asegúrese de que el agente de máquina virtual (agente de WA) es la [versión más reciente](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Asegúrese de que la versión del sistema operativo de la máquina virtual Windows o Linux, consulte [Matriz de compatibilidad para copias de seguridad de máquinas virtuales IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Compruebe que no hay otro servicio de copia de seguridad en ejecución.
   * Para asegurarse de que no hay problemas con las extensiones de instantáneas, [desinstale las extensiones para forzar la recarga y vuelva a intentar la copia de seguridad](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load).
* Compruebe que la máquina virtual tiene conectividad a Internet.
   * Asegúrese de que no hay otro servicio de copia de seguridad en ejecución.
* En `Services.msc`, asegúrese de que el estado del servicio **Microsoft Azure Guest Agent** es **En ejecución**. Si falta el servicio **Windows Azure Guest Agent**, instálelo desde [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* El **registro de eventos** puede mostrar errores procedentes de otros productos de copia de seguridad, como por ejemplo Copias de seguridad de Windows Server, que no se deben a Azure Backup. Siga estos pasos para determinar si el problema tiene que ver con Azure Backup:
   * Si hay algún error en una entrada **Backup** (Copia de seguridad) en el origen del evento o en el mensaje, compruebe si las copias de seguridad de la máquina virtual IaaS de Azure se realizaron correctamente y si se creó un punto de restauración con el tipo de instantánea deseado.
    * Si Azure Backup funciona, es probable que el problema lo produzca otra solución de copia de seguridad.
    * Este es un ejemplo de un error del visor de eventos en el que Azure Backup funcionaba correctamente, pero se produjo un error en "Copias de seguridad de Windows Server":<br>
    ![Error de Copias de seguridad de Windows Server](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
    * Si Azure Backup no funciona correctamente, busque el código de error correspondiente en la sección Errores comunes de la copia de seguridad de VM de este artículo.

## <a name="common-issues"></a>Problemas comunes

A continuación encontrará problemas habituales que generan errores en las copias de seguridad de las máquinas virtuales de Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime: Se agotó el tiempo de espera para copiar los datos de copia de seguridad del almacén

Código de error: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Mensaje de error: Se agotó el tiempo de espera para copiar los datos de copia de seguridad del almacén.

Esto puede deberse a errores transitorios de almacenamiento o a IOPS insuficientes en la cuenta de almacenamiento para que el servicio de copia de seguridad pueda transferir datos al almacén dentro del período del tiempo de expiración. Configure la copia de seguridad de VM mediante estos [procedimientos recomendados](backup-azure-vms-introduction.md#best-practices) y vuelva a intentar la operación de copia de seguridad.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState: El estado de la máquina virtual no permite realizar copias de seguridad

Código de error: UserErrorVmNotInDesirableState <br/>
Mensaje de error: El estado de la máquina virtual no permite realizar copias de seguridad.<br/>

Se produjo un error en la operación de copia de seguridad porque la VM se encuentra en un estado Incorrecto. Para que la copia de seguridad se realice correctamente, el estado de la máquina virtual debe ser En ejecución, Detenido o Detenido (desasignado).

* Si la máquina está en un estado transitorio entre **En ejecución** y **Apagar**, espere a que cambie el estado. A continuación, desencadenar el trabajo de copia de seguridad.
* Si se trata de una VM de Linux y utiliza el módulo de kernel Security-Enhanced Linux, deberá excluir la ruta del agente de Linux de Azure ( **/var/lib/waagent**) de la directiva de seguridad y asegurarse de que la extensión de Backup está instalada.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed: La copia de seguridad no pudo inmovilizar uno o varios puntos de montaje de la máquina virtual para tomar una instantánea coherente del sistema de archivos

Código de error: UserErrorFsFreezeFailed <br/>
Mensaje de error: No se pudieron inmovilizar uno o varios puntos de montaje de la máquina virtual para tomar una instantánea coherente con el sistema de archivos.

* Compruebe el estado del sistema de archivos de todos los dispositivos montados mediante el comando **tune2fs** , por ejemplo **tune2fs- l/dev/sdb1\\** .\| grep **filesystem State**.
* Desmonte los dispositivos cuyo estado del sistema de archivos no estuviera limpio; para ello, use el comando **umount**.
* Ejecute una comprobación de coherencia del sistema de archivos en estos dispositivos mediante el comando **fsck**.
* Vuelva a montar los dispositivos e intente realizar de nuevo la operación de copia de seguridad.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC: La instalación de la extensión o la operación no se realizaron correctamente debido a un error de COM+

Código de error: ExtensionSnapshotFailedCOM <br/>
Mensaje de error: Error en la operación de instantánea debido a error de COM+

Código de error: ExtensionInstallationFailedCOM  <br/>
Mensaje de error: La instalación de la extensión o la operación no se realizaron correctamente debido a un error de COM+

Código de error: ExtensionInstallationFailedMDTC <br/>
Mensaje de error: Error de instalación de la extensión "COM+ no pudo realizar la conexión con MS DTC (Microsoft Distributed Transaction Coordinator)" <br/>

La operación de copia de seguridad no se pudo realizar debido a un problema con el servicio de Windows **Aplicación del sistema COM+** .  Para resolver el problema, siga estos pasos:

* Pruebe a iniciar/reiniciar el servicio de Windows **Aplicación del sistema COM+** (desde un símbolo del sistema con privilegios elevados: **net start COMSysApp**).
* Asegúrese de que el servicio **Coordinador de transacciones distribuidas** se esté ejecutando como una cuenta de **servicio de red**. Si no es así, cámbielos para que se ejecuten como una cuenta de **servicio de red** y reinicie **Aplicación del sistema COM+** .
* Si no se puede reiniciar el servicio, reinstale el servicio **Coordinador de transacciones distribuidas** siguiendo los pasos siguientes:
    * Detenga el servicio MSDTC.
    * Abra el símbolo del sistema (cmd).
    * Ejecute el comando "msdtc-uninstall".
    * Ejecute el comando "msdtc-install".
    * Inicie el servicio MSDTC.
* Inicie el servicio de Windows **Aplicación del sistema COM+** . Una vez que se inicie **Aplicación del sistema COM+** , desencadene un trabajo de copia de seguridad desde Azure Portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState: Error en la operación de instantánea porque los VSS Writers estaban en un estado incorrecto

Código de error: ExtensionFailedVssWriterInBadState <br/>
Mensaje de error: Error en la operación de instantánea porque los VSS Writers estaban en un estado incorrecto

Reinicie los VSS Writers que se encuentran en estado incorrecto. En un símbolo del sistema con privilegios elevados, ejecute ```vssadmin list writers```. La salida contiene todos los VSS Writers y su estado. Con cada VSS Writer cuyo estado no sea **[1] Estable**, para reiniciarlo, ejecute los comandos siguientes desde un símbolo del sistema con privilegios elevados:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure: Error al analizar la configuración de la extensión de copia de seguridad

Código de error: ExtensionConfigParsingFailure<br/>
Mensaje de error: Error al analizar la configuración de la extensión de copia de seguridad.

Este error sucede debido a que hay permisos modificados en el directorio **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Ejecute el comando siguiente y compruebe que los permisos en el directorio **MachineKeys** sean los predeterminados: **icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Los permisos predeterminados son:
* Todos: (R,W)
* BUILTIN\Administrators: (F)

Si ve permisos en el directorio **MachineKeys** distintos de los predeterminados, siga estos pasos para corregir los permisos, eliminar el certificado y desencadenar la copia de seguridad:

1. Corrija los permisos en el directorio **MachineKeys**. Con las propiedades de seguridad de Explorer y la configuración de seguridad avanzada en el directorio, restablezca los valores predeterminados de los permisos. Quite todos los objetos de usuario, excepto el valor predeterminado del directorio, y asegúrese de que el permiso **Todos** tenga acceso especial para:

    * Enumerar carpetas y leer datos
    * Leer atributos
    * Leer atributos ampliados
    * Crear archivos y escribir datos
    * Crear carpetas y anexar datos
    * Escribir atributos
    * Escribir atributos ampliados
    * Permisos de lectura
2. Elimine todos los certificados donde **Emitido para** sea el modelo de implementación clásica o bien el **Generador de certificados CRP de Microsoft Azure**:
    * [Abra los certificados en una consola del equipo local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * En **Personal** > **Certificados**, elimine todos los certificados donde **Emitido para** sea el modelo de implementación clásico, o **Generador de certificados CRP de Microsoft Azure**.
3. Desencadene un trabajo de copia de seguridad de VM.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState: El estado de la extensión no admite la operación de copia de seguridad

Código de error: ExtensionStuckInDeletionState <br/>
Mensaje de error: El estado de la extensión no admite la operación de copia de seguridad

La operación de copia de seguridad no se pudo realizar debido a un estado incoherente de la extensión de copia de seguridad. Para resolver el problema, siga estos pasos:

* Asegúrese de que el agente de invitado está instalado y tiene capacidad de respuesta.
* En Azure Portal, vaya a **Máquina virtual** > **Toda la configuración** > **Extensiones**.
* Seleccione la extensión de copia de seguridad VmSnapshot o VmSnapshotLinux y haga clic en **Desinstalar**.
* Después de eliminar la extensión de copia de seguridad, vuelva a intentar la operación de copia de seguridad.
* La operación de copia de seguridad posterior instalará la nueva extensión con el estado deseado.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError: No se pudo realizar la operación de instantánea porque se excedió el límite de instantáneas para algunos de los discos asociados

Código de error: ExtensionFailedSnapshotLimitReachedError  <br/>
Mensaje de error: No se pudo realizar la operación de instantánea porque se excedió el límite de instantáneas para algunos de los discos asociados

No se pudo realizar la operación de instantánea porque se excedió el límite de instantáneas para algunos de los discos asociados. Siga los pasos de solución de problemas siguientes y luego vuelva a intentar la operación.

* Elimine las instantáneas de blobs de disco que no sean necesarias. Tenga cuidado de no eliminar el blob de disco. Solo se deben eliminar los blobs de instantánea.
* Si está habilitada la eliminación temporal en cuentas de almacenamiento de disco de VM, configure la retención de eliminación temporal de manera que las instantáneas existentes sean inferiores a las máximas permitidas en cualquier momento.
* Si Azure Site Recovery está habilitado en la VM de la que se ha realizado la copia de seguridad, realice el siguiente procedimiento:

    * Asegúrese de que el valor de **isanysnapshotfailed** esté establecido como False en /etc/azure/vmbackup.conf.
    * Programe Azure Site Recovery en una hora distinta, de manera que no entre en conflicto con la operación de copia de seguridad.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive: Error en la operación de instantánea debido a recursos de VM no adecuados

Código de error: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Mensaje de error: Error en la operación de instantánea debido a recursos de VM no adecuados

Error de operación de copia de seguridad de la VM debido a un retraso en las llamadas de red al realizar la operación de captura de instantánea. Para resolver este problema, realice el paso 1. Si el problema persiste, intente los pasos 2 y 3.

**Paso 1**: Creación de una instantánea con permisos de host

En el símbolo del sistema de administrador con permisos elevados, ejecute el comando siguiente:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Esto garantizará que las instantáneas se realizan con permisos de host en lugar de invitado. Vuelva a intentar la operación de copia de seguridad.

**Paso 2**: Pruebe a cambiar la programación de la copia de seguridad a un momento en que la VM esté bajo una carga menor (menos CPU, E/S por segundo, etc.).

**Paso 3**: Pruebe a [aumentar el tamaño de la VM](https://azure.microsoft.com/blog/resize-virtual-machines/) y reintente la operación.

## <a name="common-vm-backup-errors"></a>Errores comunes de la copia de seguridad de VM

| Detalles del error | Solución alternativa |
| ------ | --- |
| **Código de error**: 320001<br/> **Mensaje de error**: No se pudo realizar la operación porque la máquina virtual ya no existe. <br/> <br/> **Código de error**: 400094 <br/> **Mensaje de error**: No existe la máquina virtual <br/> <br/>  No se encontró una máquina virtual de Azure.  |Este error sucede cuando se elimina la máquina virtual principal, pero la directiva de copia de seguridad continúa buscando una máquina virtual para realizar la copia de seguridad. Para corregir este error, siga estos pasos: <ol><li> Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos, **nombre del servicio en la nube**,<br>**or**</li><li> Deje de proteger la máquina virtual eliminando o sin eliminar los datos de la copia de seguridad. Para más información, consulte [Detener la protección de máquinas virtuales](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| La máquina virtual está en un estado de aprovisionamiento incorrecto: <br>Reinicie la máquina y asegúrese de que se esté ejecutando o esté apagada. | Este error ocurre cuando uno de los errores de extensión pone a la máquina virtual en un estado de aprovisionamiento erróneo. Vaya a la lista de extensiones y compruebe si hay una extensión errónea, quítela e intente reiniciar la máquina virtual. Si todas las extensiones están en estado de ejecución, compruebe si el servicio del agente de VM está en ejecución. Si no es así, reinicie el servicio del agente de VM. |
|**Código de error**: UserErrorBCMPremiumStorageQuotaError<br/> **Mensaje de error**: No se pudo copiar la instantánea de la máquina virtual debido a que no había espacio suficiente disponible en la cuenta de almacenamiento | En el caso de máquinas virtuales Prémium de la versión 1 de la pila de copia de seguridad de máquinas virtuales, la instantánea se copia a la cuenta de almacenamiento. Este pase sirve para garantizar que el tráfico de administración de copias de seguridad, que trabaja en la instantánea, no limite el número de IOPS disponibles para la aplicación con discos Prémium. <br><br>Se recomienda asignar solo un 50 por ciento, 17,5 TB, del espacio total de la cuenta de almacenamiento. El servicio de Azure Backup puede copiar la instantánea a la cuenta de almacenamiento y transferir datos desde la ubicación copiada en la cuenta de almacenamiento al almacén. |
| **Código de error: 380008** <br/> **Mensaje de error**: Error al instalar la extensión de servicios de recuperación de Microsoft como máquina virtual no se está ejecutando | Se requiere tener el agente de VM para instalar la extensión de Azure Recovery Services. Instale el agente de máquina virtual de Azure y reinicie la operación de registro. <br> <ol> <li>Compruebe si el agente de máquina virtual se ha instalado correctamente. <li>Asegúrese de que la marca de la configuración de la máquina virtual se haya establecido correctamente.</ol> Obtenga más información acerca de la instalación del agente de máquina virtual y de cómo validar dicha instalación. |
| **Código de error**: ExtensionSnapshotBitlockerError <br/> **Mensaje de error**: Error en la operación de instantánea con el error de operación del Servicio de instantáneas de volumen **El Cifrado de unidad BitLocker está bloqueando esta unidad. Esta unidad se debe desbloquear en el Panel de control.** |Desactive BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema de VSS. |
| **Código de error**: VmNotInDesirableState <br/> **Mensaje de error**:  El estado de la máquina virtual no permite realizar copias de seguridad. |<ul><li>Si la máquina está en un estado transitorio entre **En ejecución** y **Apagar**, espere a que cambie el estado. A continuación, desencadenar el trabajo de copia de seguridad. <li> Si se trata de una VM de Linux y utiliza el módulo de kernel Security-Enhanced Linux, deberá excluir la ruta del agente de Linux de Azure ( **/var/lib/waagent**) de la directiva de seguridad y asegurarse de que la extensión de Backup está instalada.  |
| El agente de máquina virtual no está en la máquina virtual: <br>Instale los requisitos previos y el agente de máquina virtual. A continuación, reinicie la operación. |Obtenga más información acerca de la [instalación del agente de máquina virtual y de cómo validarla](#vm-agent). |
| **Código de error**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Mensaje de error**: Error en la operación de instantánea debido a un error en la creación de un canal de comunicación de red segura. | <ol><li> Abra el Editor del Registro; para ello, ejecute **regedit.exe** en modo elevado. <li> Identifique todas las versiones de .NET Framework presentes en el sistema. Se encuentran en la jerarquía de la clave del Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Para cada versión de .NET Framework presente en la clave del Registro, agregue la siguiente clave: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Código de error**: ExtensionVCRedistInstallationFailure <br/> **Mensaje de error**: Error en la operación de instantánea debido a un error en la instalación de Visual C++ Redistributable para Visual Studio 2012. | Vaya a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e instale vcredist2013_x64.<br/>Asegúrese de que el valor de la clave del Registro que permite la instalación del servicio se establezca correctamente. Es decir, establezca el valor **Start** de **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** en **3** y no en **4**. <br><br>Si todavía experimenta problemas con la instalación, reinicie el servicio de instalación; para ello, ejecute **MSIEXEC /UNREGISTER** seguido de **MSIEXEC /REGISTER** desde un símbolo del sistema con privilegios elevados.  |


## <a name="jobs"></a>Trabajos

| Detalles del error | Solución alternativa |
| --- | --- |
| No se admite la cancelación para este tipo de trabajo: <br>espere hasta que el trabajo finalice. |None |
| El trabajo no se encuentra en un estado cancelable: <br>espere hasta que el trabajo finalice. <br>**or**<br> El trabajo seleccionado no se encuentra en un estado cancelable: <br>espere a que el trabajo finalice. |Es probable que el trabajo esté casi terminado. Espere hasta que el trabajo finalice.|
| La copia de seguridad no puede cancelar el trabajo porque no está en curso: <br>solo se admite la cancelación en los trabajos en curso. Intente cancelar un trabajo en curso. |Este error se produce debido a un estado transitorio. Espere un momento y reintente la operación de cancelación. |
| La copia de seguridad no pudo cancelar el trabajo: <br>espere hasta que el trabajo finalice. |None |

## <a name="restore"></a>Restauración

| Detalles del error | Solución alternativa |
| --- | --- |
| Error interno de nube en la restauración. |<ol><li>El servicio de nube que está intentando restaurar está configurado con las opciones de DNS. Puede consultar: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Si la **dirección** está configurada, los valores de DNS están establecidos.<br> <li>El servicio en la nube con el que está tratando de restaurar está configurado con la **IP reservada** y las máquinas virtuales del servicio en la nube están detenidas. Puede comprobar que un servicio en la nube ha reservado una dirección IP mediante los siguientes cmdlets de PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Está intentando restaurar una máquina virtual con las siguientes configuraciones de red especiales en el mismo servicio en la nube: <ul><li>Máquinas virtuales con la configuración del equilibrador de carga interna y externa.<li>Máquinas virtuales con varias direcciones IP reservadas. <li>Máquinas virtuales con varias NIC. </ul><li>Seleccione un nuevo servicio en la nube en la interfaz de usuario o consulte las [consideraciones de restauración](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) de las máquinas virtuales con las configuraciones de red especiales.</ol> |
| El nombre DNS seleccionado ya se ha utilizado: <br>Especifique un nombre DNS diferente e inténtelo de nuevo. |El nombre DNS hace referencia al nombre del servicio en la nube, normalmente terminado con **.cloudapp.net**. Este nombre debe ser único. Si se produce este error, deberá elegir otro nombre para la máquina virtual durante la restauración. <br><br> Este error solo se muestra a los usuarios de Azure Portal. La operación de restauración mediante PowerShell se realizará correctamente porque solo restaura los discos y no crea la máquina virtual. El error aparecerá cuando el usuario crea explícitamente la máquina virtual después de la operación de restauración del disco. |
| La configuración de la red virtual especificada no es correcta: <br>Especifique una configuración de red virtual diferente e inténtelo de nuevo. |None |
| El servicio en la nube especificado usa una dirección IP reservada que no coincide con la configuración de la máquina virtual que se va a restaurar: <br>especifique un servicio en la nube diferente que no esté usando una dirección IP reservada. O elija otro punto de recuperación desde el que restaurar. |None |
| El servicio en la nube ha alcanzado su límite en el número de puntos de conexión de entrada: <br>vuelva a intentar la operación. Para ello, especifique un servicio en la nube diferente o use un punto de conexión existente. |None |
| La cuenta de almacenamiento de destino y el almacén de Recovery Services se encuentran en dos regiones diferentes: <br>asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración está en la misma región de Azure que el almacén de Recovery Services. |None |
| No se admite la cuenta de almacenamiento especificada para la operación de restauración: <br>solo se admiten cuentas de almacenamiento básico o estándar con la configuración de replicación con redundancia local o con redundancia geográfica. Seleccione una cuenta de almacenamiento compatible. |None |
| El tipo de cuenta de almacenamiento especificada para la operación de restauración no está en línea: <br>asegúrese de que el tipo de cuenta de almacenamiento especificada para la operación de restauración esté en línea. |Este error puede suceder debido a un error transitorio en Azure Storage o a una interrupción. Elija otra cuenta de almacenamiento. |
| Se ha alcanzado la cuota del grupo de recursos: <br>elimine algunos grupos de recursos desde Azure Portal o póngase en contacto con el servicio de soporte técnico de Azure para aumentar los límites. |None |
| La subred seleccionada no existe: <br>seleccione una que exista. |None |
| El servicio Backup no tiene autorización para acceder a los recursos de su suscripción. |Para resolver este error, primero debe restaurar discos mediante los pasos que se indican en [Restauración de los discos de copia de seguridad](backup-azure-arm-restore-vms.md#restore-disks). A continuación, siga los pasos de PowerShell que se indican en [Creación de una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Tiempo excesivo en operaciones de copia de seguridad y restauración
Si la copia de seguridad tarda más de 12 horas o la restauración tarda más de 6 horas, revise los [procedimientos recomendados](backup-azure-vms-introduction.md#best-practices) y las [consideraciones de rendimiento](backup-azure-vms-introduction.md#backup-performance).

## <a name="vm-agent"></a>Agente de máquina virtual
### <a name="set-up-the-vm-agent"></a>Configuración del agente de la máquina virtual
Normalmente, el agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tendrán instalado el agente de máquina virtual. Para dichas máquinas virtuales, el agente de máquina virtual debe instalarse explícitamente.

#### <a name="windows-vms"></a>Máquinas virtuales Windows

* Descargue e instale el [MSI del agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Para completar la instalación, necesita privilegios de administrador.
* En el caso de las máquinas virtuales creadas con el modelo de implementación clásica, [actualice la propiedad de la máquina virtual](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. Este paso no es necesario para las máquinas virtuales de Azure Resource Manager.

#### <a name="linux-vms"></a>Máquinas virtuales con Linux

* Instale la versión más reciente del agente desde el repositorio de distribución. Para obtener más información sobre el nombre del paquete, consulte el [repositorio del agente de Linux](https://github.com/Azure/WALinuxAgent).
* En el caso de las máquinas virtuales creadas con el modelo de implementación clásica, [use este blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para actualizar la propiedad de la máquina virtual y comprobar que el agente está instalado. Este paso no es necesario para las máquinas virtuales de Resource Manager.

### <a name="update-the-vm-agent"></a>Actualización del agente de máquina virtual
#### <a name="windows-vms"></a>Máquinas virtuales Windows

* Para actualizar el agente de VM, vuelva a instalar los [archivos binarios del agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Antes de actualizar al agente, asegúrese de que no ocurra ninguna operación de copia de seguridad durante la actualización del agente de VM.

#### <a name="linux-vms"></a>Máquinas virtuales con Linux

* Para actualizar el agente de máquina virtual Linux, siga las instrucciones del artículo [Actualización del agente Linux de Azure en una máquina virtual](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Use siempre el repositorio de distribución para actualizar el agente.

    No descargue el código del agente de GitHub. Si el agente más reciente no está disponible para su distribución, póngase en contacto con el servicio de soporte técnico para obtener instrucciones sobre cómo adquirir el agente más reciente. También puede comprobar la información del [agente Linux de Windows Azure](https://github.com/Azure/WALinuxAgent/releases) más reciente en el repositorio de GitHub.

### <a name="validate-vm-agent-installation"></a>Validación de la instalación del agente de máquina virtual

Para comprobar la versión del agente de VM en Windows VM:

1. Inicie sesión en la máquina virtual de Azure y vaya a la carpeta **C:\WindowsAzure\Packages**. Debe encontrar el archivo **WaAppAgent.exe**.
2. Haga clic con el botón derecho en el archivo y vaya a **Propiedades**. A continuación, seleccione la pestaña **Detalles**. En el campo **Versión del producto**, debe aparecer el valor 2.6.1198.718 o uno superior.

## <a name="troubleshoot-vm-snapshot-issues"></a>Solución de problemas de instantáneas de máquina virtual
La copia de seguridad de máquinas virtuales se basa en la emisión de comandos de instantánea para el almacenamiento subyacente. No tener acceso al almacenamiento o los retrasos en la ejecución de las tarea de instantáneas puede generar un error en el trabajo de copia de seguridad. Las siguientes condiciones pueden producir un error en la tarea de instantáneas.

- **Se bloquea el acceso de red a Storage mediante NSG**. Obtenga más información sobre cómo [establecer el acceso de red](backup-azure-arm-vms-prepare.md#establish-network-connectivity) a Storage mediante listas permitidas de direcciones IP o con un servidor proxy.
- **Las máquinas virtuales con copia de seguridad de SQL Server configurada pueden provocar un retraso de la tarea de instantánea**. De forma predeterminada, la copia de seguridad de VM crea una copia de seguridad completa de VSS en VM Windows. Las máquinas virtuales que ejecutan SQL Server y tienen configurada la copia de seguridad de SQL Server pueden experimentar retrasos en las instantáneas. Si los retrasos en las instantáneas provocan errores de copia de seguridad, establezca la siguiente clave del Registro:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **El estado de la máquina virtual se notifica incorrectamente porque la máquina virtual está apagada en RDP**. Si ha usado el escritorio remoto para apagar la máquina virtual, compruebe que el estado de la máquina en el portal sea correcto. Si el estado no es correcto, utilice la opción **Apagar** en el panel de VM del portal para apagar la VM.
- **Si más de cuatro VM comparten el mismo servicio en la nube, divídalas en varias directivas de copia de seguridad**. Escalone las horas de copia de seguridad para que no se inicien más de cuatro de máquinas virtuales al mismo tiempo. Pruebe a separar las horas de inicio en las directivas al menos en una hora.
- **La ejecución de la máquina virtual utiliza mucho la CPU o la memoria**. Si la máquina virtual se ejecuta con un uso de memoria o CPU altos, de más del 90 %, la tarea de instantáneas se pone en cola y se retrasa. Finalmente, el tiempo de espera se agota. Si sucede esto, intente realizar una copia de seguridad a petición.

## <a name="networking"></a>Redes
Al igual que todas las extensiones, las de Backup necesitan acceso a Internet pública para funcionar. Si no tiene acceso público a Internet, se pueden producir estos problemas:

* Se puede producir un error en la instalación de la extensión.
* Se puede producir un error en las operaciones de Backup, por ejemplo, al realizar la instantánea de disco.
* También al mostrar el estado de la operación de copia de seguridad.

La necesidad de resolver las direcciones públicas de Internet se trata en [esta entrada del blog del servicio de soporte técnico de Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Compruebe las configuraciones de DNS para la red virtual y asegúrese de que los URI de Azure se puedan resolver.

Una vez que la resolución de nombres se haya realizado correctamente, también hay que proporcionar acceso a las direcciones IP de Azure. Para desbloquear el acceso a la infraestructura de Azure, siga uno de estos pasos:

- Permita una lista de intervalos IP del centro de datos de Azure:
   1. Obtenga la lista de [IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) que van a formar parte de la lista de permitidos.
   1. Desbloquee las direcciones IP mediante el cmdlet [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute). Ejecute este cmdlet en la máquina virtual de Azure, en una ventana de PowerShell con privilegios elevados. Realice la ejecución como administrador.
   1. Si dispone de un grupo de seguridad de red, agréguele reglas para permitir el acceso a las direcciones IP.
- Cree una ruta de acceso para el flujo del tráfico HTTP:
   1. Si tiene alguna restricción de red implementada, implemente un servidor proxy HTTP para enrutar el tráfico. Un ejemplo es un grupo de seguridad de red. Consulte los pasos para implementar un servidor proxy HTTP en [Establecimiento de la conectividad de red](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Si dispone de un grupo de seguridad de red, agréguele reglas para permitir el acceso a Internet desde el proxy HTTP.

> [!NOTE]
> DHCP debe estar habilitado dentro del invitado para que la copia de seguridad de máquinas virtuales de IaaS funcione. Si necesita una dirección IP privada estática, configúrela mediante Azure Portal o PowerShell. Asegúrese de que la opción DHCP dentro de la máquina virtual esté habilitada.
> Obtenga más información sobre cómo configurar una dirección IP estática con PowerShell:
> - [Incorporación de una dirección IP interna estática a una máquina virtual existente](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Cambio del método de asignación para una dirección IP privada asignada a una interfaz de red](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
