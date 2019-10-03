---
title: 'Migración de cuentas de Cloud Partner Portal al centro de partners: Marketplace comercial para Azure'
description: Cómo migrar su cuenta de CPP al centro de partners. - Marketplace comercial para Azure
author: ChJenk
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 6887430cad06ad33434f0f01e741979014b4558b
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219578"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migración de cuentas de Cloud Partner Portal al centro de partners

Si tiene una cuenta de Cloud Partner Portal (CPP) existente, la configuración de la cuenta debe migrarse al Centro de partners.

## <a name="account-migration-process"></a>Proceso de migración de cuentas

Si es un usuario con el rol de propietario en CPP para una cuenta determinada, se muestra un banner en amarillo en la página Perfil de publicación. Puede que su caso se encuadre en uno de estos:

- Su cuenta ya se ha migrado y está listo para administrar su configuración de cuenta en el Centro de partners.
- Su cuenta no se ha migrado al Centro de partners y es necesario que tome medidas adicionales.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Su cuenta se ha migrado al Centro de partners.

En todas las cuentas en las que se haya completado la migración desde el CPP al Centro de partners, la administración de cuentas se realizará en este último. Los cambios, como la adición o la eliminación de usuarios, se volverán a sincronizar con CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Todavía no ha migrado su cuenta al Centro de partners.

Haga clic en el banner para iniciar el proceso de migración de la cuenta. Deberá escribir los siguientes elementos:

1. Dirección de correo electrónico del trabajo: <br> <br> La mayoría de las veces se inicia sesión con la dirección de correo electrónico que se usa para iniciar sesión en CPP. En algunos casos, se debe usar una dirección de correo electrónico diferente:

    * Cuenta de Microsoft: si la cuenta de CPP es una cuenta Microsoft, especifique una dirección de correo electrónico del trabajo válida asociada al inquilino para el que está registrado el identificador de Microsoft Partner Network (MPN). Para más información, consulte [Registro en el programa Microsoft Partner Network](#sign-up-for-microsoft-partner-network-program).

    * El inquilino no coincide: si su dirección de correo electrónico del trabajo no pertenece al inquilino asociado al identificador de Microsoft Partner Network de la cuenta de CPP, se producirá un error. Para saltarse este error, escriba una dirección de correo electrónico asociada con el inquilino. En un mensaje de error se proporcionará el nombre del inquilino.

2. Registro en el programa Microsoft Partner Network

    En caso de que la cuenta de CPP no tenga un identificador de Microsoft Partner Network o si lo tiene, pero no es válido, debe registrarse en el programa Microsoft Partner Network como parte del proceso de activación.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registro en el programa Microsoft Partner Network

Las empresas que deseen asociarse a Microsoft deben unirse a Microsoft Partner Network (MPN) y obtener un identificador de MPN. Si ya es miembro de Microsoft Partner Network y tiene un identificador de MPN, tenga a mano la información, ya que la necesitará durante el proceso de activación de la cuenta.  

Si no es miembro de Microsoft Partner Network, puede [unirse aquí](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) para obtener un identificador de MPN. Anote su identificador de MPN, ya que deberá escribirlo durante el proceso de activación de la cuenta.

Para obtener más información sobre Microsoft Partner Network, consulte [Únase a Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) en el sitio web del asociado. Para obtener más información sobre las ventajas de ISV en Microsoft Partner Network, consulte el [centro de recursos de ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Traslado de ofertas de Dynamics 365 y PowerApps al Centro de partners

Para optimizar la administración de cuentas y ofertas para Dynamics 365 Customer Engagement, PowerApps y Dynamics 365 Operations, las ofertas se han trasladado al [Centro de partners](https://partner.microsoft.com/). El traslado garantiza que el mismo contenido esté disponible tanto para los catálogos públicos como para los catálogos de vendedores.

Para obtener información sobre lo que debe hacerse antes del **15 de octubre de 2019** para sus ofertas de Dynamics 365 Customer Engagement, PowerApps y Dynamics 365 Operations, siga las instrucciones a continuación.

> [!NOTE]
> Esto no se aplica a las ofertas de Dynamics 365 Business Central.  

1. Si la cuenta de pertenencia de MPN se creó originalmente en Partner Membership Center (PMC), inicie sesión en el [Centro de partners](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar que la cuenta se ha migrado. Si ve una pantalla de perfil con el identificador de MPN, significa que ya está listo para continuar. Si no es así, debe iniciar la migración de la cuenta siguiendo las indicaciones de [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Consulte [Soporte técnico](https://partner.microsoft.com/support?issueid=100-0077) si necesita ayuda.
2. Vaya a [Introducción al portal de Marketplace comercial en el Centro de partners](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Si ve "Marketplace comercial" en el panel de navegación izquierdo, significa que está inscrito y debe continuar con el paso siguiente. Si no es así, [inscríbase ahora en el Marketplace comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv).
3. Confirme que sus ofertas están en AppSource. Para ello, [busque sus ofertas](https://appsource.microsoft.com/). Si las ofertas ya están en AppSource, continúe con el paso siguiente. En el caso de las ofertas que no estén en AppSource, cree una [nueva oferta de Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) o una [nueva oferta de Dynamics 365 Operations](create-new-operations-offer.md).
4. En la [página de acuerdos](https://partner.microsoft.com/dashboard/account/agreements) del Centro de partners, asegúrese de que ha revisado y aceptado **anexo de ISV de Business Applications**.
5. En la [configuración de cuenta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) del Centro de partners, asegúrese de que su información de facturación está completa.
6. Envíe cada oferta nueva y existente de certificación y publicación, incluso si las ofertas se han certificado previamente.
    * Complete las pantallas de información, incluido el suministro de su aplicación para la certificación, así como la información de marketing. Seleccione **Enviar** (esquina superior derecha de la pantalla) antes del **15 de octubre de 2019**. Estos pasos deben completarse para que la disponibilidad de la oferta no se vea afectada.
    * Si es apto, puede solicitar participar en el nivel premium durante este proceso.
    * La certificación o la recertificación requieren que la aplicación sea compatible con la versión más reciente de nuestra plataforma Business Applications.
    * Una vez aprobada su aplicación, recibirá un correo electrónico para volver a la oferta y seleccionar "transmitir" a fin de habilitar la oferta que se transmitirá en Microsoft AppSource.

## <a name="additional-resources"></a>Recursos adicionales

Únase a la [llamada de la comunidad de ISV de Dynamics](https://aka.ms/DynamicsISV-CommunityCall) para obtener soporte técnico y actualizaciones.

Si necesita ayuda para publicar, certificar o administrar sus ofertas de Marketplace, [envíe una incidencia de soporte técnico](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Pasos siguientes

- [Administración de la cuenta de Marketplace comercial en el Centro de partners](./manage-account.md)
