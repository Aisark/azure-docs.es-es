---
title: Solución de problemas del agente de Azure Backup
description: Solución de problemas de instalación y registro del Agente de Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: d8a1d261808eb8f97d1e0dab78b767b37ae6802f
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743136"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Solución de problemas del agente de Microsoft Azure Recovery Services (MARS)

A continuación, se indica cómo resolver los errores que pueden aparecer durante la configuración, el registro, la copia de seguridad y la restauración.

## <a name="basic-troubleshooting"></a>Pasos básicos para solucionar problemas

Se recomienda realizar la siguiente validación, antes de empezar a solucionar problemas del agente de Microsoft Azure Recovery Services (MARS):

- [Asegúrese de que el agente de Microsoft Azure Recovery Services (MARS) está actualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Asegúrese de que haya conectividad de red entre el agente de MARS y Azure](https://aka.ms/AB-A4dp50)
- Asegúrese de que se ejecuta Microsoft Azure Recovery Services (en la consola del servicio). Si es necesario, reinicie y vuelva a intentar la operación.
- [Asegúrese de que haya disponible entre un 5 % y un 10 % en la ubicación de la carpeta temporal](https://aka.ms/AB-AA4dwtt)
- [Compruebe si otro proceso o software antivirus interfiere con Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Se produce un error en las copias de seguridad programadas pero no en las copias de seguridad manuales](https://aka.ms/ScheduledBackupFailManualWorks)
- Asegúrese de que su sistema operativo tiene las actualizaciones más recientes.
- [Asegúrese de que las unidades no compatibles y los archivos con atributos no compatibles se excluyen de la copia de seguridad](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Asegúrese de que el **reloj del sistema** del sistema protegido está configurado con la zona horaria correcta <br>
- [Asegúrese de que el servidor tiene como mínimo .NET Framework 4.5.2 o versiones posteriores](https://www.microsoft.com/download/details.aspx?id=30653).<br>
- Si está intentando **volver a registrar el servidor** en un almacén: <br>
  - Asegúrese de que el agente no está instalado en el servidor y de que se ha eliminado del portal. <br>
  - Utilice la misma frase de contraseña que se usó inicialmente para registrar el servidor. <br>
- En el caso de copia de seguridad sin conexión, asegúrese de que Azure PowerShell versión 3.7.0 está instalado en el equipo de origen y copia antes de comenzar la operación de copia de seguridad sin conexión
- [Consideración al agente de copia de seguridad se ejecuta en una máquina virtual de Azure](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Se han proporcionado credenciales de almacén no válidas

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| **Error** </br> *Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. (Id.: 34513)* | <ul><li> Las credenciales del almacén no son válidas (es decir, se descargaron más de 48 horas antes del registro).<li>El agente de MARS no puede descargar archivos en el directorio TEMP de Windows. <li>Las credenciales del almacén están en una ubicación de red. <li>TLS 1.0 está deshabilitado<li> Hay un servidor proxy configurado que bloquea la conexión. <br> |  <ul><li>Descargue las credenciales de almacén nuevas. (**Nota**: si se han descargado anteriormente varios archivos de credenciales de almacén, solo el archivo descargado más reciente será válido dentro de 48 horas). <li>Vaya a **IE** > **Configuración** > **Opciones de Internet** > **Seguridad** > **Internet**. A continuación, seleccione **Nivel personalizado** y desplácese hasta que vea la sección de descarga de archivos. Seleccione **Habilitar**.<li>Es posible que también tenga que agregar estos sitios a los [sitios de confianza](https://docs.microsoft.com/azure/backup/backup-configure-vault#verify-internet-access) de Internet Explorer.<li>Cambie la configuración para usar un servidor proxy. A continuación, proporcione los detalles del servidor proxy. <li> Haga coincidir la fecha y hora con las de la máquina.<li>Si recibe un error que indica que no se permiten las descargas de archivos, es probable que haya un gran número de archivos en el directorio C:/Windows/Temp.<li>Vaya a C:/Windows/Temp para comprobar si hay más de 60 000 o 65 000 archivos con la extensión .tmp. Si es el caso, elimínelos.<li>Asegúrese de que tiene instalado .NET Framework 4.6.2. <li>Si deshabilitó TLS 1.0 a causa del cumplimiento de PCI, consulte esta [página de solución de problemas](https://support.microsoft.com/help/4022913). <li>Si tiene instalado un software antivirus instalado en el servidor, excluya los archivos siguientes del examen: <ul><li>CBengine.exe<li>CSC.exe, relacionado con .NET Framework. Hay un archivo CSC.exe por cada versión de .NET instalada en el servidor. Excluya todos los archivos CSC.exe vinculados a todas las versiones de .NET Framework en el servidor afectado. <li>Ubicación de caché o carpeta temporal. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>La carpeta Bin está en C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Bin.

## <a name="unable-to-download-vault-credential-file"></a>No se puede descargar el archivo de credenciales de almacén

| Detalles del error | Acciones recomendadas |
| ---     | ---    |
|No se pudo descargar el archivo de credenciales de almacén. (Id.: 403) | <ul><li> Intente descargar las credenciales de almacén mediante un explorador diferente o realice los pasos siguientes: <ul><li> Inicie Internet Explorer y presione F12. </li><li> Vaya a la pestaña **Red** para borrar la caché de Internet Explorer y las cookies. </li> <li> Actualice la página.<br>O BIEN</li></ul> <li> Compruebe si la suscripción está deshabilitada o ha expirado.<br>O BIEN</li> <li> Compruebe si hay alguna regla de firewall que esté bloqueando la descarga del archivo de credenciales de almacén. <br>O BIEN</li> <li> Asegúrese de que no haya agotado el límite en el almacén (50 máquinas por almacén).<br>O BIEN</li>  <li> Asegúrese de usuario tenga el permiso de copia de seguridad de Azure para descargar credenciales de almacén y registrar el servidor con el almacén, vea necesario [artículo](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>El Agente de Microsoft Azure Recovery Service no se pudo conectar a Microsoft Azure Backup

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| **Error** <br /><ol><li>*El Agente de Microsoft Azure Recovery Services no se pudo conectar a Microsoft Azure Backup. (Id.: 100050) Compruebe la configuración de red y asegúrese de que tiene conexión a Internet*<li>*(407) Se requiere autenticación del proxy* |El proxy bloquea la conexión. |  <ul><li>Vaya a **IE** > **Configuración** > **Opciones de Internet** > **Seguridad** > **Internet**. A continuación, seleccione **Nivel personalizado** y desplácese hasta que vea la sección de descarga de archivos. Seleccione **Habilitar**.<li>Es posible que también tenga que agregar estos sitios a los [sitios de confianza](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins) de Internet Explorer.<li>Cambie la configuración para usar un servidor proxy. A continuación, proporcione los detalles del servidor proxy.<li> Si el equipo ha limitado el acceso a internet, asegúrese de que la configuración del firewall en la máquina o proxy permite estos [direcciones URL](backup-configure-vault.md#verify-internet-access) y [dirección IP](backup-configure-vault.md#verify-internet-access). <li>Si tiene instalado un software antivirus instalado en el servidor, excluya los archivos siguientes del examen. <ul><li>CBEngine.exe (en lugar de dpmra.exe).<li>CSC.exe (relacionado con .NET Framework). Hay un archivo CSC.exe por cada versión de .NET instalada en el servidor. Excluya todos los archivos CSC.exe vinculados a todas las versiones de .NET Framework en el servidor afectado. <li>Ubicación de caché o carpeta temporal. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.<li>La carpeta Bin está en C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Bin.



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>No se pudo establecer la clave de cifrado para proteger las copias de seguridad

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| **Error** <br />*No se pudo establecer la clave de cifrado para proteger las copias de seguridad. La activación no se realizó completamente, pero la frase de contraseña de cifrado se guardó en el siguiente archivo*. |<li>El servidor ya está registrado con otro almacén.<li>Durante la configuración, se ha dañado la frase de contraseña.| Anule el registro del servidor del almacén y vuelva a registrarlo con una nueva frase de contraseña.

## <a name="the-activation-did-not-complete-successfully"></a>La activación no se completó correctamente.

| Detalles del error | Causas posibles | Acciones recomendadas |
|---------|---------|---------|
|**Error** <br />*La activación no se completó correctamente. No se pudo realizar la operación actual debido a un error de servicio interno [0x1FC07]. Intente de nuevo la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft*     | <li> La carpeta temporal se encuentra en un volumen sin espacio suficiente. <li> La carpeta temporal se movió de manera incorrecta a otra ubicación. <li> Falta el archivo OnlineBackup.KEK.         | <li>Actualice a la [versión más reciente](https://aka.ms/azurebackup_agent) del agente de MARS.<li>Mueva la carpeta temporal o la ubicación de caché a un volumen con espacio libre equivalente al 5 % o 10 % del tamaño total de los datos de copia de seguridad. Para mover correctamente la ubicación de caché, consulte los pasos de las [preguntas sobre el Agente de Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Asegúrese de que exista el archivo OnlineBackup.KEK. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La frase de contraseña de cifrado no está configurada correctamente.

| Detalles del error | Causas posibles | Acciones recomendadas |
|---------|---------|---------|
|**Error** <br />*Error 34506. La frase de contraseña de cifrado almacenada en este equipo no está configurada correctamente*.    | <li> La carpeta temporal se encuentra en un volumen sin espacio suficiente. <li> La carpeta temporal se movió de manera incorrecta a otra ubicación. <li> Falta el archivo OnlineBackup.KEK.        | <li>Actualice a la [versión más reciente](https://aka.ms/azurebackup_agent) del agente de MARS.<li>Mueva la carpeta temporal o la ubicación de caché a un volumen con espacio libre equivalente al 5 % o 10 % del tamaño total de los datos de copia de seguridad. Para mover correctamente la ubicación de caché, consulte los pasos de las [preguntas sobre el Agente de Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Asegúrese de que exista el archivo OnlineBackup.KEK. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Las copias de seguridad no se ejecutan según la programación.
Si las copias de seguridad programadas no se desencadenan automáticamente, mientras que las copias de seguridad manuales funcionan sin problemas, pruebe las acciones siguientes:

- Asegúrese de programación de copia de seguridad de Windows Server no entra en conflicto con la programación de copia de seguridad de archivos y carpetas de Azure.

- Asegúrese de que el estado de copia de seguridad en línea se establece en **habilitar**. Para comprobar el estado de realizar la siguiente:

  - Vaya a **Panel de Control** > **Herramientas administrativas** > **Programador de tareas**.
    - Expanda **Microsoft** y seleccione **Copia de seguridad en línea**.
  - Haga doble clic en **Microsoft-OnlineBackup** y vaya a la pestaña **Desencadenadores**.
  - Compruebe si el estado se establece en **habilitado**. En caso contrario, seleccione **Editar**, active la casilla **Habilitado** y haga clic en **Aceptar**.

- Asegúrese de la cuenta de usuario seleccionada para ejecutar la tarea es **sistema** o **grupo Local de administradores** en el servidor. Para comprobar la cuenta de usuario, vaya a la **General** pestaña y compruebe el **las opciones de seguridad**.

- Compruebe si PowerShell 3.0 o posterior está instalado en el servidor. Para comprobar la versión de PowerShell, ejecute el siguiente comando y verifique que el número de versión *principal* es igual o mayor que 3.

  `$PSVersionTable.PSVersion`

- Compruebe si la ruta de acceso siguiente forma parte de la variable de entorno *PSMODULEPATH*.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Si la directiva de ejecución de PowerShell para *LocalMachine* está establecida como restringida, el cmdlet de PowerShell que desencadena la tarea de copia de seguridad puede producir errores. Ejecute los siguientes comandos con permisos elevados para comprobar y establecer la directiva de ejecución en *Unrestricted* o *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Asegúrese de que se ha reiniciado el servidor después de la instalación del agente de copia de seguridad

- Asegúrese de que no hay ninguna falta o está dañado **PowerShell** módulo **MSonlineBackup**. Si hay cualquier archivo falta o está dañado, para resolver el problema realice el siguiente:

  - Desde otra máquina (Windows 2008 R2) que el agente de MARS funciona correctamente, copie la carpeta de MSOnlineBackup de *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* ruta de acceso.
  - Pegue esto en el equipo problemático en la misma ruta de acceso *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Si **MSOnlineBackup** carpeta está ya existe en la máquina, pegar y reemplazar los archivos de contenido dentro de él.


> [!TIP]
> Para garantizar que los cambios realizados se aplican de forma coherente, reinicie el servidor después de realizar los pasos anteriores.


## <a name="troubleshoot-restore-issues"></a>Solución de problemas de restauración

Es posible que Azure Backup no monte correctamente el volumen de recuperación, incluso tras varios minutos. También se podrían producir mensajes de error durante el proceso. Para empezar la recuperación normalmente, siga estos pasos:

1.  Cancele el proceso de montaje en curso en caso de que se haya estado ejecutando durante varios minutos.

2.  Compruebe si tiene la versión más reciente de Backup Agent. Para saber cuál es la versión, en el panel **Acciones** de la consola de MARS, seleccione **Acerca del Agente de Microsoft Azure Recovery Services**. Confirme que el número de **versión** es igual o mayor que la versión mencionada en [este artículo](https://go.microsoft.com/fwlink/?linkid=229525). Puede descargar la versión más reciente [aquí](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Vaya al **Administrador de dispositivos** > **Controladores de almacenamiento** y busque el **iniciador iSCSI de Microsoft**. Si lo encuentra, vaya directamente al paso 7.

4.  Si no encuentra el servicio del iniciador iSCSI de Microsoft, intente encontrar una entrada en **Administrador de dispositivos** > **Controladores de almacenamiento** denominada **Dispositivo desconocido** con el identificador de hardware **ROOT\ISCSIPRT**.

5.  Haga clic con el botón derecho en **Dispositivo desconocido** y seleccione **Actualizar software de controlador**.

6.  Actualice el controlador. Para ello, seleccione la opción **Buscar software de controlador actualizado automáticamente**. Al finalizar la actualización, **Dispositivo desconocido** debería pasar a **Iniciador iSCSI de Microsoft**, tal y como se muestra a continuación.

    ![Captura de pantalla del Administrador de dispositivos de Azure Backup con la opción Controladores de almacenamiento resaltada](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vaya a **Administrador de tareas** > **Servicios (local)**  > **Servicio del iniciador iSCSI de Microsoft**.

    ![Captura de pantalla del Administrador de tareas de Azure Backup con la opción Servicios (local) resaltada](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie el servicio del iniciador iSCSI de Microsoft. Para ello, haga clic con el botón derecho en el servicio, seleccione **Detener**, vuelva a hacer clic con el botón derecho y seleccione **Iniciar**.

9.  Vuelva a intentar la recuperación mediante [**Instant Restore**](backup-instant-restore-capability.md) (Restauración instantánea).

Si la recuperación sigue sin funcionar, reinicie el cliente o el servidor. Si no quiere reiniciar o la recuperación sigue sin funcionar incluso después del reinicio del servidor, intente recuperar desde una máquina alternativa. Siga los pasos de [este artículo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes
* Más detalles sobre la [creación de copias de seguridad de Windows Server con el Agente de Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Si necesita restaurar una copia de seguridad, use este artículo: [Restaurar archivos en una máquina de Windows Server o del Cliente de Windows](backup-azure-restore-windows-server.md).
