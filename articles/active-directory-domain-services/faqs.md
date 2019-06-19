---
title: Preguntas más frecuentes - Azure Active Directory Domain Services | Microsoft Docs
description: Preguntas más frecuentes sobre Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 89930bcc4f49a406586c2770f65cc65f81387302
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246110"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Preguntas más frecuentes (P+F)
Esta página responde a las preguntas más frecuentes acerca de Azure Active Directory Domain Services. Siga comprobando si hay actualizaciones.

## <a name="troubleshooting-guide"></a>Guía de solución de problemas
Consulte nuestra [Guía de solución de problemas](troubleshoot.md) para obtener soluciones a los problemas más habituales al configurar o administrar Azure AD Domain Services.

## <a name="configuration"></a>Configuración
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>¿Se pueden crear varios dominios administrados para un único directorio de Azure AD?
No. Solo se puede crear un único dominio administrado atendido por Azure AD Domain Services para un único directorio de Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>¿Puedo habilitar Azure AD Domain Services en una red virtual de Azure Resource Manager?
Sí. Azure AD Domain Services se puede habilitar en una red virtual de Azure Resource Manager. Ya no se admiten redes virtuales de Azure clásicas para crear dominios administrados.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>¿Puedo migrar mi dominio administrado existente de una red virtual clásica a una red virtual de Resource Manager?
Actualmente, no. En el futuro Microsoft ofrecerá un mecanismo para migrar un dominio administrado existente de una red virtual clásica a una red virtual de Resource Manager.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>¿Puedo habilitar Azure AD Domain Services en una suscripción de Azure CSP (proveedor de soluciones en la nube)?
Sí. Consulte cómo habilitar [Azure AD Domain Services en las suscripciones de Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>¿Puedo habilitar Azure AD Domain Services en un directorio de Azure AD federado? No sincronizo los valores hash de contraseña con Azure AD. ¿Puedo habilitar Azure AD Domain Services para este directorio?
No. Azure AD Domain Services necesita acceso a los hashes de contraseña de las cuentas de usuario para autenticar a los usuarios a través de NTLM o Kerberos. En un directorio federado, los hashes de contraseña no se almacenan en el directorio de Azure AD. Por tanto, Azure AD Domain Services no funciona con estos directorios de Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>¿Puedo hacer que Azure AD Domain Services esté disponible en varias redes virtuales dentro de mi suscripción?
El propio servicio no admite directamente este escenario. Su dominio administrado está disponible en una sola red virtual a la vez. Sin embargo, puede configurar la conectividad entre varias redes virtuales con el fin de exponer Azure AD Domain Services a otras redes virtuales. Consulte cómo puede [conectar redes virtuales en Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>¿Puedo habilitar Servicios de dominio de Azure AD mediante PowerShell?
Sí. Consulte [cómo puede habilitar Azure AD Domain Services mediante PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>¿Puedo habilitar Azure AD Domain Services mediante la plantilla de Resource Manager?
No, por el momento no es posible habilita Azure AD Domain Services con una plantilla. En su lugar, use PowerShell y vea [cómo habilitar Azure AD Domain Services con PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>¿Puedo agregar controladores de dominio a un dominio administrado de Servicios de dominio de Azure AD?
No. El dominio de Servicios de dominio de Azure AD es un dominio administrado. No es necesario aprovisionar, configurar o administrar controladores de dominio para este dominio, Microsoft proporciona estas actividades de administración como un servicio. Por lo tanto, no podrá agregar controladores de dominio adicionales (ni de lectura y escritura ni de solo lectura) para el dominio administrado.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>¿Pueden los usuarios invitados a mi directorio usar Azure AD Domain Services?
No. Los usuarios invitados a su directorio de Azure AD mediante el proceso de invitación [B2B de Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) se sincronizan en el dominio administrado de Azure AD Domain Services. Sin embargo, las contraseñas para estos usuarios no se almacenan en el directorio de Azure AD. Por lo tanto, Azure AD Domain Services no tiene ninguna manera de sincronizar los códigos hash de Kerberos y NTLM para estos usuarios en el dominio administrado. Como resultado, estos usuarios no pueden iniciar sesión en el dominio administrado o unir equipos a este.

## <a name="administration-and-operations"></a>Administración y operaciones
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>¿Puedo conectarme al controlador de dominio para mi dominio administrado mediante Escritorio remoto?
No. Los administradores de inquilinos no tienen privilegios para conectarse a controladores de dominio en el dominio administrado mediante el Escritorio remoto. Los miembros del grupo "Administradores de controlador de dominio de AAD" pueden administrar el dominio administrado usando las herramientas de administración de AD, como el Centro de administración de Active Directory (ADAC) o AD PowerShell. Estas herramientas se instalan mediante la característica "Herramientas de administración de servidor remoto" en un servidor de Windows unido al dominio administrado.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>He habilitado Servicios de dominio de Azure AD ¿Qué cuenta de usuario utilizo para unir máquinas a este dominio?
Los miembros del grupo administrativo "Administradores de controlador de dominio de AAD" pueden unir máquinas mediante dominio. Además, los miembros de este grupo se les concede acceso mediante Escritorio remoto a las máquinas que se han unido al dominio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>¿Puedo tener privilegios de administrador de dominio para el dominio administrado proporcionado por Azure AD Domain Services?
No. No se le conceden privilegios administrativos en el dominio administrado. Los privilegios "Administrador de dominio" y "Administrador de organización" no estarán disponibles dentro del dominio. A los miembros de los grupos de administradores de dominio o administradores de organización dentro del directorio local de Active Directory tampoco se les concederán privilegios de administrador de dominio o de organización en el dominio administrado.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>¿Puedo modificar pertenencias a grupos mediante LDAP u otras herramientas administrativas de AD en dominios administrados?
No. Las pertenencias a grupos en dominios ofrecidos por Servicios de dominio de Azure AD no se pueden modificar. Lo mismo se aplica para los atributos de usuario. Sin embargo, puede cambiar las pertenencias a grupos o los atributos de usuario en Azure AD o en el dominio local. Dichos cambios se sincronizarán automáticamente con Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>¿Cuánto tardan los cambios realizados en el directorio de Azure AD en estar visibles en mi dominio administrado?
Los cambios realizados en su directorio de Azure AD mediante la interfaz de usuario de Azure AD o PowerShell se sincronizan con el dominio administrado. Este proceso de sincronización se ejecuta en segundo plano. Tras completar la sincronización inicial, los cambios realizados en Azure AD suelen tardar unos 20 minutos en aparecer reflejados en el dominio administrado.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>¿Puedo extender el esquema del dominio administrado por Azure AD Domain Services?
No. Microsoft administra el esquema del dominio administrado. No se admiten extensiones de esquema en los Servicios de dominio de Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>¿Puedo modificar o agregar registros DNS en mi dominio administrado?
Sí. Los miembros del grupo "Administradores de controlador de dominio de AAD" reciben privilegios de "Administrador de DNS" con el fin de modificar los registros DNS en el dominio administrado. Pueden utilizar la consola de administrador de DNS en un equipo que ejecuta Windows Server unido al dominio administrado para administrar DNS. A fin de utilizar la consola de administrador de DNS, instale "Herramientas del servidor DNS", que forma parte de la característica opcional "Herramientas de administración remota del servidor" en el servidor. Hay más información sobre [utilidades para administrar, supervisar y solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) disponible en TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>¿Qué es la directiva de duración de la contraseña en un dominio administrado?
La duración predeterminada de la contraseña en un dominio administrado de Azure AD Domain Services es de 90 días. Esta duración de la contraseña no está sincronizada con la duración de la contraseña configurada en Azure AD. Por lo tanto, podría darse una situación donde las contraseñas de los usuarios hayan expirado en el dominio administrado, pero sigan siendo válidas en Azure AD. En tales escenarios, los usuarios deben cambiar sus contraseñas en Azure AD y la nueva contraseña se sincronizará con el dominio administrado. Además, los atributos "password-does-not-expire" y "user-must-change-password-at-next-logon" de las cuentas de usuario no se sincronizan con el dominio administrado.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>¿Azure AD Domain Services proporciona protección de bloqueo de cuentas de AD?
Sí. Tras escribir una contraseña incorrecta del dominio administrado cinco veces en un lapso de dos minutos, la cuenta de usuario quedará bloqueada durante 30 minutos. Pasados los 30 minutos, la cuenta de usuario se desbloqueará automáticamente. Los intentos no válidos de escribir la contraseña del dominio administrado no provocarán el bloqueo de la cuenta de usuario en Azure AD. La cuenta de usuario solo se bloqueará en el dominio administrado de Azure AD Domain Services.

## <a name="billing-and-availability"></a>Facturación y disponibilidad
### <a name="is-azure-ad-domain-services-a-paid-service"></a>¿Azure AD Domain Services es un servicio de pago?
Sí. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/active-directory-ds/)para obtener más información.

### <a name="is-there-a-free-trial-for-the-service"></a>¿Hay una versión de prueba gratuita para el servicio?
Este servicio se incluye en evaluación gratuita de Azure. Puede suscribirse a un [mes de evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>¿Puedo pausar un dominio administrado de Azure AD Domain Services? 
No. Después de habilitar un dominio administrado de Azure AD Domain Services, el servicio está disponible en la red virtual administrada hasta que se deshabilita o elimina el dominio administrado. No hay ninguna manera de pausar el servicio. La facturación continúa cada hora hasta que se elimine el dominio administrado.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>¿Puedo conmutar por error Azure AD Domain Services a otra región en un evento de recuperación ante desastres?
No.  Azure AD Domain Services no proporciona actualmente un modelo de implementación con redundancia geográfica. Se limita a una única red virtual en una región de Azure. Si quiere usar varias regiones de Azure, debe ejecutar los controladores de dominio de Active Directory en máquinas virtuales de IaaS de Azure.  Puede encontrar información sobre la arquitectura [aquí](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>¿Puedo obtener Servicios de dominio de Azure AD como parte de Enterprise Mobility Suite (EMS)? ¿Necesito Azure AD Premium para usar Azure AD Domain Services?
No. No, Azure AD Domain Services es un servicio de pago por uso de Azure y no forma parte de EMS. Azure AD Domain Services puede utilizarse con todas las ediciones de Azure AD (Gratis, Básico y Premium). Se le facturará por cada hora en función del uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>¿En qué regiones de Azure está disponible el servicio?
Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure AD Domain Services.
