---
title: Carga de un certificado de administración de servicios de Azure | Microsoft Docs
description: Aprenda a cargar el certificado de administración de servicios para Azure Portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 014a26c2500959502eeb1c50d3f311584c1ad84e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895705"
---
# <a name="upload-an-azure-service-management-certificate"></a>Carga de un certificado de administración de servicios de Azure
Los certificados de administración le permiten autenticar con el modelo de implementación clásica que proporciona Azure. Muchos programas y herramientas (como Visual Studio o Azure SDK) utilizan estos certificados para automatizar la configuración y la implementación de diferentes servicios de Azure. 

> [!WARNING]
> Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados.
>
>

Si deseara más información acerca de los certificados de Azure (incluido cómo crear un certificado autofirmado), consulte [Introducción a los certificados para Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

También puede usar [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticar el código de cliente para fines de automatización.

**Nota:** Debe ser Coadministrador en la suscripción para realizar operaciones en certificados de administración. [Más información](https://go.microsoft.com/fwlink/?linkid=849300) acerca de cómo agregar o quitar coadministradores en el nuevo Azure Portal 

## <a name="upload-a-management-certificate"></a>Carga de un certificado de administración
Una vez que tenga creado un certificado de administración, (archivo .cer con solo la clave pública) puede cargarlo en el portal. Cuando el certificado esté disponible en el portal, cualquiera que tenga un certificado que coincida (clave privada) puede conectarse a través de Management API y obtener acceso a los recursos de la suscripción asociada.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Todos los servicios** en la lista de servicios de Azure en la parte inferior y, a continuación, seleccione **Suscripciones** en el grupo de servicios _General_.

    ![Menú Suscripción](./media/azure-api-management-certs/subscriptions_menu.png)

3. Asegúrese de seleccionar la suscripción correcta a la que desee asociar el certificado.     
4. Después de haber seleccionado la suscripción correcta, presione **Certificados de administración** en el grupo _Configuración_.

    ![Configuración](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Presione el botón **Cargar** .

    ![Cargar la página Certificados](./media/azure-api-management-certs/certificates_page.png)
6. Rellene la información del cuadro de diálogo y haga clic en **Cargar**.

    ![Configuración](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un certificado de administración asociado a una suscripción, puede conectarse mediante programación (después de haber instalado localmente el certificado correspondiente) a la [API de REST del modelo de implementación clásica](/azure/#pivot=sdkstools) y automatizar los distintos recursos de Azure que también están asociados a esa suscripción.
