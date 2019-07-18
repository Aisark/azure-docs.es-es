---
title: Solución de problemas del agente de Azure Backup
description: Solución de problemas de instalación y registro del agente de Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434005"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Solución de problemas del agente de Microsoft Azure Recovery Services (MARS)

En este artículo se explica cómo resolver errores que pueden aparecer durante operaciones de configuración, registro, copia de seguridad y restauración.

## <a name="basic-troubleshooting"></a>Pasos básicos para solucionar problemas

Se recomienda confirmar lo siguiente antes de empezar a solucionar problemas del agente de Microsoft Azure Recovery Services (MARS):

- [Asegúrese de que el agente de MARS está actualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Asegúrese de que hay conectividad de red entre el agente de MARS y Azure](https://aka.ms/AB-A4dp50).
- Asegúrese de que MARS se está ejecutando (en la consola de servicio). Si lo precisa, reinicie y vuelva a intentar la operación.
- [Asegúrese de que hay disponible entre un 5 % y un 10 % de espacio de volumen en la ubicación de la carpeta temporal](https://aka.ms/AB-AA4dwtt).
- [Compruebe si otro proceso o software antivirus interfiere con Azure Backup](https://aka.ms/AB-AA4dwtk).
- Si la copia de seguridad programada genera errores, pero no la copia de seguridad manual, vea [Las copias de seguridad no se ejecutan según la programación](https://aka.ms/ScheduledBackupFailManualWorks).
- Asegúrese de que el sistema operativo tiene las actualizaciones más recientes.
- [Asegúrese de excluir de la copia de seguridad las unidades no compatibles y los archivos con atributos no compatibles](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Asegúrese de que el reloj del sistema protegido está configurado con la zona horaria correcta.
- [Asegúrese de que .NET Framework 4.5.2 o posterior está instalado en el servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Si está intentando volver a registrar el servidor en un almacén:
  - Asegúrese de que el agente no está instalado en el servidor y de que se ha eliminado de Portal.
  - Utilice la misma frase de contraseña que se usó inicialmente para registrar el servidor.
- Antes de iniciar una copia de seguridad sin conexión, asegúrese de que Azure PowerShell 3.7.0 está instalado tanto en el equipo de origen como en el equipo donde se realizará la copia.
- Si el agente de Backup se ejecuta en una máquina virtual de Azure, vea [este artículo](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Se han proporcionado credenciales de almacén no válidas

**Mensaje de error**: Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. (Id.: 34513)

| Causa | Acciones recomendadas |
| ---     | ---    |
| **Las credenciales de almacén no son válidas** <br/> <br/> Los archivos de credenciales de almacén podrían estar dañados o es posible que hayan expirado (por ejemplo, podrían haberse descargado más de 48 horas antes del momento del registro).| Descargue nuevas credenciales del almacén de Recovery Services en Azure Portal (vea el paso 6 de la sección [Descargar el agente de MARS](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)). Luego, realice estos pasos, según corresponda: <ul><li> Si ya ha instalado y registrado MARS, abra la consola MMC del agente de Microsoft Azure Backup y seleccione **Registrar servidor** en el panel **Acciones** para completar el registro con las nuevas credenciales. <br/> <li> Si se produce un error en la nueva instalación, intente realizarla otra vez con las nuevas credenciales.</ul> **Nota**: Si se han descargado varios archivos de credenciales de almacén, solo el último archivo será válido las próximas 48 horas. Le recomendamos que descargue un nuevo archivo de credenciales de almacén.
| **El servidor proxy o el firewall están bloqueando el registro** <br/>o <br/>**No hay conectividad de Internet** <br/><br/> Si la máquina o servidor proxy tiene una conectividad de Internet limitada y el acceso a las direcciones URL necesarias no está garantizado, se producirá un error en el registro.| Siga estos pasos:<br/> <ul><li> Acuda al equipo de TI para asegurarse de que el sistema tiene conectividad de Internet.<li> Si no tiene un servidor proxy, asegúrese de que la opción de proxy no está seleccionada al registrar el agente. [Compruebe la configuración de proxy](#verifying-proxy-settings-for-windows).<li> Si tiene un servidor proxy o un firewall, acuda al equipo de red para asegurarse de que estas direcciones URL y direcciones IP tienen acceso:<br/> <br> **URLs**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Direcciones IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Intente realizar el registro de nuevo después de completar los pasos de solución de problemas anteriores.
| **El software antivirus está bloqueando el registro** | Si tiene un software antivirus instalado en el servidor, agregue al examen antivirus las reglas de exclusión necesarias correspondientes a los siguientes archivos y carpetas: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> La carpeta temporal. La ubicación predeterminada es: C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch. <li> La carpeta Bin en C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendaciones adicionales
- Vaya a C:/Windows/Temp para comprobar si hay más de 60 000 o 65 000 archivos con la extensión .tmp. Si es el caso, elimínelos.
- Asegúrese de que la fecha y la hora de la máquina coinciden con la zona horaria local.
- Asegúrese de que [estos sitios](backup-configure-vault.md#verify-internet-access) están incluidos como sitios de confianza en Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Comprobación de la configuración de proxy en Windows

1. Descargue PsExec de la página [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec).
1. Ejecute `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` en un símbolo del sistema con privilegios elevados.

   Este comando abrirá Internet Explorer.
1. Vaya a **Herramientas** > **Opciones de Internet** > **Conexiones** > **Configuración de LAN**.
1. Compruebe la configuración del proxy de la cuenta del sistema.
1. Si no se ha configurado ningún proxy, pero se han proporcionado detalles del proxy, quite esos detalles.
1. Si hay un proxy configurado y los detalles del proxy son incorrectos, asegúrese de que los datos de **IP del servidor proxy** y **Puerto** son correctos.
1. Cierre Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>No se puede descargar el archivo de credenciales de almacén

| Error   | Acciones recomendadas |
| ---     | ---    |
|No se pudo descargar el archivo de credenciales de almacén. (Id.: 403) | <ul><li> Pruebe a descargar las credenciales del almacén con otro explorador o siga estos pasos: <ul><li> Inicie Internet Explorer. Seleccione F12. </li><li> Vaya a la pestaña **Red** y borre la memoria caché y las cookies. </li> <li> Actualice la página.<br></li></ul> <li> Compruebe si la suscripción está deshabilitada o ha expirado.<br></li> <li> Compruebe si hay alguna regla de firewall que esté bloqueando la descarga. <br></li> <li> Asegúrese de que no ha agotado el límite en el almacén (50 máquinas por almacén).<br></li>  <li> Asegúrese de que el usuario tiene los permisos de Azure Backup necesarios para descargar credenciales de almacén y registrar un servidor con el almacén. Vea [Uso del control de acceso basado en roles para administrar puntos de recuperación de Azure Backup](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>El Agente de Microsoft Azure Recovery Service no se pudo conectar a Microsoft Azure Backup

| Error  | Causa posible | Acciones recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>El Agente de servicios Microsoft Azure Recovery no se pudo conectar al servicio Microsoft Azure Backup. (Id.: 100050) Compruebe la configuración de red y asegúrese de que tiene conexión a Internet.<li>(407) Se requiere autenticación del proxy. |El proxy bloquea la conexión. |  <ul><li>En Internet Explorer, vaya a **Herramientas** > **Opciones de Internet** > **Seguridad** > **Internet**. Seleccione **Nivel personalizado** y desplácese hasta que vea la sección de **descarga de archivos**. Seleccione **Habilitar**.<p>También es posible que deba agregar [direcciones URL y direcciones IP](backup-configure-vault.md#verify-internet-access) a los sitios de confianza en Internet Explorer.<li>Cambie la configuración para usar un servidor proxy. A continuación, proporcione los detalles del servidor proxy.<li> Si la máquina tiene limitado el acceso a Internet, asegúrese de que su configuración de firewall está establecida para permitir estas [direcciones URL y direcciones IP](backup-configure-vault.md#verify-internet-access). <li>Si tiene un software antivirus instalado en el servidor, excluya estos archivos del examen: <ul><li>CBEngine.exe (en lugar de dpmra.exe).<li>CSC.exe (relacionado con .NET Framework). Hay un archivo CSC.exe por cada versión de .NET Framework instalada en el servidor. Excluya todos los archivos CSC.exe de todas las versiones de .NET Framework en el servidor afectado. <li>La ubicación de la memoria caché o la carpeta temporal. <br>La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la memoria caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch.<li>La carpeta Bin en C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>No se pudo establecer la clave de cifrado para proteger las copias de seguridad

| Error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| <br />No se pudo establecer la clave de cifrado para proteger las copias de seguridad. La activación no se realizó completamente, pero la frase de contraseña de cifrado se guardó en el siguiente archivo. |<li>El servidor ya está registrado con otro almacén.<li>Durante la configuración, se ha dañado la frase de contraseña.| Anule el registro del servidor del almacén y vuelva a registrarlo con una nueva frase de contraseña.

## <a name="the-activation-did-not-complete-successfully"></a>La activación no se completó correctamente.

| Error  | Causas posibles | Acciones recomendadas |
|---------|---------|---------|
|<br />La activación no se completó correctamente. No se pudo realizar la operación actual debido a un error de servicio interno [0x1FC07]. Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft.     | <li> La carpeta temporal se encuentra en un volumen sin espacio suficiente. <li> La carpeta temporal se ha trasladado incorrectamente. <li> Falta el archivo OnlineBackup.KEK.         | <li>Actualice a la [versión más reciente](https://aka.ms/azurebackup_agent) del agente de MARS.<li>Mueva la carpeta temporal o la ubicación de caché a un volumen con espacio libre entre el 5 % y el 10 % del tamaño total de los datos de copia de seguridad. Para mover correctamente la ubicación de caché, vea los pasos de [Preguntas comunes acerca de la realización de copias de seguridad de archivos y carpetas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Asegúrese de que exista el archivo OnlineBackup.KEK. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la memoria caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La frase de contraseña de cifrado no está configurada correctamente.

| Error  | Causas posibles | Acciones recomendadas |
|---------|---------|---------|
| <br />Error 34506. La frase de contraseña de cifrado almacenada en este equipo no está configurada correctamente.    | <li> La carpeta temporal se encuentra en un volumen sin espacio suficiente. <li> La carpeta temporal se ha trasladado incorrectamente. <li> Falta el archivo OnlineBackup.KEK.        | <li>Actualice a la [versión más reciente](https://aka.ms/azurebackup_agent) del agente de MARS.<li>Mueva la carpeta temporal o la ubicación de caché a un volumen con espacio libre entre el 5 % y el 10 % del tamaño total de los datos de copia de seguridad. Para mover correctamente la ubicación de caché, vea los pasos de [Preguntas comunes acerca de la realización de copias de seguridad de archivos y carpetas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Asegúrese de que exista el archivo OnlineBackup.KEK. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la memoria caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Las copias de seguridad no se ejecutan según la programación
Si las copias de seguridad programadas no se desencadenan automáticamente, pero las copias de seguridad manuales funcionan bien, pruebe las acciones siguientes:

- Asegúrese de que la programación de copia de seguridad de Windows Server no entra en conflicto con la programación de copia de seguridad de archivos y carpetas de Azure.

- Asegúrese de que el estado de copia de seguridad en línea está establecido en **Habilitar**. Haga lo siguiente para comprobar el estado:

  1. En el Programador de tareas, expanda **Microsoft** y seleccione **Copia de seguridad en línea**.
  1. Haga doble clic en **Microsoft-OnlineBackup** y vaya a la pestaña **Desencadenadores**.
  1. Compruebe si el estado de la tarea está establecido en **Habilitado**. Si no lo está, seleccione **Editar**, luego **Habilitado** y, por último, **Aceptar**.

- Asegúrese de que la cuenta de usuario que está seleccionada para la ejecución de la tarea es la de **sistema** o la del **grupo de administradores locales** del servidor. Para comprobar la cuenta de usuario, vaya a la pestaña **General** y consulte las opciones en **Seguridad**.

- Asegúrese de que PowerShell 3.0 o posterior está instalado en el servidor. Para comprobar la versión de PowerShell, ejecute este comando y confirme que el número de versión reflejado en `Major` es 3 o posterior:

  `$PSVersionTable.PSVersion`

- Asegúrese de que esta ruta de acceso forma parte de la variable de entorno `PSMODULEPATH`:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Si la directiva de ejecución de PowerShell para `LocalMachine` está establecida como restringida, el cmdlet de PowerShell que desencadena la tarea de copia de seguridad puede producir errores. Ejecute estos comandos con permisos elevados para comprobar y establecer la directiva de ejecución en `Unrestricted` o `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Asegúrese de que no falta ningún archivo de MSOnlineBackup del módulo de PowerShell ni está dañado. Haga lo siguiente si falta algún archivo o está dañado:

  1. Desde cualquier máquina que tenga un agente de MARS que funcione correctamente, copie la carpeta MSOnlineBackup de C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. En la máquina con el error, pegue los archivos copiados en la misma ubicación de carpeta (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Si ya hay una carpeta MSOnlineBackup en la máquina, pegue los archivos en ella o reemplace los archivos existentes.


> [!TIP]
> Para garantizar que los cambios realizados se aplican de forma coherente, reinicie el servidor después de realizar los pasos anteriores.


## <a name="troubleshoot-restore-problems"></a>Solución de problemas de restauración

Es posible que Azure Backup no monte correctamente el volumen de recuperación, incluso tras varios minutos. También se podrían producir mensajes de error durante el proceso. Para empezar la recuperación normalmente, siga estos pasos:

1.  Cancele el proceso de montaje si se ha estado ejecutando durante varios minutos.

2.  Compruebe si tiene la versión más reciente del agente de Backup. Para averiguar la versión, en el panel **Acciones** de la consola de MARS, seleccione **Acerca del Agente de Microsoft Azure Recovery Services**. Confirme que el número de **versión** es igual o mayor que la versión mencionada en [este artículo](https://go.microsoft.com/fwlink/?linkid=229525). Seleccione este vínculo para [descargar la versión más reciente](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Vaya a **Administrador de dispositivos** > **Controladores de almacenamiento** y busque el **iniciador iSCSI de Microsoft**. Si lo encuentra, vaya directamente al paso 7.

4.  Si no encuentra el servicio del iniciador iSCSI de Microsoft, intente encontrar una entrada en **Administrador de dispositivos** > **Controladores de almacenamiento** denominada **Dispositivo desconocido** con el identificador de hardware **ROOT\ISCSIPRT**.

5.  Haga clic con el botón derecho en **Dispositivo desconocido** y seleccione **Actualizar software de controlador**.

6.  Actualice el controlador. Para ello, seleccione la opción **Buscar software de controlador actualizado automáticamente**. Esta actualización debe cambiar **Dispositivo desconocido** por **Iniciador iSCSI de Microsoft**:

    ![Captura de pantalla del Administrador de dispositivos de Azure Backup con la opción Controladores de almacenamiento resaltada](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vaya a **Administrador de tareas** > **Servicios (local)**  > **Servicio del iniciador iSCSI de Microsoft**:

    ![Captura de pantalla del Administrador de tareas de Azure Backup con la opción Servicios (local) resaltada](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie el servicio del iniciador iSCSI de Microsoft. Para ello, haga clic con el botón derecho en el servicio y, después, seleccione **Detener**. Luego, haga clic con el botón derecho otra vez y seleccione **Iniciar**.

9.  Vuelva a intentar la recuperación mediante la [restauración instantánea](backup-instant-restore-capability.md).

Si la recuperación sigue sin funcionar, reinicie el cliente o el servidor. Si no quiere reiniciar o la recuperación sigue sin funcionar incluso después del reinicio del servidor, pruebe a [recuperar desde otra máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico
Si sigue necesitando ayuda, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes
* Más detalles sobre la [creación de copias de seguridad de Windows Server con el agente de Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Si necesita restaurar una copia de seguridad, vea [Restauración de archivos en Windows](backup-azure-restore-windows-server.md).
