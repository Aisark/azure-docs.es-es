---
title: Guía para instalar e implementar el agente basado en C de Linux del agente de Azure Security Center para IoT | Microsoft Docs
description: Aprenda a instalar el agente de Azure Security Center for IoT en máquinas Linux de 32 bits y 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812731"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implementación del agente de seguridad basado en C de Azure Security Center para IoT para Linux

En esta guía se explica cómo instalar e implementar el agente de seguridad basado en C de Azure Security Center para IoT en Linux.

En esta guía, aprenderá a: 
> [!div class="checklist"]
> * Instalación
> * Comprobación de la implementación
> * Desinstalación del agente
> * Solución de problemas 

## <a name="prerequisites"></a>Requisitos previos

Para obtener información sobre otras plataformas y tipos de agente, vea [Elegir el agente de seguridad correcto](how-to-deploy-agent.md).

1. Para implementar el agente de seguridad, se necesitan derechos de administrador local en el equipo de instalación (sudo).

1. [Crear un módulo de seguridad](quickstart-create-security-twin.md) para el dispositivo.

## <a name="installation"></a>Instalación 

Para instalar e implementar el agente de seguridad, use el siguiente flujo de trabajo:


1. Descargue en la máquina la última versión desde [GitHub](https://aka.ms/iot-security-github-c).

1. Extraiga el contenido del paquete y vaya a la carpeta _/src/installation_.

1. Agregue permisos de ejecución al **script de InstallSecurityAgent**, para lo que debe ejecutar el siguiente comando:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. A continuación, ejecute: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Para más información acerca de los parámetros de autenticación, consulte [Procedimiento para configurar la autenticación](concept-security-agent-authentication-methods.md).

Este script realiza la siguiente función:

1. Instala los requisitos previos.

2. Agrega un usuario de servicio (con el inicio de sesión interactivo deshabilitado).

3. Instala el agente como **daemon** (se da por hecho que el dispositivo usa **systemd** para la administración del servicio).

4. Configura al agente con los parámetros de autenticación proporcionados. 

Para obtener ayuda adicional, ejecute el script con el parámetro – help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Desinstalación del agente

Para desinstalar al agente, ejecute el script con el parámetro –-uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>solución de problemas
Compruebe el estado de implementación, para lo que debe ejecutar:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Pasos siguientes
- Lea la [introducción](overview.md) del servicio Azure Security Center para IoT
- Obtenga más información sobre la [arquitectura](architecture.md) de Azure Security Center para IoT
- Habilite el [servicio](quickstart-onboard-iot-hub.md)
- Lea las [preguntas más frecuentes](resources-frequently-asked-questions.md)
- Obtenga información acerca de las [alertas de seguridad](concept-security-alerts.md)
