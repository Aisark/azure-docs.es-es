---
title: ¿Qué es Windows Virtual Desktop? - Azure
description: Una información general de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/27/2020
ms.author: helohr
ms.openlocfilehash: 168a345427be47dc1c33f43be1af47daa8f638ef
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772782"
---
# <a name="what-is-windows-virtual-desktop"></a>¿Qué es Windows Virtual Desktop? 

Windows Virtual Desktop es un servicio de virtualización de escritorio y de aplicaciones que se ejecuta en la nube.

A continuación se indica lo que puede hacer al ejecutar Windows Virtual Desktop en Azure:

* Configurar una implementación de Windows 10 de sesión múltiple que ofrezca toda la funcionalidad de Windows 10 con escalabilidad
* Virtualizar Office 365 ProPlus y optimizarlo para ejecutarse en escenarios virtuales multiusuario
* Proporcionar escritorios virtuales de Windows 7 con actualizaciones gratuitas de seguridad ampliada
* Llevar sus aplicaciones y escritorios existentes de Servicios de Escritorio remoto (RDS) y Windows Server a cualquier equipo
* Virtualizar escritorios y aplicaciones
* Administrar escritorios y aplicaciones de Windows 10, Windows Server y Windows 7 con una experiencia de administración unificada

## <a name="introductory-video"></a>Vídeo de introducción

En este vídeo, obtendrá información sobre Windows Virtual Desktop, y conocerá por qué es único y cuáles son las novedades:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Vea más vídeos sobre Windows Virtual Desktop en [nuestra lista de reproducción](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Principales capacidades

Con Windows Virtual Desktop, puede configurar un entorno flexible y escalable:

* Cree un entorno de virtualización de escritorio completo en su suscripción de Azure sin tener que ejecutar ningún servidor de puerta de enlace adicional.
* Publique todos los grupos host que necesite para dar cabida a diversas cargas de trabajo.
* Aporte su propia imagen para las cargas de trabajo de producción o de prueba desde la Galería de Azure.
* Reduzca los costos con recursos de sesión múltiple agrupados. Con la nueva funcionalidad de sesión múltiple de Windows 10 Enterprise exclusiva de Windows Virtual Desktop y del rol de host de sesión de Escritorio remoto (RDSH) de Windows Server, puede reducir considerablemente el número de máquinas virtuales y la sobrecarga del sistema operativo (SO), mientras se proporcionan los mismos recursos a los usuarios.
* Asigne propiedad individual mediante escritorios personales (permanentes).

Puede implementar y administrar escritorios virtuales:

* Use las interfaces de PowerShell y REST de Windows Virtual Desktop para configurar los grupos host, crear grupos de aplicaciones, asignar usuarios y publicar recursos.
* Publique aplicaciones de escritorio completo o aplicaciones remotas individuales desde un único grupo host, cree grupos de aplicaciones individuales para distintos conjuntos de usuarios o incluso asigne usuarios a varios grupos de aplicaciones para reducir el número de imágenes.
* A medida que administre su entorno, usará el acceso delegado integrado para asignar roles y recopilar diagnósticos con el fin de comprender diversos errores de configuración o de los usuarios.
* Use el nuevo servicio de diagnóstico para solucionar los errores.
* Administre únicamente la imagen y las máquinas virtuales y olvídese de la infraestructura. No es necesario que administre personalmente los roles de Escritorio remoto, como hace con los Servicios de Escritorio remoto, sino solo las máquinas virtuales de su suscripción de Azure.

También puede asignar y conectar a los usuarios a los escritorios virtuales:

* Una vez asignados, los usuarios pueden iniciar cualquier cliente de Windows Virtual Desktop para conectar los usuarios a sus aplicaciones y escritorios de Windows publicados. Conéctese desde cualquier dispositivo mediante una aplicación nativa de su dispositivo o por medio del cliente web HTML5 de Windows Virtual Desktop.
* Establezca de forma segura los usuarios mediante conexiones inversas al servicio, así nunca tendrá que dejar los puertos de entrada abiertos.

## <a name="requirements"></a>Requisitos

Es necesario cumplir una serie de requisitos para configurar Windows Virtual Desktop y conectar correctamente sus usuarios a los escritorios y aplicaciones de Windows.

Está previsto agregar compatibilidad con los siguientes sistemas operativos, así que asegúrese de que dispone de las [licencias adecuadas](https://azure.microsoft.com/pricing/details/virtual-desktop/) para los usuarios según el escritorio y las aplicaciones que tenga pensado implementar:

|SO|Licencia necesaria|
|---|---|
|Sesión múltiple de Windows 10 Enterprise o Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licencia de acceso de cliente (CAL) de RDS con Software Assurance|

Su infraestructura necesita cumplir los siguientes requisitos para ser compatible con Windows Virtual Desktop:

* Una instancia de [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).
* Una instancia de Windows Server Active Directory sincronizada con Azure Active Directory. Puede configurarla con uno de los siguientes elementos:
  * Azure AD Connect (para organizaciones híbridas)
  * Azure AD Domain Services (para organizaciones híbridas o de nube)
* Una suscripción a Azure que contenga una red virtual que conste de la instancia de Windows Server Active Directory o esté conectada a ella
  
Las máquinas virtuales de Azure que cree para Windows Virtual Desktop deben cumplir estos requisitos:

* Estar [unidas a un dominio estándar](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) o a un [dominio híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Las máquinas virtuales no pueden estar unidas a Azure AD.
* Deben ejecutar una de las siguientes [imágenes de sistema operativo admitidas](#supported-virtual-machine-os-images).

>[!NOTE]
>Si necesita una suscripción a Azure, puede [registrarse para obtener una evaluación gratuita por un mes](https://azure.microsoft.com/free/). Si usa la versión de evaluación gratuita de Azure, debe utilizar Azure AD Domain Services para mantener sincronizada su instancia de Windows Server Active Directory con Azure Active Directory.

Las máquinas virtuales de Azure que cree para Windows Virtual Desktop deben tener acceso a las siguientes direcciones URL:

|Dirección|Puerto de salida|Propósito|
|---|---|---|
|*.wvd.microsoft.com|Puerto TCP 443|Tráfico de servicio|
|*.blob.core.windows.net|Puerto TCP 443|Agente, actualizaciones de pila SXS y tráfico de agente|
|*.core.windows.net|Puerto TCP 443|Tráfico de agente|
|*.servicebus.windows.net|Puerto TCP 443|Tráfico de agente|
|prod.warmpath.msftcloudes.com|Puerto TCP 443|Tráfico de agente|
|catalogartifact.azureedge.net|Puerto TCP 443|Azure Marketplace|
|kms.core.windows.net|Puerto TCP 1688|Activación de Windows 10|

>[!IMPORTANT]
>Es esencial abrir estas direcciones URL para una implementación confiable de Windows Virtual Desktop. No se admite el bloqueo del acceso a estas direcciones URL y afectará a la funcionalidad del servicio. Estas direcciones URL solo se corresponden con sitios y recursos de Windows Virtual Desktop y no incluyen direcciones URL para otros servicios como Azure Active Directory.

>[!NOTE]
>Windows Virtual Desktop actualmente no tiene una lista de intervalos de direcciones IP que puede incluir en la lista blanca para permitir el tráfico de red. En este momento, solo se admiten direcciones URL específicas en las listas blancas.
>
>Debe usar el carácter comodín (*) para las direcciones URL que impliquen tráfico de servicio. Si prefiere no usar el carácter comodín (*) para el tráfico relacionado con el agente, aquí se muestra cómo buscar las direcciones URL sin estos caracteres:
>
>1. Registre las máquinas virtuales en el grupo de hosts de Windows Virtual Desktop.
>2. Abra **Visor de eventos** y desplácese a **Registros de Windows** > **Aplicación** > **WVD-Agent** y busque el evento ID 3702.
>3. Agregue a la lista de permitidos las direcciones URL que se encuentran en el identificador de evento 3702. Las direcciones URL del identificador de evento 3702 son específicas de la región. Deberá repetir el proceso de inclusión en la lista de permitidos con las direcciones URL pertinentes de cada región en la que desee implementar las máquinas virtuales.

Windows Virtual Desktop consta de los escritorios y las aplicaciones de Windows que entrega a los usuarios y de la solución de administración, que Microsoft hospeda en Azure como un servicio. Se pueden implementar escritorios y aplicaciones en máquinas virtuales (VM) de cualquier región de Azure, y la solución de administración y los datos de estas VM residirán en Estados Unidos. Esto puede dar lugar a la transferencia de datos a Estados Unidos.

Para obtener un rendimiento óptimo, asegúrese de que la red cumple los requisitos siguientes:

* La latencia de ida y vuelta (RTT) desde la red del cliente hasta la región de Azure donde se han implementado grupos host debe ser inferior a 150 ms.
* El tráfico de red puede fluir fuera de las fronteras del país o la región si las máquinas virtuales que hospedan los escritorios y las aplicaciones se conectan al servicio de administración.
* Para optimizar el rendimiento de la red, se recomienda que las máquinas virtuales del host de sesión se coloquen en la misma región de Azure que el servicio de administración.

## <a name="supported-remote-desktop-clients"></a>Clientes compatibles de Escritorio remoto

Los clientes de Escritorio remoto siguientes admiten Windows Virtual Desktop:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (versión preliminar)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Imágenes de SO de máquinas virtuales admitidas

Windows Virtual Desktop admite las imágenes de SO x64 siguientes:

* Sesión múltiple de Windows 10 Enterprise, versión 1809 o superior
* Windows 10 Enterprise, versión 1809 o superior
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop no es compatible con imágenes de los sistemas operativos x86 (32 bits), Windows 10 Enterprise N o Windows 10 Enterprise KN. Windows 7 tampoco admite las soluciones de perfil basadas en VHD o VHDX hospedadas en Azure Storage administrado debido a un límite de tamaño del sector.

Las opciones de automatización y de implementación disponibles dependen del sistema operativo y la versión que elija, tal como se muestra en la tabla siguiente: 

|Sistema operativo|Galería de imágenes de Azure|Implementación manual de la máquina virtual|Integración de la plantilla de Azure Resource Manager|Aprovisionamiento de grupos host en Azure Marketplace|Actualizaciones del agente de Windows Virtual Desktop|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Sesión múltiple de Windows 10, versión 1903|Sí|Sí|Sí|Sí|Automático|
|Sesión múltiple de Windows 10, versión 1809|Sí|Sí|No|No|Automático|
|Windows 10 Enterprise, versión 1903|Sí|Sí|Sí|Sí|Automático|
|Windows 10 Enterprise, versión 1809|Sí|Sí|No|No|Automático|
|Windows 7 Enterprise|Sí|Sí|No|No|Manual|
|Windows Server 2019|Sí|Sí|No|No|Automático|
|Windows Server 2016|Sí|Sí|Sí|Sí|Automático|
|Windows Server 2012 R2|Sí|Sí|No|No|Automático|

## <a name="next-steps"></a>Pasos siguientes

Para comenzar, deberá crear un inquilino. Para más información sobre cómo crear un inquilino, siga con el tutorial de creación de inquilinos.

> [!div class="nextstepaction"]
> [Creación de un inquilino en Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
