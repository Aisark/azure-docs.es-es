---
title: Instalación de puerta de enlace de datos local | Microsoft Docs
description: Aprenda a instalar y configurar una puerta de enlace de datos local.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 629a97048ceba4ac02e3aa1dd59310980e5a0c95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327567"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalación y configuración de una puerta de enlace de datos local

Se requiere una puerta de enlace de datos local cuando uno o varios servidores de Azure Analysis Services de la misma región se conectan a orígenes de datos locales. Para más información acerca de la puerta de enlace, consulte [Puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="prerequisites"></a>Requisitos previos

**Requisitos mínimos:**

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

**Se recomienda que use:**

* CPU de 8 núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

**Consideraciones importantes:**

* Durante la instalación, al registrar la puerta de enlace en Azure, se selecciona la región predeterminado para su suscripción. Puede elegir otra región. Si tiene servidores en varias regiones, debe instalar una puerta de enlace en cada una de ellas. 
* La puerta de enlace no se puede instalar en un controlador de dominio.
* Solo se puede instalar una puerta de enlace en un equipo.
* Instale la puerta de enlace en un equipo que permanezca encendido y no entre en suspensión.
* No instale la puerta de enlace en un equipo conectado de forma inalámbrica a la red. Puede disminuir el rendimiento.
* Al instalar la puerta de enlace, la cuenta de usuario con que ha iniciado sesión en el equipo debe tener Iniciar sesión como privilegios de servicio. Una vez completada la instalación, el servicio de puerta de enlace de datos local usa la cuenta de SERVICE\PBIEgwService para iniciar sesión como servicio. Puede especificar una cuenta diferente durante la instalación o en los servicios una vez completada la instalación. Asegúrese de que la configuración de la directiva de grupo permita tanto la cuenta con la que ha iniciado sesión al instalar como la cuenta de servicio que ha elegido que tenga Iniciar sesión como privilegios de servicio.
* Inicie sesión en Azure con una cuenta de Azure AD que tenga el mismo [inquilino](/previous-versions/azure/azure-services/jj573650(v=azure.100)#BKMK_WhatIsAnAzureADTenant) que la suscripción donde va a registrar la puerta de enlace. No se pueden utilizar cuentas B2B (invitadas) de Azure para instalar y registrar una puerta de enlace.
* Si los orígenes de datos se encuentran en una red Azure Virtual Network (VNet) debe configurar la propiedad de servidor [AlwaysUseGateway](analysis-services-vnet-gateway.md).
* La puerta de enlace (unificada) que se describe aquí no se admite en regiones de Azure Alemania. En su lugar, use una **puerta de enlace local específica para Azure Analysis Services**, instalada desde el **Inicio rápido** del servidor en el portal. 


## <a name="download"></a>Descarga

 [Descargar la puerta de enlace](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalación

1. Ejecute la configuración.

2. Seleccione una ubicación, acepte los términos y haga clic en **Instalar**.

   ![Ubicación de instalación y términos de la licencia](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sesión en Azure. La cuenta debe estar en la instancia de Azure Active Directory de su inquilino. Dicha cuenta se usa para el administrador de la puerta de enlace. No se pueden utilizar cuentas B2B (invitadas) de Azure para instalar y registrar la puerta de enlace.

   ![Inicio de sesión en Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Si inicia sesión con una cuenta de dominio, se asignará a la cuenta profesional de Azure AD. La cuenta profesional se usará como administrador de la puerta de enlace.

## <a name="register"></a>Registro

Para crear un recurso de puerta de enlace en Azure, debe registrar la instancia local que instaló con el servicio en la nube de la puerta de enlace. 

1.  Seleccione **Registrar una nueva puerta de enlace en este equipo**.

    ![Register ](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Escriba el nombre y la clave de recuperación de la puerta de enlace. De forma predeterminada, la puerta de enlace usa la región predeterminada de la suscripción. Si tiene que seleccionar otra región diferente, elija **Cambiar la región**.

    > [!IMPORTANT]
    > Guarde la clave de recuperación en un lugar seguro. La clave de recuperación es necesaria para adquirir, migrar o restaurar una puerta de enlace. 

   ![Register ](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Creación de un recurso de puerta de enlace de Azure

Una vez que ha instalado y registrado la puerta de enlace, debe crear un recurso de puerta de enlace en su suscripción de Azure. Inicie sesión en Azure con la misma cuenta que usó al registrar la puerta de enlace.

1. En Azure Portal, elija **Crear un recurso** > **Integración** > **Puerta de enlace de datos local**.

   ![Creación de un recurso de puerta de enlace](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. En **Crear puerta de enlace de conexión**, escriba estos valores:

   * **Nombre**: escriba un nombre para el recurso de puerta de enlace. 

   * **Suscripción**: seleccione la suscripción de Azure que se asociará al recurso de puerta de enlace. 
   
     La suscripción predeterminada se basa en la cuenta de Azure que usó para iniciar sesión.

   * **Grupo de recursos**: Cree un grupo de recursos o seleccione uno existente.

   * **Ubicación**: seleccione la región en que se ha registrado la puerta de enlace.

   * **Nombre de instalación**: si la instalación de la puerta de enlace no está seleccionada, seleccione la puerta de enlace registrada. 

     Cuando haya terminado, haga clic en **Crear**.

## <a name="connect-servers"></a>Conexión de servidores al recursos de la puerta de enlace

1. En la introducción al servidor de Azure Analysis Services, haga clic en **Puerta de enlace de datos local**.

   ![Conectar un servidor a una puerta de enlace](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. En **Seleccione una puerta de enlace de datos local que conectar**, seleccione el recurso de puerta de enlace y, después, haga clic en **Conectar la puerta de enlace seleccionada**.

   ![Conectar un servidor a un recurso de puerta de enlace](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Si la puerta de enlace no aparece en la lista, es probable que el servidor no esté en la región que especificó al registrarla. 

Eso es todo. Si necesita abrir puertos o solucionar cualquier problema, asegúrese de consultar [Puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="next-steps"></a>Pasos siguientes

* [Administración de Analysis Services](analysis-services-manage.md)   
* [Obtención de datos de Azure Analysis Services](analysis-services-connect.md)   
* [Uso de la puerta de enlace para orígenes de datos en Azure Virtual Network](analysis-services-vnet-gateway.md)
