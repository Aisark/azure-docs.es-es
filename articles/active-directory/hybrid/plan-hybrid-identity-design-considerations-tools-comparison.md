---
title: 'Identidad híbrida: comparación de herramientas de integración de directorios | Microsoft Docs'
description: Esta página proporciona una tabla completa con la comparación de las distintas herramientas que se pueden usar para la integración de directorios.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456924"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Identidades híbridas: comparación de las herramientas para la integración de directorios de identidades híbridas
A lo largo de los años, las herramientas para la integración de directorios han crecido y evolucionado.  En este documento, se proporciona una vista consolidada de estas herramientas, además de una comparación de las características disponibles en cada una de ellas.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect incorpora los componentes y la funcionalidad publicados previamente como Dirsync y Sincronización de AAD. Estas herramientas ya no se publican de manera individual, y todas las futuras mejoras se incluirán en actualizaciones de Azure AD Connect; así, siempre sabrá dónde obtener la funcionalidad más actualizada.
> 
> DirSync y Sincronización de Azure AD están en desuso. Puede encontrar más información [aquí](reference-connect-dirsync-deprecated.md).
> 
> 

Utilice la clave siguiente para cada una de las tablas.

●  = Ya disponible  
FR = versión futura  
PP = En versión preliminar pública  

## <a name="on-premises-to-cloud-synchronization"></a>Sincronización de local a la nube
| Característica | Azure Active Directory Connect | Servicios de sincronización de Azure Active Directory (Sincronización de AAD): YA NO SE ADMITE | Herramienta de sincronización de Azure Active Directory (DirSync): YA NO SE ADMITE | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Conexión a un bosque de AD local |● |● |● |● |● |
| Conexión a varios bosques de AD locales |● |● | |● |● |
| Conexión a varias organizaciones de Exchange locales |● | | | | |
| Conexión a un directorio LDAP local |●* | | |● |● | 
| Conexión a varios directorios LDAP locales |●*  | | |● |● | 
| Conexión a directorios AD y LDAP locales |●* | | |● |● | 
| Conexión a sistemas personalizados (es decir, SQL, Oracle, MySQL, etc.) |VF | | |● |● |
| Sincronización de atributos definidos por el cliente (extensiones de directorio) |● | | | | |
| Conexión a sistemas de recursos humanos locales (es decir, SAP, Oracle eBusiness, PeopleSoft) |VF | | |● |● |
| Admite reglas de sincronización y conectores de FIM para el aprovisionamiento de sistemas locales. | | | |● |● |

 
&#42; Actualmente hay dos opciones admitidas para esto.  Son las siguientes: 

   1. Puede usar el conector LDAP genérico y habilitarlo fuera de Azure AD Connect.  Es un proceso complejo y requiere un asociado para la incorporación y un acuerdo de soporte técnico Premier para su mantenimiento.  Esta opción puede controlar uno o varios directorios LDAP. 

   2. Puede desarrollar su propia solución para mover objetos de LDAP a Active Directory.  Después, sincronice los objetos con Azure AD Connect.  MIM o FIM podrían utilizarse como una posible solución para mover los objetos. 

## <a name="cloud-to-on-premises-synchronization"></a>Sincronización de la nube a local
| Característica | Azure Active Directory Connect | Servicios de sincronización de Azure Active Directory: YA NO SE ADMITE  | Herramienta de sincronización de Azure Active Directory (DirSync): YA NO SE ADMITE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Escritura diferida de dispositivos |● | |● | | |
| Escritura diferida de atributos (para la implementación híbrida de Exchange) |● |● |● |● |● |
| Escritura diferida de objetos de grupos |● | | | | |
| Escritura diferida de contraseñas (desde autoservicio de restablecimiento de contraseña [SSPR] y cambio de contraseña) |● |● | | | |

## <a name="authentication-feature-support"></a>Compatibilidad con características de autenticación
| Característica | Azure Active Directory Connect | Servicios de sincronización de Azure Active Directory: YA NO SE ADMITE  | Herramienta de sincronización de Azure Active Directory (DirSync): YA NO SE ADMITE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Sincronización de hash contraseñas para un bosque individual de AD local |●|●|● | | |
| Sincronización de hash contraseñas para varios bosques de AD locales |●|● | | | |
| Autenticación de paso a través para bosques de AD locales individuales |●| | | | |
| Inicio de sesión único con federación |● |● |● |● |● |
| Inicio de sesión único de conexión directa|● |||||
| Escritura diferida de contraseñas (desde SSPR y cambio de contraseña) |● |● | | | |

## <a name="set-up-and-installation"></a>Configuración e instalación
| Característica | Azure Active Directory Connect | Servicios de sincronización de Azure Active Directory: YA NO SE ADMITE  | Herramienta de sincronización de Azure Active Directory (DirSync): YA NO SE ADMITE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Admite la instalación de un controlador de dominio |● |● |● | |
| Admite la instalación con SQL Express |● |● |● | |
| Actualización sencilla desde DirSync |● | | | |
| Localización de la experiencia de usuario de administración a los idiomas de Windows Server |● |● |● | |
| Localización de la experiencia de usuario de usuario final a los idiomas de Windows Server | | | |● |
| Compatibilidad con Windows Server 2008 y Windows Server 2008 R2 |● para sincronización, no para federación |● |● |● |
| Compatibilidad con Windows Server 2012 y Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrado y configuración
| Característica | Azure Active Directory Connect | Servicios de sincronización de Azure Active Directory: YA NO SE ADMITE  | Herramienta de sincronización de Azure Active Directory (DirSync): YA NO SE ADMITE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrar según dominios y unidades organizativas |● |● |● |● |● |
| Filtrar según valores de atributo de los objetos |● |● |● |● |● |
| Permitir sincronización de un conjunto mínimo de atributos (MinSync) |● |● | | | |
| Permitir aplicar distintas plantillas de servicio para flujos de atributo |● |● | | | |
| Permitir quitar atributos del flujo de AD a Azure AD |● |● | | | |
| Permitir personalización avanzada para flujos de atributo |● |● | |● |● |

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md).

