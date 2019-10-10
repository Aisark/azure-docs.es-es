---
title: Solución de problemas de actualización del proveedor de Microsoft Azure Site Recovery | Microsoft Docs
description: Descripción y
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: raynew
ms.openlocfilehash: 6bb8263e7291506be6af7d5af7eda5786274d73c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972621"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Solución de problemas de actualización del proveedor de Microsoft Azure Site Recovery

Este artículo le ayuda a resolver problemas que pueden causar errores durante la actualización de un proveedor de Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Se produce un error en la actualización al notificar que el proveedor más reciente de Site Recovery ya está instalado

Al actualizar el proveedor de Microsoft Azure Site Recovery (DRA), se produce un error en la actualización de la instalación unificada y se envía el siguiente mensaje de error:

No se admite la actualización porque ya está instalada una versión posterior del software.

Para actualizar, realice los siguientes pasos:

1. Descargue la instalación unificada de Microsoft Azure Site Recovery:
   1. En la sección "Vínculos a paquetes acumulativos de actualizaciones admitidos actualmente" del artículo [Actualizaciones del servicio Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups), seleccione el proveedor al que va a actualizar.
   2. En la página de acumulación, busque la sección **Actualización de la información** y descargue el paquete acumulativo de actualizaciones para la instalación unificada de Microsoft Azure Site Recovery.

2. Abra un símbolo del sistema y vaya hasta la carpeta en la que descargó el archivo de instalación unificada. Extraiga los archivos de instalación de la descarga mediante el comando MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;ruta de acceso de la carpeta para los archivos extraídos&gt;.
    
    Comando de ejemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. En el símbolo del sistema, vaya a la carpeta en la que extrajo los archivos y ejecute los siguientes comandos de instalación:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Vuelva a la carpeta donde descargó el programa de instalación unificada y ejecute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para finalizar la actualización. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Error durante la actualización debido a que se ha cambiado el nombre de una carpeta de terceros

Para que la actualización se realice correctamente, no se debe cambiar el nombre de esta carpeta.

Para resolver el problema:

1. Inicie el Editor del Registro (regedit.exe) y abra la rama HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Inspeccione el valor de la clave `Build_Version`. Si está establecido en la versión más reciente, reduzca el número de versión. Por ejemplo, si la versión más reciente es la 9.22.\* y `Build_Version` es la clave establecida para ese valor, reduzca la versión a 9.21.\*.
1. Descargue la instalación unificada más reciente de Microsoft Azure Site Recovery:
   1. En la sección "Vínculos a paquetes acumulativos de actualizaciones admitidos actualmente" del artículo [Actualizaciones del servicio Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups), seleccione el proveedor al que va a actualizar.
   2. En la página de acumulación, busque la sección **Actualización de la información** y descargue el paquete acumulativo de actualizaciones para la instalación unificada de Microsoft Azure Site Recovery.
1. Abra un símbolo del sistema y vaya a la carpeta en la que descargó el archivo de la instalación unificada y extraiga los archivos de instalación de la descarga mediante el siguiente comando: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;ruta de acceso a la carpeta para los archivos extraídos&gt;.

    Comando de ejemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. En el símbolo del sistema, vaya a la carpeta en la que extrajo los archivos y ejecute los siguientes comandos de instalación:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Use el administrador de tareas para supervisar el progreso de la instalación. Cuando el proceso para CX_THIRDPARTY_SETUP.EXE ya no esté visible en el administrador de tareas, continúe con el paso siguiente.
1. Compruebe que C:\thirdparty existe y que la carpeta contiene las bibliotecas RRD.
1. Vuelva a la carpeta donde descargó el programa de instalación unificada y ejecute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para finalizar la actualización. 