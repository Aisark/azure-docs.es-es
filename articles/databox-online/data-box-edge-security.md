---
title: Seguridad de Azure Data Box Edge| Microsoft Docs
description: Describe las características de seguridad y privacidad que protegen el dispositivo de Azure Data Box Edge, así como el servicio y los datos, tanto locales como en la nube.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970883"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Protección de datos y seguridad de Azure Data Box Edge

La seguridad es una gran preocupación para cualquiera que adopte una tecnología nueva, en especial cuando se utiliza la tecnología con información confidencial o de su propiedad. Azure Data Box Edge le ayuda a asegurarse de que solo entidades autorizadas puedan ver, modificar o eliminar sus datos.

En este artículo se describen las características de seguridad de Data Box Edge que ayudan a proteger cada uno de los componentes de la solución y los datos almacenados en ellos.

Azure Data Box Edge consta de cuatro componentes principales que interactúan entre sí:

- El **servicio Data Box Edge hospedado en Azure**. El recurso de administración que se usa para crear el pedido del dispositivo, configurar el dispositivo y, a continuación, supervisar el pedido hasta que se realiza.
- El **dispositivo de Data Box Edge**. El dispositivo de transferencia que se envía para que pueda importar los datos del entorno local a Azure.
- Los **clientes/hosts conectados al dispositivo**. Los clientes de la infraestructura que se conectan al dispositivo Data Box Edge y contienen los datos que se deben proteger.
- El **almacenamiento en la nube**. La ubicación de la plataforma Azure Cloud donde se almacenan los datos. Esta ubicación suele ser la cuenta de almacenamiento vinculada al recurso de Data Box Edge que ha creado.

## <a name="data-box-edge-service-protection"></a>Protección del servicio Data Box Edge

Data Box Edge es un servicio de administración que se hospeda en Azure. El servicio se usa para configurar y administrar el dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Protección del dispositivo Data Box Edge

El dispositivo Data Box Edge es un dispositivo local que ayuda a transformar los datos al procesarlos localmente y, a continuación, enviarlos a Azure. El dispositivo:

- Necesita una clave de activación para acceder al servicio Data Box Edge.
- Está protegido en todo momento mediante una contraseña de dispositivo.
- Es un dispositivo bloqueado. Los elementos BMC y BIOS del dispositivo están protegidos con contraseñas. El BIOS está protegido por acceso de usuario limitado.
- Tiene el arranque seguro habilitado.
- Ejecuta Device Guard de Windows Defender. Device Guard le permite ejecutar solo las aplicaciones de confianza que estén definidas en las directivas de integridad de código.

### <a name="protect-the-device-via-activation-key"></a>Protege el dispositivo con una clave de activación

Solo se permite que un dispositivo Data Box Edge autorizado se una al servicio Data Box Edge que crea en su suscripción de Azure. Si quiere autorizar un dispositivo, debe usar una clave de activación para activarlo con el servicio Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obtener más información, consulte [Obtención de la clave de activación](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Protección del dispositivo con una contraseña

Las contraseñas garantizan que solo los usuarios autorizados puedan acceder a sus datos. Los dispositivos Data Box Edge arrancan en un estado bloqueado.

Puede:

- Conectarse a la interfaz de usuario web local del dispositivo mediante un explorador y, a continuación, proporcionar una contraseña para iniciar sesión en el dispositivo.
- Conectarse de forma remota a la interfaz de PowerShell del dispositivo a través de HTTP. La administración remota está activada de manera predeterminada. A continuación, puede proporcionar la contraseña del dispositivo para iniciar sesión en el dispositivo. Para obtener más información, consulte el tema sobre la [conexión remota al dispositivo Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use la interfaz de usuario web local para [cambiar la contraseña](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Si cambia la contraseña, asegúrese de notificar a todos los usuarios de acceso remoto para que no experimenten problemas al iniciar sesión.

## <a name="protect-your-data"></a>Protección de los datos

En esta sección se describen las características de seguridad de Data Box Edge que protegen los datos en tránsito y almacenados.

### <a name="protect-data-at-rest"></a>Protección de los datos en reposo

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- El cifrado de BitLocker XTS-AES de 256 bits se usa para proteger los datos locales.


### <a name="protect-data-in-flight"></a>Protección de los datos en tránsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Protección de datos a través de cuentas de almacenamiento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Rote y, a continuación, [sincronice las claves de la cuenta de almacenamiento](data-box-edge-manage-shares.md#sync-storage-keys) periódicamente para proteger su cuenta de almacenamiento de usuarios no autorizados.

## <a name="manage-personal-information"></a>Administración de información personal

El servicio Data Box Edge recopila información personal en los escenarios siguientes:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para ver la lista de usuarios que pueden acceder a un recurso compartido o eliminarlo, siga los pasos que se indican en el tema sobre la [administración de recursos compartidos en Data Box Edge](data-box-edge-manage-shares.md).

Para más información, revise la directiva de privacidad de Microsoft en el [Centro de confianza](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Pasos siguientes

[Implemente su dispositivo Data Box Edge](data-box-edge-deploy-prep.md)
