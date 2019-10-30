---
title: 'Solicitar acceso Just-in-Time: Azure Managed Applications'
description: Describe cómo los editores de Azure Managed Applications solicitan acceso Just-In-Time a una aplicación administrada.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: fdd1dcf5ab6b58a91534a465a8c92c10de0d2097
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528956"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Habilitación y solicitud de acceso Just-In-Time para Azure Managed Applications

Los consumidores de la aplicación administrada pueden ser reacios a concederle acceso permanente al grupo de recursos administrados. Como editor de una aplicación administrada, es preferible que los consumidores sepan exactamente cuando necesita acceder a los recursos administrados. Para ofrecer a los consumidores mayor control sobre la concesión de acceso a los recursos administrados, Azure Managed Applications proporciona una característica denominada acceso Just-In-Time (JIT), que se encuentra actualmente en versión preliminar.

El acceso JIT permite solicitar acceso con privilegios elevados a recursos de la aplicación administrada con fines de solución de problemas o mantenimiento. Siempre tendrá acceso de solo lectura a los recursos, pero puede tener mayor acceso durante un período de tiempo específico.

El flujo de trabajo de concesión de acceso es el siguiente:

1. Agrega una aplicación administrada en Marketplace y especifica que el acceso JIT esté disponible.

1. Durante la implementación, el consumidor permite el acceso JIT a esa instancia de la aplicación administrada.

1. Después de la implementación, el consumidor puede cambiar la configuración del acceso JIT.

1. Envía una solicitud de acceso cuando se necesita para solucionar problemas o actualizar los recursos administrados.

1. El consumidor aprueba la solicitud.

Este artículo se centra en las acciones que los editores llevan a cabo para habilitar el acceso JIT y enviar solicitudes. Para obtener información acerca de la aprobación de solicitudes de acceso JIT, consulte el tema sobre la [aprobación del acceso Just-In-Time en Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Agregar el paso de acceso JIT a la interfaz de usuario

El archivo CreateUiDefinition.json es exactamente igual que el archivo de interfaz de usuario que crea para el acceso permanente, excepto en que debe incluir un paso que permita a los consumidores habilitar el acceso JIT. Para más información sobre cómo publicar su primera oferta de aplicaciones administradas en Azure Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](publish-marketplace-app.md).

Para admitir la funcionalidad JIT para la oferta, agregue el siguiente contenido al archivo CreateUiDefinition.json:

En "steps":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
En "outputs":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> El acceso JIT está en versión preliminar. El esquema de configuración de JIT podría cambiar en futuras iteraciones.

## <a name="enable-jit-access"></a>Habilitar el acceso JIT

Al definir su oferta en Marketplace, asegúrese de habilitar el acceso JIT.

1. Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com).

1. Proporcione valores para publicar la aplicación administrada en Marketplace. Seleccione **Sí** en **Enable JIT Access?** (¿Habilitar el acceso JIT?)

   ![Habilitar acceso Just-In-Time](./media/request-just-in-time-access/marketplace-enable.png)

Ha agregado un paso de configuración JIT a la interfaz de usuario y habilitado el acceso JIT en la oferta de Marketplace. Cuando los consumidores implementen la aplicación administrada, pueden [activar el acceso JIT para su instancia](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Solicitar acceso

Cuando necesite acceder a los recursos administrados del consumidor, envíe una solicitud para un rol, una hora y una duración específicos. A continuación, el consumidor debe aprobar la solicitud.

Para enviar una solicitud de acceso JIT:

1. Seleccione **Acceso de JIT** para la aplicación administrada a la que necesita acceder.

1. Seleccione **Roles elegibles** y, a continuación, **Activar** en la columna ACCIÓN del rol deseado.

   ![Activar la solicitud de acceso](./media/request-just-in-time-access/send-request.png)

1. En el formulario **Activar rol**, seleccione una hora de inicio y la duración que el rol deberá estar activo. Seleccione **Activar** para enviar la solicitud.

   ![Activar el acceso](./media/request-just-in-time-access/activate-access.png) 

1. Consulte las notificaciones para ver que la nueva solicitud de JIT se ha enviado correctamente al consumidor.

   ![Notificación](./media/request-just-in-time-access/in-progress.png)

   Ahora, debe esperar a que el consumidor [apruebe su solicitud](approve-just-in-time-access.md#approve-requests).

1. Para ver el estado de todas las solicitudes JIT de una aplicación administrada, seleccione **Acceso de JIT** e **Historial de solicitudes**.

   ![Ver estado](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conocidos

El identificador de entidad de seguridad de la cuenta que solicita acceso JIT debe incluirse explícitamente en la definición de la aplicación administrada. La cuenta no puede incluirse solo a través de un grupo especificado en el paquete. Esta limitación se corregirá en una versión futura.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información acerca de la aprobación de solicitudes de acceso JIT, consulte el tema sobre la [aprobación del acceso Just-In-Time en Azure Managed Applications](approve-just-in-time-access.md).
