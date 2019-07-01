---
title: Solución de problemas de instalación en Azure AD Connect | Microsoft Docs
description: En este tema se proporcionan los pasos para solucionar problemas relacionados con la instalación de Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454798"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Solución de problemas: Problemas de instalación en Azure AD Connect

## <a name="recommended-steps"></a>**Pasos recomendados**
Compruebe qué [tipo de instalación de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) es adecuada para usted. Si cumple los criterios de instalación rápida, se recomienda que elija esa opción. La instalación rápida proporciona un número mínimo de opciones para finalizar la instalación; por lo tanto, la probabilidad de que surjan problemas es menor. 

Sin embargo, si no cumple los criterios para la instalación rápida y debe realizar la instalación personalizada, a continuación se describen algunos procedimientos recomendados que puede seguir para evitar problemas comunes. Para simplificar, solo se mencionan algunas opciones:

* Asegúrese de que es administrador en el equipo en el que está instalando AAD Connect. Inicie sesión en la máquina con esas mismas credenciales de administrador.

* Permita que todas las opciones de la siguiente página se queden en sus valores predeterminados, a excepción de "Use an existing SQL Server" (Usar una instancia de SQL Server existente) si quiere usar una instancia de SQL Server existente. En [este artículo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) encontrará más información sobre cómo usar las opciones de instalación personalizada. 

    ![Uso de una instancia de SQL Server existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* En la página siguiente, seleccione la opción "Crear una cuenta de AD", para evitar los problemas de permisos con la cuenta existente.

    ![Cuenta del bosque de AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas comunes**

* [Problemas de conectividad con Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemas de conectividad con Azure Active Directory en línea](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemas de permisos con Active Directory local](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documentos recomendados**
* [Requisitos previos de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selección del tipo de instalación que se usará para Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introducción a Azure AD Connect mediante la configuración rápida](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Instalación personalizada de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: actualización de una versión anterior a la versión más reciente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: ¿Qué es un servidor de almacenamiento provisional?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [¿Qué es el módulo de PowerShell ADConnectivityTool?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Pasos siguientes
- [Sincronización de Azure AD Connect](how-to-connect-sync-whatis.md).
- [¿Qué es la identidad híbrida?](whatis-hybrid-identity.md)





