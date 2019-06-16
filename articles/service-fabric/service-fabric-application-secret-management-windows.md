---
title: Configuración de certificado de cifrado y cifrado de secretos en clústeres Windows de Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre cómo configurar un certificado de cifrado y cifrado de secretos en clústeres Windows.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615270"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Configuración de un certificado de cifrado y cifrado de secretos en clústeres Windows
En este artículo se muestra cómo configurar un certificado de cifrado y usarlo para cifrar secretos en clústeres Windows. Para los clústeres Linux, consulte [Configuración de un certificado de cifrado y cifrado de secretos en clústeres Linux][secret-management-linux-specific-link].

[Azure Key Vault][key-vault-get-started] se usa aquí como ubicación de almacenamiento seguro para los certificados y como forma de obtener los certificados instalados en clústeres de Service Fabric en Azure. Si no va a implementar en Azure, no es necesario usar Key Vault para administrar secretos en aplicaciones de Service Fabric. Sin embargo, el *uso* de secretos en una aplicación es independiente de la plataforma de nube para permitir que las aplicaciones se implementen en un clúster hospedado en cualquier parte. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obtención de un certificado de cifrado de datos
Un certificado de cifrado de datos se utiliza estrictamente para cifrar y descifrar los [parámetros][parameters-link] de un archivo Settings.xml del servicio y las [variables de entorno][environment-variables-link] de un archivo ServiceManifest.xml del servicio. No se usa para la autenticación o la firma de texto cifrado. El certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El uso de claves de certificado debe incluir el cifrado de datos (10), y no debe incluir la autenticación de servidor o la autenticación de cliente. 
  
  Por ejemplo, al crear un certificado autofirmado mediante PowerShell, la marca `KeyUsage` debe establecerse en `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalación del certificado en el clúster
Este certificado debe instalarse en cada nodo del clúster. Consulte [cómo crear un clúster mediante Azure Resource Manager][service-fabric-cluster-creation-via-arm] para ver las instrucciones de configuración. 

## <a name="encrypt-application-secrets"></a>Cifrado de los secretos de aplicación
El siguiente comando de PowerShell se usa para cifrar un secreto. Este comando solo cifra el valor; **no** firma el texto cifrado. Para producir texto cifrado para los valores de secreto, debe usar el mismo certificado de cifrado que está instalado en el clúster:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

La cadena codificada en base64 resultante contiene tanto el texto cifrado del secreto, así como la información sobre el certificado que se usó para cifrarlo.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo [especificar secretos cifrados en una aplicación.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
