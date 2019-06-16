---
title: Uso de identidades administradas para recursos de Azure en Azure Cloud Shell | Microsoft Docs
description: Autenticación de código con MSI en Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199489"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Uso de identidades administradas para recursos de Azure en Azure Cloud Shell

Azure Cloud Shell admite la autorización con las identidades de servicio administradas para los recursos de Azure. Utilice esta opción para recuperar los tokens de acceso para comunicarse de forma segura con los servicios de Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Acerca de las identidades administradas de recursos de Azure
Un desafío común al crear aplicaciones en la nube consiste en el modo de administrar de forma segura las credenciales que tienen que estar en el código para autenticar los servicios en la nube. En Cloud Shell puede que tenga que autenticar la recuperación desde Key Vault para una credencial que necesite un script.

Las identidades administradas par recursos de Azure facilita la resolución de este problema, al ofrecer a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquisición del token de acceso en Cloud Shell

Ejecute los comandos siguientes para establecer el token de acceso de MSI como una variable de entorno, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Control de la expiración de un token

El subsistema MSI local almacena en memoria caché los tokens. Por lo tanto, se puede llamar con tanta frecuencia como necesite y solo se realiza una llamada a Azure AD si:
- se produce un error de caché debido a no existir ningún token en la memoria caché
- el token ha expirado

Si almacena el token en el código, debe estar preparado para administrar escenarios en los que el recurso indica que el token ha expirado.

Para controlar los errores de token, visite la [página de MSI sobre tokens de acceso de MSI de curl](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Adquisición de tokens de acceso a partir de máquinas virtuales de MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
