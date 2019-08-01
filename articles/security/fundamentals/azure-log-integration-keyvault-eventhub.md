---
title: Integrar registros de Azure Key Vault mediante Event Hubs | Microsoft Docs
description: Tutorial en el que se proporcionan los pasos necesarios para que los registros de Key Vault estén disponibles para un SIEM mediante la integración de registros de Azure
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 985c6eadbc1ef4da4a15825d263b3ebe95de8107
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68611791"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de Azure Log Integration: Procesamiento de eventos de Azure Key Vault mediante Event Hubs

>[!IMPORTANT]
> La característica Azure Log Integration dejará de utilizarse el 15/06/2019. Las descargas de AzLog se deshabilitaron el 27 de junio de 2018. Para obtener orientación sobre cómo avanzar, consulte el artículo [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Uso de Azure Monitor para realizar la integración con herramientas SIEM) 

Puede usar Azure Log Integration para recuperar los eventos registrados y hacer que estén disponibles en su sistema de información de seguridad y administración de eventos (SIEM). En este tutorial se muestra un ejemplo de cómo se puede usar Azure Log Integration para procesar registros adquiridos a través de Azure Event Hubs.

El método preferido para la integración de registros de Azure es mediante el conector de Azure Monitor del proveedor SIEM y siguiendo estas [instrucciones](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). De todas formas, si su proveedor SIEM no proporciona un conector a Azure Monitor, es posible que pueda usar Azure Log Integration como solución temporal (si el SIEM es compatible con Azure Log Integration) hasta que esté disponible un conector de este tipo.

 
Utilícelo para familiarizarse con el modo en que Azure Log Integration y Event Hubs funcionan conjuntamente siguiendo los pasos del ejemplo y entendiendo cómo cada paso se encamina hacia la solución. A continuación, puede aprovechar lo que ha aprendido aquí para crear sus propios pasos según los requisitos únicos de su empresa.

> [!WARNING]
> Los pasos y los comandos de este tutorial no están diseñados para copiarse y pegarse. Tan solo son ejemplos. No use los comandos de PowerShell tal cual en el entorno activo. Deben personalizarse en función del entorno específico.


Este tutorial le guiará por el proceso de registrar la actividad de Azure Key Vault en un centro de eventos y hacer que esté disponible como archivos JSON en el sistema SIEM. Después, puede configurar el sistema SIEM para que procese los archivos JSON.

>[!NOTE]
>La mayoría de los pasos de este tutorial conllevan la configuración de almacenes de claves, cuentas de almacenamiento y centros de eventos. Los pasos específicos de la integración de registros de Azure están al final de este tutorial. No realice estos pasos en un entorno de producción, ya que se han diseñado exclusivamente para un entorno de laboratorio. Los pasos deben personalizarse antes de usarlos en un entorno de producción.

La información proporcionada en el proceso le ayudará a comprender los motivos detrás de cada paso. Los vínculos a otros artículos proporcionan más detalles sobre determinados temas.

Para más información sobre los servicios que se mencionan en este tutorial, vea: 

- [Azure Key Vault](/azure/key-vault/key-vault-whatis)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Integración de registros de Azure](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuración inicial

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Necesita lo siguiente para completar los pasos de este artículo:

* Una suscripción de Azure y una cuenta en esa suscripción con derechos de administrador. Si no tiene una suscripción, puede crear [una cuenta gratuita](https://azure.microsoft.com/free/).
 
* Un sistema con acceso a Internet que cumpla los requisitos para la instalación de la integración de registros de Azure. El sistema puede estar en un servicio en la nube u hospedado en una ubicación local.

* Azure Log Integration instalada. Para instalarla:

   a. Use Escritorio remoto para conectarse al sistema mencionado en el paso 2.   
   b. Copie el instalador de la integración de registros de Azure en el sistema. c. Inicie el instalador y acepte los Términos de licencia del software de Microsoft.

* Si va a proporcionar información de telemetría, deje activada la casilla. Si prefiere no enviar información de uso a Microsoft, desactive la casilla.

   Para obtener más información sobre Azure Log Integration y cómo instalar este servicio, consulte [Azure Log Integration en registros de Azure Diagnostics y reenvío de eventos de Windows](azure-log-integration-get-started.md).

* La versión más reciente de PowerShell.

   Si tiene instalado Windows Server 2016, entonces tiene al menos PowerShell 5.0. Si usa otra versión de Windows Server, es posible que tenga instalada una versión anterior de PowerShell. Puede comprobar la versión escribiendo ```get-host``` en una ventana de PowerShell. Si no tiene PowerShell 5.0 instalado, puede [descargarlo](https://www.microsoft.com/download/details.aspx?id=50395).

   Cuando ya disponga al menos de PowerShell 5.0, puede continuar con la instalación de la versión más reciente con las instrucciones de [Instalación de Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Crear los elementos de la infraestructura de soporte

1. Abra una ventana de PowerShell con privilegios elevados y vaya a **C:\Archivos de programa\Microsoft Azure Log Integration**.
1. Importe los cmdlets de AzLog mediante la ejecución del script LoadAzLogModule.ps1. Escriba el comando `.\LoadAzLogModule.ps1`. Observe la parte ".\"" en el comando. Puede ver algo así:</br>

   ![Lista de módulos cargados](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Escriba el comando `Connect-AzAccount`. En la ventana de inicio de sesión, escriba la información de credenciales de la suscripción que va a usar para este tutorial.

   >[!NOTE]
   >Si se trata de la primera vez que inicia sesión en Azure desde este equipo, verá un mensaje que le pregunta si quiere permitir a Microsoft recopilar datos de uso de PowerShell. Es recomendable que habilite esta recopilación de datos ya que se usará para mejorar Azure PowerShell.

1. Una vez que se autentique correctamente, se iniciará la sesión. Tome nota del identificador y el nombre de la suscripción, ya que los necesitará para completar los pasos posteriores.

1. Cree variables para almacenar valores que se usarán más adelante. Escriba cada una de las siguientes líneas de PowerShell. Es posible que necesite ajustar los valores para que se adapten a su entorno.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (El nombre de la suscripción puede ser diferente. Puede verlo como parte de la salida del comando anterior).
    - ```$location = 'West US'``` (Se utilizará esta variable para pasar la ubicación donde se deben crear los recursos. Puede cambiar esta variable para que sea cualquier otra ubicación que elija).
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (El nombre puede ser de cualquier tipo, pero solo debe incluir números y letras en minúsculas).
    - ```$storageName = $name``` (Esta variable se usará para el nombre de la cuenta de almacenamiento).
    - ```$rgname = $name``` (Esta variable se usará para el nombre del grupo de recursos).
    - ```$eventHubNameSpaceName = $name``` (El nombre del espacio de nombres del centro de eventos).
1. Especifique la suscripción con la que va a trabajar:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Cree un grupo de recursos:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Si escribe `$rg` en este punto, deberá ver un resultado similar al de esta captura de pantalla:

   ![Salida después de la creación de un grupo de recursos](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Cree una cuenta de almacenamiento que se usará para realizar el seguimiento de la información de estado:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Cree el espacio de nombres del centro de eventos. Esto es necesario para crear un centro de eventos.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Obtenga el identificador de regla que se usará con el proveedor de Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Obtenga todas las ubicaciones posibles de Azure y agregue los nombres a una variable que se puede usar en un paso posterior:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Si escribe `$locations` en este punto, verá los nombres de las ubicaciones sin la información adicional que devuelve Get-AzLocation.
1. Cree un perfil de registro de Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para más información sobre el perfil de registro de Azure, vea [Información general sobre el registro de actividad de Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Es posible que reciba un mensaje de error al intentar crear un perfil de registro. Revise la documentación de Get-AzLogProfile y Remove-AzLogProfile. Si ejecuta Get-AzLogProfile, verá información sobre el perfil de registro. Puede eliminar el perfil de registro actual escribiendo el comando ```Remove-AzLogProfile -name 'Log Profile Name'```.
>
>![Error de perfil de Resource Manager](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves

1. Cree el almacén de claves:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Configure el registro del almacén de claves:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generar actividad de registro

Las solicitudes se deben enviar a Key Vault para generar actividades de registro. Acciones como la generación de claves, la lectura o el almacenamiento de secretos de Key Vault, crearán entradas del registro.

1. Muestre las claves de almacenamiento actuales:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Genere una nueva **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Vuelva a mostrar las claves y verá que **key2** contiene un valor diferente:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Establezca y lea un secreto para generar entradas de registro adicionales:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Secreto devuelto](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar la integración de registros de Azure

Ahora que ya ha configurado todos los elementos necesarios para guardar el registro de Key Vault en un centro de eventos, debe configurar la integración de registros de Azure:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Ejecute el comando AzLog para cada centro de eventos:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Aproximadamente un minuto después de la ejecución de los dos últimos comandos, deberían empezar a generarse los archivos JSON. Puede confirmarlo supervisando el directorio **C:\usuarios\AzLog\EventHubJson**.

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes sobre la integración de registro de Azure](azure-log-integration-faq.md)
- [Introducción a la integración de registros de Azure](azure-log-integration-get-started.md)
- [Integrar registros de recursos de Azure en sistemas SIEM](azure-log-integration-overview.md)
