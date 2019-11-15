---
title: Transferencia de la propiedad de facturación de suscripción de Azure a otra cuenta | Microsoft Docs
description: Transferencia de una propiedad de facturación de suscripción de Azure a otra cuenta y algunas preguntas frecuentes (P+F) sobre el proceso
keywords: transferencia de la suscripción de Azure, suscripción de transferencia de Azure, traslado de la suscripción de Azure a otra cuenta, cambio de propietario de la suscripción de Azure, transferencia de la suscripción de Azure a otra cuenta, facturación de transferencia de Azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: beef37b0ece9cf57c86927b9f57d85ad4a25dbd1
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746321"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferencia de la propiedad de facturación de una suscripción de Azure a otra cuenta

Es posible que quiera transferir la propiedad de facturación de la suscripción de Azure si deja la organización o que quiera que la suscripción se facture en otra cuenta. La transferencia de la propiedad de facturación a otra cuenta proporciona a los administradores del nuevo permiso de la cuenta para realizar tareas de facturación, como cambiar el método de pago, ver los gastos y cancelar la suscripción.

Si quiere mantener la propiedad de facturación, pero cambiar el tipo de suscripción, consulte [Cambio de la suscripción de Azure a otra oferta](billing-how-to-switch-azure-offer.md). Si quiere controlar quién puede administrar los recursos de la suscripción, consulte [Roles integrados en los recursos de Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Si es un cliente de Contrato Enterprise (EA), los administradores de la empresa pueden transferir la propiedad de facturación de las suscripciones entre cuentas. Para obtener más información, consulte [Transferencia de la propiedad de facturación de suscripciones de Contrato Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferencia de la propiedad de facturación de una suscripción de Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de la cuenta de facturación que tiene la suscripción que quiere transferir. Para averiguar si es administrador, consulte [Preguntas más frecuentes](#faq).

1. Busque en **Administración de costos + facturación**.

   ![Captura de pantalla que muestra la búsqueda en Azure Portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Seleccione **Suscripciones** en el panel izquierdo. Según el acceso, es posible que deba seleccionar un ámbito de facturación y, a continuación, seleccione **Suscripciones** o **Suscripciones de Azure**.

1. Seleccione **Transferir propiedad de la facturación** para la suscripción que quiera transferir.

   ![Seleccione la suscripción que va a transferir.](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Escriba la dirección de correo electrónico de un usuario que sea administrador de facturación de la cuenta que será el nuevo propietario de la suscripción.

1. Si va a transferir la suscripción a la cuenta de otro inquilino de Azure AD, seleccione si desea trasladar la suscripción al inquilino de la nueva cuenta. Para más información, consulte [Transferencia de una suscripción a una cuenta en otro inquilino de Azure AD](#transferring-subscription-to-an-account-in-another-azure-ad-tenant).

    > [!IMPORTANT]
    >
    > Si opta por mover la suscripción al inquilino de Azure AD de la nueva cuenta, todas las asignaciones de [control de acceso basado en rol (RBAC)](../role-based-access-control/overview.md) para administrar los recursos de la suscripción se quitan de forma permanente. Solo el usuario de la nueva cuenta que acepte la solicitud de transferencia tendrá acceso para administrar los recursos de la suscripción. Para obtener más información, consulte [Transferencia de la suscripción a un usuario en otro inquilino de Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md). También puede desactivar la casilla Subscription Azure AD tenant (Inquilino de Azure AD de la suscripción) para transferir la propiedad de facturación sin mover la suscripción al inquilino de la nueva cuenta. Si lo hace así, se mantendrán los permisos RBAC existentes para administrar los recursos de Azure.

    ![Envío de la página de transferencia](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Seleccione **Enviar solicitud de transferencia**.

1. El usuario recibe un correo electrónico con instrucciones para revisar la solicitud de transferencia.

   ![Correo electrónico de transferencia de suscripción enviado al destinatario](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Para aprobar la solicitud de transferencia, el usuario selecciona el vínculo en el correo electrónico y sigue las instrucciones. El usuario tendría que seleccionar un método de pago que se usará para pagar la suscripción. Además, si el usuario no tiene una cuenta de Azure, deberá registrarse para obtener una cuenta nueva.

   ![Página web de primera transferencia de suscripción](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Página web de segunda transferencia de suscripción](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Página web de segunda transferencia de suscripción](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. ¡Hecho! La suscripción ya está transferida.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transferencia de una suscripción a una cuenta en otro inquilino de Azure AD

Cuando se registra en Azure, se crea un inquilino de Azure Active Directory (AD) para usted. El inquilino representa su cuenta. Use el inquilino para administrar el acceso a sus suscripciones y recursos.

Cuando se crea una suscripción nueva, se hospeda en el inquilino de Azure AD de la cuenta. Si quiere proporcionar a otros usuarios acceso a la suscripción o a sus recursos, deberá invitarlos a unirse al inquilino. Esto ayuda a controlar el acceso a sus suscripciones y recursos.

Cuando transfiere la propiedad de facturación de la suscripción a una cuenta en otro inquilino de Azure AD, puede mover la suscripción al inquilino de la nueva cuenta. En este caso, todos los usuarios, grupos o entidades de servicio que tenían [acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md) para administrar suscripciones y sus recursos perderán el acceso. Solo el usuario de la cuenta nueva que acepte la solicitud de transferencia tendrá acceso para administrar los recursos. Para proporcionar acceso a los usuarios que originalmente tenían acceso, el nuevo propietario debería [agregar manualmente estos usuarios a la suscripción](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transferencia de suscripciones de Visual Studio, MPN y Desarrollo/pruebas - Pago por uso

Las suscripciones de Visual Studio y Microsoft Partner Network tienen crédito de Azure periódico mensual asociado. Al transferir estas suscripciones, el crédito no está disponible en la cuenta de facturación de destino. La suscripción usa el crédito de la cuenta de facturación de destino. Por ejemplo, si Roberto transfiere una suscripción de Visual Studio Enterprise a la cuenta de Julia el 9 de septiembre y Julia acepta a la transferencia. Una vez completada la transferencia, la suscripción empieza a usar el crédito de la cuenta de Julia. El crédito se restablecerá el día 9 de cada mes.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transferencia de la propiedad de facturación de suscripciones de Contrato Enterprise (EA)

El administrador de la empresa puede transferir la propiedad de las suscripciones entre cuentas de una inscripción. Para más información, vea [Transferir propiedad de cuenta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) en el portal de EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Pasos siguientes después de aceptar la propiedad de facturación

Si ha aceptado la propiedad de facturación de una suscripción de Azure, se recomienda que revise los pasos siguientes:

1. Revise y actualice el administrador del servicio, los coadministradores y otros roles de RBAC. Para obtener más información, vea [Agregar o cambiar los administradores de la suscripción de Azure](billing-add-change-azure-subscription-administrator.md) y [Administración del acceso mediante RBAC y Azure Portal](../role-based-access-control/role-assignments-portal.md).
1. Actualice las credenciales asociadas a los servicios de esta suscripción:
   1. Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](../cloud-services/cloud-services-certs-create.md)
   1. Claves de acceso para servicios como Almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).
   1. Credenciales de acceso remoto para servicios como Azure Virtual Machines.
1. Si trabaja con un asociado, considere la posibilidad de actualizar el identificador del asociado en esta suscripción. Puede actualizar el identificador de asociado en [Azure Portal](https://portal.azure.com). Para más información, consulte [Vinculación de un Id. de partner a cuentas de Azure](billing-partner-admin-link-started.md).

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipos de suscripciones admitidos

La transferencia de suscripciones en Azure Portal está disponible para los tipos de suscripción que se enumeran a continuación. Actualmente la transferencia de suscripciones de [Evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) o [Azure bajo licencia Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) no se admite. Para ver una solución alternativa, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md). Para transferir otras suscripciones, como [Patrocinio](https://azure.microsoft.com/offers/ms-azr-0036p/) o planes de soporte técnico, [póngase en contacto con el soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Suscriptores de Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [A través del portal de EA](#EA).

\*\* Solo se admite para las cuentas que se crean durante el registro en el sitio web de Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Preguntas más frecuentes (P+F) de los emisores

Estas Preguntas más frecuentes se aplican a los usuarios que van a transferir la propiedad de facturación de una suscripción de Azure a otra cuenta.

### <a name="whoisaa"></a> ¿Quién es el administrador de facturación de una cuenta?

Un administrador de facturación es una persona que tiene permiso para administrar la facturación de una cuenta. Está autorizado para tener acceso a la facturación en [Azure Portal](https://portal.azure.com) y realizar diversas tareas de facturación, como crear suscripciones, ver y pagar facturas o actualizar los métodos de pago.

Para identificar las cuentas de las que es administrador de facturación, siga estos pasos:

1. Visite la [página Administración de costos + facturación en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Seleccione **Todos los ámbitos de facturación** en el panel izquierdo.
1. La página de suscripciones enumera todas las suscripciones de las que es administrador de facturación.

Si no está seguro de quién es el administrador de cuenta de la suscripción, use los pasos siguientes para averiguarlo.

1. Visite la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
1. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>¿Se transfiere todo? ¿Incluidos los grupos de recursos, las máquinas virtuales, los discos y otros servicios en ejecución?

Todos los recursos, como las VM, los discos y los sitios web, se transfieren a la nueva cuenta. Sin embargo, si transfiere la suscripción a una cuenta en otro inquilino de Azure AD, los [roles de administrador](billing-add-change-azure-subscription-administrator.md) y las asignaciones de [Control de acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md) de la suscripción [no se transfieren](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Tampoco se transferirán los [registros de aplicaciones](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) ni otros servicios específicos del inquilino junto con la suscripción.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>¿Puedo transferir la propiedad a una cuenta en otro país?
Las transferencias entre países no pueden realizarse en Azure Portal. Para transferir la suscripción entre países, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Soy el administrador de dos cuentas. ¿Puedo transferir una suscripción de una de mis cuentas a otra?
Sí, puede transferir la suscripción entre las cuentas. En teoría, las cuentas se consideran cuentas de dos usuarios distintos, por lo que puede usar los pasos anteriores para transferir las suscripciones entre las cuentas.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>¿Provocan las transferencias de suscripciones un tiempo de inactividad en el servicio?

Si transfiere una suscripción a una cuenta en el mismo inquilino de Azure AD, los recursos que se ejecutan en la suscripción no se ven afectados. Sin embargo, si transfiere la suscripción a una cuenta de otro inquilino y decide mover la suscripción al inquilino, todos los usuarios, grupos y entidades de servicio que tenían [acceso basado en roles (RBAC)](../role-based-access-control/overview.md) para administrar recursos de la suscripción perderán el acceso. Esto puede dar lugar a un tiempo de inactividad del servicio.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>¿Los usuarios de la nueva cuenta tienen acceso al historial de facturación y uso?

La única información disponible para los usuarios de la nueva cuenta es el costo del último mes de la suscripción. El resto del historial de facturación y uso no se transfiere con la suscripción.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>¿Cómo se migran los datos y servicios de una suscripción de Azure a otra?

Si no se puede transferir la propiedad de suscripción, puede migrar manualmente los recursos. Consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Si transfiero una suscripción de Visual Studio o Microsoft Partner Network, ¿mi crédito pasa con la suscripción a la cuenta nueva?

No, el crédito no está disponible en la cuenta nueva. El usuario que acepta la solicitud de transferencia debe tener una licencia de Visual Studio para aceptar la solicitud de transferencia. La suscripción usa el crédito de Visual Studio que está disponible en la cuenta del usuario. Para más información, consulte [Transferencia de Visual Studio, Microsoft Partner Network (MPN) y suscripciones de pago por uso para desarrollo/pruebas](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Preguntas más frecuentes (P+F) de los destinatarios

Estas preguntas frecuentes se aplican a los usuarios que van a asumir la propiedad de facturación de una suscripción de Azure procedente de otra cuenta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Si asumo la propiedad de la facturación de una suscripción de otra organización, ¿los usuarios de esa cuenta seguirán teniendo acceso a mis recursos?

Sí. Sin embargo, si la cuenta se encuentra en un inquilino de Azure AD diferente del inquilino de la suscripción y el usuario que envió la solicitud de transferencia mueve la suscripción al inquilino de su cuenta, se quitarán los [roles de administrador](billing-add-change-azure-subscription-administrator.md) y las asignaciones de [control de acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md). Para ver los usuarios que tienen acceso [RBAC](../role-based-access-control/overview.md) para administrar recursos de la suscripción, siga estos pasos:

1. Visite la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que quiere comprobar y, a continuación, seleccione **Control de acceso (IAM)** desde el panel izquierdo.
1. En la parte superior de la página, seleccione **Asignaciones de roles**. La página de asignaciones de roles enumera todos los usuarios que tienen acceso RBAC en la suscripción.

Incluso si las asignaciones de [control de acceso basado en roles (RBAC)](../role-based-access-control/role-assignments-portal.md) se quitan durante la transferencia, es posible que los usuarios de la cuenta del propietario original sigan teniendo acceso a la suscripción a través de algunos mecanismos de seguridad, entre los que se incluyen:

* Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para más información, consulte [Create and Upload a Management Certificate for Azure](../cloud-services/cloud-services-certs-create.md) (Creación y actualización de un certificado de administración para Azure).
* Claves de acceso para servicios como Almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).
* Credenciales de acceso remoto para servicios como Azure Virtual Machines.

El destinatario debe restringir el acceso a sus recursos y considerar la actualización de todos los secretos asociados al servicio. La mayoría de los recursos se pueden actualizar mediante el uso de los siguientes pasos:

  1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
  2. En el menú Concentrador, seleccione **Todos los recursos**.
  3. Seleccione el recurso.
  4. En la página de recursos, haga clic en **Configuración**. Aquí puede ver y actualizar los secretos existentes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Si asumo la propiedad de facturación de una suscripción en medio del ciclo de facturación, ¿pago todo el ciclo de facturación?

La cuenta es responsable del pago de cualquier uso que se notifica desde el momento de la transferencia en adelante. Puede haber una parte del uso que se realizara antes de la transferencia, pero que se notificara con posterioridad. El uso se incluye en la factura de la cuenta.

### <a name="can-i-use-a-different-payment-method"></a>¿Puedo usar otro método de pago?

Sí. Al aceptar la solicitud de transferencia, puede seleccionar un método de pago existente que esté vinculado a la cuenta o agregar uno nuevo.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>¿Cómo puedo transferir la propiedad de la cuenta de suscripción del Contrato Enterprise (EA) si el propietario de la cuenta original ya no está en la organización?

El administrador de empresa puede actualizar la propiedad de la cuenta para cualquier cuenta incluso después de que el propietario de la cuenta original ya no forme parte de la organización. Para ello, puede seguir las instrucciones de [Transferencia de la propiedad de la cuenta para todas las suscripciones](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) en EA Portal.

## <a name="troubleshooting"></a>solución de problemas

### <a id="no-button"></a>¿Por qué no veo el botón "Transfer subscription" (Transferir suscripción)?

La transferencia de suscripción de autoservicio no está disponible para su cuenta de facturación. Actualmente, no se admite la transferencia de la propiedad de facturación de suscripciones en cuentas de Contrato Enterprise (EA) en Azure Portal. Tampoco admiten la transferencia de la propiedad de facturación las cuentas de Contrato de cliente de Microsoft que se crean mientras trabaja con un representante de Microsoft.

### <a id="no-button"></a> ¿Por qué mi tipo de suscripción no admite la transferencia?

No todos los tipos de suscripciones admiten la transferencia de la propiedad de facturación. Para ver la lista de tipos de suscripción que admiten transferencias, consulte [Tipos de suscripción admitidos](#supported-subscription-types).

### <a id="no-button"></a> ¿Por qué recibo un error de acceso denegado al intentar transferir la propiedad de facturación de una suscripción?

Verá este error si intenta transferir una suscripción de un plan de Microsoft Azure y no tiene el permiso necesario. Para transferir una suscripción de un plan de Microsoft Azure, debe ser propietario o colaborador en la sección de la factura en la que se factura la suscripción. Para más información, consulte [Administrar las suscripciones para la sección de factura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- Revise y actualice el administrador del servicio, los coadministradores y otros roles de RBAC. Para obtener más información, vea [Agregar o cambiar los administradores de la suscripción de Azure](billing-add-change-azure-subscription-administrator.md) y [Administración del acceso mediante RBAC y Azure Portal](../role-based-access-control/role-assignments-portal.md).
