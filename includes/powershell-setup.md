---
services: virtual-machines
title: Configuración de PowerShell
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: b96e8e6e31817f6d261f41dbf3b3047dd49c29ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485411"
---
## <a name="setting-up-powershell"></a>Configuración de PowerShell
Siga estos pasos para poder usar Azure PowerShell.

### <a name="verify-powershell-versions"></a>Comprobar las versiones de PowerShell
Para poder usar Windows PowerShell, debe tener instalada la versión 3.0 o 4.0 de Windows PowerShell. Para buscar la versión de Windows PowerShell, escriba este comando en un símbolo del sistema de Windows PowerShell.

    $PSVersionTable

Puede ver algo parecido a lo siguiente:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Compruebe que el valor de **PSVersion** es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

También debe disponer de Azure PowerShell, versión 0.8.0 o posterior. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

    Get-Module azure | format-table version

Puede ver algo parecido a lo siguiente:

    Version
    -------
    0.8.16.1

Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Definición de su cuenta y suscripción de Azure
Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra un símbolo del sistema de Azure PowerShell e inicie sesión en Azure con este comando.

    Add-AzureAccount

Si tiene varias suscripciones de Azure, puede enumerarlas con este comando.

    Get-AzureSubscription

Recibirá el siguiente tipo de información:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Puede establecer la suscripción actual de Azure ejecutando estos comandos en el símbolo del sistema de Azure PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Para más información acerca de las cuentas y suscripciones de Azure, consulte [Procedimiento de conexión a su suscripción](/powershell/azureps-cmdlets-docs#Connect).

