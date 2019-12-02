---
title: Solución de problemas de inicio de sesión en la suscripción a Azure
description: Ayuda a resolver problemas por los que no se puede iniciar sesión en Azure Portal o en el Centro de cuentas de Azure.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 7b467e42553d992231f8f5d45afcff37f077a9d2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223406"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Solución de problemas de inicio de sesión en la suscripción a Azure

Esta guía ayuda a resolver problemas por los que no se puede iniciar sesión en Azure Portal o en el Centro de cuentas de Azure. 

## <a name="issues"></a>Issues

### <a name="page-hangs-in-the-loading-status"></a>La página se bloquea en el estado de carga

Si la página del explorador de Internet se bloquea, pruebe cada uno de los siguientes procedimientos hasta que pueda acceder a Azure Portal.

- Actualice la página.
- Use un explorador de Internet diferente.
- Use el modo de exploración privado para el explorador. En Internet Explorer: Haga clic en **Herramientas** > **Seguridad** > **Exploración de InPrivate** y, luego, examine e inicie sesión en [Azure Portal](https://portal.azure.com/) o en el [Centro de cuentas de Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Inicia sesión automáticamente con un usuario diferente

Este problema puede producirse si usa más de una cuenta de usuario en un explorador de Internet.

Para solucionar este problema, pruebe uno de los métodos siguientes:

- Borre la memoria caché y elimine las cookies de Internet. En Internet Explorer, haga clic en **Herramientas** > **Opciones de Internet** > **Eliminar**. Asegúrese de que las casillas de archivos temporales, cookies, contraseñas e historial de exploración están activadas y luego haga clic en Eliminar.
- Restablezca la configuración de Internet Explorer para revertir la configuración personal que haya realizado. Haga clic en **Herramientas** > **Opciones de Internet** > **Avanzadas**> active la casilla **Eliminar configuración personal** > **Restablecer**.
- Use el modo de exploración privado para el explorador. En Internet Explorer:  Haga clic en **Herramientas** > **Seguridad** > **Exploración de InPrivate** y, luego, examine e inicie sesión en [Azure Portal](https://portal.azure.com/) o en el [Centro de cuentas de Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Puedo iniciar sesión, pero veo el mensaje *No se encontró ninguna suscripción*

Este problema se produce si seleccionó el directorio equivocado, o si la cuenta no tiene permisos suficientes.

**Escenario 1:** El mensaje de error se recibe en [Azure Portal](https://portal.azure.com/)

Para corregir este problema:

- Asegúrese de que se seleccionó el directorio correcto de Azure haciendo clic en la cuenta pertinente en la esquina superior derecha.
- Si ha seleccionado el directorio correcto de Azure, pero sigue recibiendo el mensaje de error, [agregue la cuenta como propietario](billing-add-change-azure-subscription-administrator.md).

**Escenario 2:** El mensaje de error se recibe en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions)

Compruebe si la cuenta que ha usado es el administrador de cuentas. Para comprobar quién es el administrador de cuentas, siga estos pasos:

1. Inicie sesión en la [vista de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.

3. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .

## <a name="additional-help-resources"></a>Más recursos de ayuda

Otros artículos de solución de problemas relativos a la facturación y las suscripciones de Azure

- [Tarjeta rechazada](billing-troubleshoot-declined-card.md)
- [Problemas de registro de suscripción](billing-troubleshoot-azure-sign-up.md)
- [No se han encontrado suscripciones](billing-no-subscriptions-found.md)
- [Se ha deshabilitado la vista de costos empresariales](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Póngase en contacto con nosotros para obtener ayuda

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

- [Documentación de Facturación de Azure](index.md)
