---
title: Almacén central de secretos de Azure Service Fabric
description: En este artículo se indica cómo usar el almacén central de secretos en Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980939"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Almacén central de secretos en Azure Service Fabric 
En este artículo se describe cómo usar el Almacén central de secretos (CSS) en Azure Service Fabric para crear secretos en aplicaciones de Service Fabric. CSS es una caché local de almacén de secretos que conserva datos confidenciales, como contraseñas, tokens y claves, cifrados en memoria.

## <a name="enable-central-secrets-store"></a>Habilitación del Almacén central de secretos
Agregue el script siguiente a la configuración del clúster en `fabricSettings` para habilitar CSS. Para CSS se recomienda usar un certificado que no sea un certificado de clúster. Asegúrese de que el certificado de cifrado esté instalado en todos los nodos y de que `NetworkService` tenga permiso de lectura en la clave privada del certificado.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Declaración de un recurso como secreto
Puede crear un recurso secreto mediante la plantilla de Azure Resource Manager o la API REST.

### <a name="use-resource-manager"></a>Uso de Resource Manager

Con esta plantilla puede usar Resource Manager para crear el recurso secreto. La plantilla crea un recurso secreto `supersecret`, pero todavía no se ha establecido en ningún valor.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>Uso de la API de REST

Para crear un recurso secreto `supersecret` mediante la API REST, realice una solicitud PUT a `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Necesita el certificado del clúster o el certificado de cliente de administrador para crear un recurso secreto.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Establecimiento del valor secreto

### <a name="use-the-resource-manager-template"></a>Uso de la plantilla de Resource Manager

Use la plantilla de Resource Manager siguiente para crear y establecer el valor secreto. Esta plantilla establece el valor secreto del recurso secreto `supersecret` como la versión `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>Uso de la API de REST

Con este script puede usar la API REST para establecer el valor secreto.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Uso del secreto en la aplicación

Siga estos pasos para usar el secreto en la aplicación Service Fabric.

1. Agregue una sección al archivo **settings.xml** con el fragmento de código siguiente. Tenga en cuenta que aquí el valor tiene el formato {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importe la sección en **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   La variable de entorno `SecretPath` apuntará al directorio donde se almacenan todos los secretos. Cada parámetro incluido en la sección `testsecrets` se almacena en un archivo independiente. Ahora la aplicación puede usar el secreto como se muestra aquí:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monte los secretos en un contenedor. El único cambio necesario para que los secretos pasen a estar disponibles dentro del contenedor consiste en aplicar `specify` a un punto de montaje en `<ConfigPackage>`.
El fragmento de código siguiente es el archivo **ApplicationManifest.xml** modificado.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Los secretos están disponibles en el punto de montaje dentro del contenedor.

1. Puede enlazar un secreto a una variable de entorno de proceso si especifica `Type='SecretsStoreRef`. El fragmento siguiente es un ejemplo de cómo enlazar la versión `ver1` de `supersecret` a la variable de entorno `MySuperSecret` de **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [seguridad de aplicaciones y servicios](service-fabric-application-and-service-security.md).
