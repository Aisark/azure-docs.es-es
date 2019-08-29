---
title: 'Administración de cuentas: solución de VMware en Azure del portal de CloudSimple'
description: En este artículo se describe cómo administrar cuentas en la solución de VMware en Azure del portal de CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563847"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Administración de cuentas en la solución de VMware en Azure del portal de CloudSimple

Cuando se crea el servicio CloudSimple, se crea una cuenta en CloudSimple. La cuenta se asocia a su suscripción de Azure donde se encuentra el servicio. Todos los usuarios con roles de propietario y de colaborador en la suscripción tienen acceso al portal de CloudSimple. El identificador de suscripción de Azure y el identificador de inquilino asociados con el servicio CloudSimple se encuentran en la página Cuentas.

Para administrar las cuentas en el portal de CloudSimple, [acceda al portal](access-cloudsimple-portal.md) y seleccione **Cuenta** en el menú lateral.

Seleccione **Resumen** para ver información sobre la configuración de CloudSimple de su empresa. Se mostrará la capacidad actual de la configuración de la nube, incluido el número de nubes privadas, el almacenamiento total, la configuración del clúster de vSphere, el número de nodos y el número de núcleos de proceso. Se incluye un vínculo para comprar nodos adicionales si la configuración actual no satisface todas sus necesidades.

## <a name="email-alerts"></a>Alertas por correo electrónico

Puede agregar direcciones de correo electrónico de las personas a las que quiere notificar los cambios en la configuración de la nube privada.

1. En el área **Additional email alerts** (Alertas de correo electrónico adicionales), haga clic en **Agregar nueva**.
2. Escriba la dirección de correo electrónico.
3. Presione ENTRAR.  

Para quitar una entrada, haga clic en **X**.

## <a name="cloudsimple-operator-access"></a>Acceso de operador de CloudSimple

La configuración de acceso de operador ayuda a CloudSimple a solucionar problemas, ya que permite que un ingeniero de soporte técnico inicie sesión en el portal de CloudSimple.  Esta opción está habilitada de forma predeterminada. Todas las acciones que el ingeniero de soporte técnico lleve a cabo cuando haya iniciado sesión en su cuenta de cliente se registrarán y estarán disponibles para su revisión en la página **Actividad** > **Auditoría**.

Haga clic en el botón de alternancia **CloudSimple operator access enabled** (Acceso de operador de CloudSimple habilitado) para habilitar o deshabilitar el acceso.
