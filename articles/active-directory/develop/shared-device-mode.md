---
title: Modo de dispositivo compartido para dispositivos Android | Azure
description: Más información sobre el modo de dispositivo compartido, que permite a los trabajadores de primera línea compartir un dispositivo Android
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: babbaf75b91dfb870ba5001d23f62c5461a28218
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030951"
---
# <a name="shared-device-mode-for-android-devices"></a>Modo de dispositivo compartido para dispositivos Android

> [!NOTE]
> Esta característica está en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Los trabajadores de primera línea, como los asociados comerciales, los miembros de la tripulación de vuelos y los trabajadores de servicio de campo, suelen usar un dispositivo móvil compartido para realizar su trabajo. Esto resulta problemático cuando comienzan a compartir las contraseñas o los números PIN para acceder a los datos de clientes y empresas en el dispositivo compartido.

El modo de dispositivo compartido le permite configurar un dispositivo Android para que varios empleados puedan compartirlo fácilmente. Los empleados pueden iniciar sesión y acceder a la información de los clientes rápidamente. Cuando terminan su turno o tarea, pueden cerrar sesión en el dispositivo y estará inmediatamente listo para que lo use el siguiente empleado.

El modo de dispositivo compartido también proporciona la administración del dispositivo respaldada por la identidad de Microsoft.

Para crear una aplicación en modo de dispositivo compartido, los desarrolladores y los administradores de dispositivos en la nube trabajan juntos:

- Los desarrolladores escriben una aplicación de una sola cuenta (las aplicaciones de varias cuentas no se admiten en el modo de dispositivo compartido), agregan `"shared_device_mode_supported": true` a la configuración de la aplicación y escriben código para controlar aspectos como el cierre de sesión del dispositivo compartido.
- Los administradores de dispositivos preparan el dispositivo que se va a compartir mediante la instalación de la aplicación de autenticación y el establecimiento del dispositivo en modo compartido mediante dicha aplicación. Solo los usuarios que se encuentran en el rol [administrador de dispositivos en la nube](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) pueden poner un dispositivo en modo compartido mediante la [aplicación de autenticación](https://www.microsoft.com/account/authenticator). Puede configurar la pertenencia de los roles de la organización en Azure Portal mediante: **Azure Active Directory** > **Roles y administradores**  > **Administrador de dispositivos en la nube**.

 Este artículo se centra principalmente en lo que los desarrolladores deben considerar.

## <a name="single-vs-multiple-account-applications"></a>Aplicaciones de una sola cuenta y de varias cuentas

Las aplicaciones escritas mediante el SDK de la Biblioteca de autenticación de Microsoft (MSAL) pueden administrar una sola cuenta o varias cuentas. Para más información, consulte [el modo de una sola cuenta o el modo de varias cuentas](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Las características de la plataforma de identidad de Microsoft disponibles para la aplicación varían en función de si la aplicación se ejecuta en modo de una sola cuenta o en modo de varias cuentas.

**Las aplicaciones en modo de dispositivo compartido solo funcionan en el modo de una sola cuenta**.

> [!IMPORTANT]
> Las aplicaciones que solo admiten el modo de varias cuentas no se pueden ejecutar en un dispositivo compartido. Si un empleado carga una aplicación que no admite el modo de una sola cuenta, no se ejecutará en el dispositivo compartido.
>
> Las aplicaciones escritas antes de que se publicara el SDK de MSAL se ejecutan en modo de varias cuentas y deben actualizarse para admitir el modo de una sola cuenta antes de poder ejecutarse en un dispositivo de modo compartido.

**Compatibilidad con una sola cuenta y con varias cuentas**

La aplicación se puede crear para admitir la ejecución tanto en dispositivos personales como en dispositivos compartidos. Si la aplicación admite actualmente varias cuentas y desea admitir el modo de dispositivo compartido, agregue compatibilidad para el modo de una sola cuenta.

También puede que desee que la aplicación cambie su comportamiento en función del tipo de dispositivo en el que se está ejecutando. Use `ISingleAccountPublicClientApplication.isSharedDevice()` para determinar cuándo se debe ejecutar en el modo de una sola cuenta.

Hay dos interfaces diferentes que representan el tipo de dispositivo en el que se encuentra la aplicación. Cuando se solicita una instancia de aplicación de la factoría de aplicaciones de MSAL, se proporciona el objeto de aplicación correcto automáticamente.

En el modelo de objetos siguiente se muestra el tipo de objeto que puede recibir y lo que significa en el contexto de un dispositivo compartido:

![modelo de herencia de aplicaciones cliente públicas](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Tendrá que realizar una comprobación de tipos y realizar la conversión a la interfaz adecuada cuando obtenga el objeto `PublicClientApplication`. El código siguiente comprueba el modo de varias cuentas o el modo de una sola cuenta, y convierte el objeto de aplicación de forma adecuada:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Las siguientes diferencias se aplican en función de si la aplicación se ejecuta en un dispositivo compartido o personal:

|  | Dispositivo en modo compartido  | Dispositivo personal |
|---------|---------|---------|
| **Cuentas**     | Una sola cuenta | Varias cuentas |
| **Inicio de sesión** | Global | Global |
| **Cierre de sesión** | Global | Cada aplicación puede controlar si el cierre de sesión es local para la aplicación o para la familia de aplicaciones. |
| **Tipos de cuenta admitidos** | Solo cuentas profesionales | Se admiten cuentas personales y profesionales  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>¿Por qué es posible que desee admitir solo el modo de una sola cuenta?

Si va a escribir una aplicación que solo utilizarán los trabajadores de primera línea mediante un dispositivo compartido, se recomienda escribirla para que admita únicamente el modo de una sola cuenta. Esto incluye a la mayoría de las aplicaciones que se centran en tareas, como las de registros médicos, facturas y la mayor parte de las de línea de negocio. Solo admitir el modo de una sola cuenta simplifica el desarrollo porque no necesitará implementar las características adicionales que forman parte de las aplicaciones de varias cuentas.

## <a name="what-happens-when-the-device-mode-changes"></a>¿Qué ocurre cuando el modo del dispositivo cambia?

Si la aplicación se ejecuta en el modo de varias cuentas y un administrador coloca el dispositivo en el modo de dispositivo compartido, todas las cuentas del dispositivo se borrarán de la aplicación y esta pasará al modo de una sola cuenta.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Cierre de sesión del dispositivo compartido y el ciclo de vida de la aplicación general

Cuando un usuario cierre la sesión, deberá tomar medidas para proteger la privacidad y los datos del usuario. Por ejemplo, si va a crear una aplicación de historias clínicas, querrá asegurarse de que cuando el usuario cierra la sesión, las historias de pacientes mostradas anteriormente se borran. La aplicación debe estar preparada para este fin y comprobarse cada vez que entra en primer plano.

Cuando la aplicación usa MSAL para cerrar la sesión del usuario en una aplicación que se ejecuta en el dispositivo que está en modo compartido, la cuenta con sesión iniciada y los tokens almacenados en caché se quitan tanto de la aplicación como del dispositivo.

En el diagrama siguiente se muestra el ciclo de vida de la aplicación general y los eventos comunes que se pueden producir mientras se ejecuta la aplicación. El diagrama abarca desde el momento en que se inicia una actividad, el inicio y cierre de sesión en una cuenta y el modo en que se ajustan los eventos, como pueden ser la pausa, reanudación y detención de la actividad.

![Ciclo de vida de la aplicación de dispositivo compartido](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Pasos siguientes

Pruebe el tutorial [Uso del modo de dispositivo compartido en la aplicación Android](tutorial-v2-shared-device-mode.md), en el que se muestra cómo ejecutar una aplicación de trabajo de primera línea en un dispositivo Android en modo compartido.
