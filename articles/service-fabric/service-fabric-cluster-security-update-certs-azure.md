---
title: Administración de certificados en un clúster de Azure Service Fabric | Microsoft Docs
description: Describe cómo agregar nuevos certificados, sustituir certificados y quitar certificados de un clúster de Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: f1998ec2fe82b9fd52547fbccb208542b22bc949
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306910"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Agregar o quitar certificados para un clúster de Service Fabric de Azure
Se recomienda leer [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para familiarizarse con cómo Service Fabric usa los certificados X.509. Debe entender qué es un certificado de clúster y para qué se usa antes de seguir avanzando.

El comportamiento de carga de certificado predeterminado del SDK de Azure Service Fabric consiste en implementar y usar un certificado definido con un plazo de validez muy amplio; independientemente de la definición de configuración principal o secundaria. Revirtiendo al comportamiento clásico es no avanzada acción recomendada y requiere establecer el valor del parámetro de configuración "UseSecondaryIfNewer" en false en la configuración de Fabric.Code.

Además de los certificados de client, al configurar la seguridad mediante certificados durante la creación del clúster, Service Fabric le permite especificar dos certificados de clúster: uno principal y uno secundario. Consulte el artículo sobre la [creación de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md) o la [creación de un clúster de Azure a través de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para más información sobre cómo configurarlos en tiempo de creación. Si se especifica un único certificado de clúster en tiempo de creación, este se utilizará como el certificado principal. Después de la creación del clúster, puede agregar un nuevo certificado como elemento secundario.

> [!NOTE]
> Para que un clúster sea seguro, siempre deberá tener implementado al menos un certificado de clúster principal o secundario válido (no revocado ni caducado), o el clúster dejará de funcionar. Cuando falten 90 días para la caducidad de todos los certificados válidos, el sistema genera un seguimiento de advertencias y un evento de estado de advertencia en el nodo. Actualmente, Service Fabric no envía ningún mensaje de correo electrónico ni cualquier otra notificación sobre este artículo. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Incorporación de un certificado de clúster secundario mediante el portal
No se pueden agregar certificados de clúster secundarios mediante Azure Portal; en su lugar, use Azure PowerShell. El proceso se describe más adelante en este documento.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Eliminación de un certificado de clúster mediante el portal
Para un clúster seguro, siempre necesitará al menos un certificado válido (no revocado ni caducado). Se utilizará el certificado implementado con el plazo de validez mayor y eliminarlo hará que el clúster deje de funcionar; asegúrese de solo eliminar el certificado caducado o el que no se use y caduque antes.

Para eliminar un certificado de seguridad sin usar, vaya a la sección Seguridad y seleccione la opción "Eliminar" en el menú contextual del certificado sin usar.

Si su intención es eliminar el certificado marcado como principal, deberá implementar uno secundario con un plazo de validez mayor que el del certificado principal para permitir la sustitución automática; una vez completada la sustitución automática, quite el certificado principal.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Incorporación de un certificado secundario mediante Powershell para Resource Manager
> [!TIP]
> Hay ahora una manera mejor y más fácil agregar un certificado secundario mediante la [agregar AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) cmdlet. No es necesario que siga el resto de los pasos descritos en esta sección.  Además, no es necesario en la plantilla usada originalmente para crear e implementar el clúster cuando se usa el [agregar AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) cmdlet.

En estos pasos se da por hecho que está familiarizado con el funcionamiento de Resource Manager, que ha implementado al menos un clúster de Service Fabric mediante una plantilla de Resource Manager, y que tiene a mano la plantilla que utilizó para configurar el clúster. También se da por hecho que está familiarizado con el uso de JSON.

> [!NOTE]
> Si necesita una plantilla de ejemplo y parámetros para usar como guía o punto de partida, descárguelos desde este [repositorio de GitHub](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edición de la plantilla de Resource Manager

Para facilitar las siguientes tareas, el ejemplo 5-VM-1-NodeTypes-Secure_Step2.JSON contiene todas las modificaciones que se van a realizar. El ejemplo está disponible [en el repositorio de GitHub](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Asegúrese de seguir todos los pasos**

1. Abra la plantilla de Resource Manager utilizada para implementar el clúster. (Si ha descargado el ejemplo del repositorio anterior, use 5-VM-1-NodeTypes-Secure_Step1.JSON para implementar un clúster seguro y, después, abra esa plantilla).

2. Agregue **dos parámetros nuevos** "secCertificateThumbprint" y "secCertificateUrlValue" del tipo "string" en la sección de parámetros de la plantilla. Puede copiar el siguiente fragmento de código y agregarlo a la plantilla. En función del origen de la plantilla, esto ya estará definido; si es así, continúe con el paso siguiente. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Modifique el recurso **Microsoft.ServiceFabric/clusters**; busque la definición del recurso "Microsoft.ServiceFabric/clusters" en la plantilla. En las propiedades de esa definición, encontrará una etiqueta JSON "Certificate", similar al siguiente fragmento de código JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Agregue una nueva etiqueta "thumbprintSecondary" y asígnele un valor "[parameters('secCertificateThumbprint')]".  

    Ahora, la definición del recurso debería ser similar a la siguiente (en función del origen de la plantilla, puede que no sea exactamente igual que este fragmento de código). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Si desea **sustituir el certificado**, especifique el nuevo certificado como principal y cambie el principal actual a secundario. Como resultado, el certificado principal actual se sustituye por el nuevo certificado en un paso de implementación.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Modifique **todas** las definiciones del recurso **Microsoft.Compute/virtualMachineScaleSets**; busque la definición del recurso Microsoft.Compute/virtualMachineScaleSets. Desplácese hasta la sección "publisher": Valor "Microsoft.Azure.ServiceFabric", en "virtualMachineProfile".

    En la configuración de publicador de Service Fabric, verá algo parecido a esto.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Agregue las nuevas entradas de certificado.
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Las propiedades tendrán ahora un aspecto similar al siguiente:
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Si desea **sustituir el certificado**, especifique el nuevo certificado como principal y cambie el principal actual a secundario. Esto provoca la sustitución del certificado actual por el nuevo certificado en un paso de implementación.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Las propiedades tendrán ahora un aspecto similar al siguiente:    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Modifique **todas** las definiciones del recurso **Microsoft.Compute/virtualMachineScaleSets**; busque la definición del recurso Microsoft.Compute/virtualMachineScaleSets. Vaya a "vaultCertificates":, en "OSProfile". Tendrá un aspecto similar al siguiente.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Agregue el valor de secCertificateUrlValue. Use el siguiente fragmento de código:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Ahora, el código JSON resultante será similar al siguiente.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Asegúrese de repetir los pasos 4 y 5 para todas las definiciones del recurso Nodetypes/Microsoft.Compute/virtualMachineScaleSets de la plantilla. Si se salta una, el certificado no se instalará en ese conjunto de escalado de máquinas virtuales y tendrá resultados impredecibles en el clúster, incluso el clúster puede deja de funcionar (si finalmente no hay ningún certificado válido que el clúster pueda usar para la seguridad). Compruébelo dos veces antes de continuar.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edición del archivo de plantilla para reflejar los nuevos parámetros agregados anteriormente
Si está usando el ejemplo del [repositorio de GitHub](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para seguir el artículo, puede empezar a hacer estos cambios en el ejemplo 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON. 

Modifique el parámetro File de la plantilla de Resource Manager, y agregue los dos nuevos parámetros para secCertificateThumbprint y secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Implementación de la plantilla en Azure

- Ahora está preparado para implementar la plantilla en Azure. Abra un símbolo del sistema de Azure PS versión 1 o superior.
- Inicie sesión en su cuenta de Azure y seleccione la suscripción de azure específica. Este es un paso importante para personas que tienen acceso a más de una suscripción.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Pruebe la plantilla antes de implementarla. Utilice el grupo de recursos en el que esté implementado el clúster actualmente.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implemente la plantilla en el grupo de recursos. Utilice el grupo de recursos en el que esté implementado el clúster actualmente. Ejecute el comando New-AzResourceGroupDeployment. No es necesario especificar el modo, ya que el valor predeterminado es **incremental**.

> [!NOTE]
> Si establece el modo completo, podría eliminar accidentalmente los recursos que no estén en la plantilla. Por ello no lo utilice en este escenario.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Este es un ejemplo ya rellenado del mismo powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Una vez completada la implementación, conecte el clúster mediante el nuevo certificado y realice algunas consultas. Siempre que sea capaz de ello. Después, puede eliminar el antiguo certificado. 

Si está utilizando un certificado autofirmado, no olvide importarlo en su almacén de certificados local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Como referencia rápida, este es el comando para conectarse a un clúster seguro 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Como referencia rápida, este es el comando para obtener el estado del clúster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Implementación de certificados de cliente en el clúster.

Puede usar los mismos pasos descritos en el paso 5 anterior para implementar los certificados en los nodos desde un almacén de claves. Solo necesita definir y usar parámetros diferentes.


## <a name="adding-or-removing-client-certificates"></a>Incorporación o eliminación de certificados de cliente

Además de los certificados de clúster, puede agregar certificados de cliente para llevar a cabo operaciones de administración en un clúster de Service Fabric.

Puede agregar dos tipos de certificados de cliente: administrador o solo lectura. Estos se pueden usar para controlar el acceso a las operaciones de administración y a las operaciones de consulta en el clúster. De forma predeterminada, los certificados de clúster se agregan a la lista de certificados de administración permitidos.

Puede especificar cualquier número de certificados de cliente. Cada incorporación o eliminación provoca una actualización de la configuración en el clúster de Service Fabric.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Incorporación de certificados de cliente de administrador o solo lectura mediante el portal

1. Vaya a la sección Seguridad y seleccione el botón "+ Autenticación" en la parte superior de la sección.
2. En la sección "Agregar autenticación", en "Tipo de autenticación", elija "Cliente de solo lectura" o "Cliente de administración".
3. Ahora, elija el método de autorización. Indica a Service Fabric si se debe buscar este certificado mediante el nombre del sujeto o la huella digital. Por lo general, no es una buena práctica de seguridad usar el método de autorización de nombre de sujeto. 

![Incorporación de certificados de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Eliminación de certificados de cliente de administrador o solo lectura mediante el portal

Para quitar un certificado secundario y dejar de usarlo para la seguridad del clúster, vaya a la sección Seguridad y seleccione “Eliminar” en el menú contextual del certificado especificado.

## <a name="next-steps"></a>Pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

* [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
* [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


