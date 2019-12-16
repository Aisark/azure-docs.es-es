---
title: Protección de artefactos en plantillas
description: Obtenga información sobre cómo proteger los artefactos que se usan en las plantillas de Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1a9d209e843d8e9a1735a3c6907b00d85be6580b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971745"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Protección de los artefactos de las implementaciones de plantillas de Azure Resource Manager

Obtenga información sobre cómo proteger los artefactos que se usan en las plantillas de Azure Resource Manager mediante la cuenta de Azure Storage con firmas de acceso compartido (SAS). Los artefactos de implementación son cualquier archivo, además del archivo de plantilla principal, que se necesitan para completar una implementación. Por ejemplo, en [Tutorial: Importación de archivos BACPAC de SQL con plantillas de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), la plantilla principal crea una base de datos de Azure SQL; también llama a un archivo BACPAC para crear tablas e insertar datos. El archivo BACPAC es un artefacto que se almacena en una cuenta de Azure Storage. Se utilizó la clave de la cuenta de almacenamiento para acceder al artefacto. En este tutorial, usará SAS para conceder acceso limitado al archivo BACPAC en su propia cuenta de Azure Storage. Para obtener más información sobre SAS, consulte [Uso de firmas de acceso compartido (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Si quiere más detalles sobre cómo proteger plantillas vinculadas, consulte [Tutorial: Creación de plantillas vinculadas de Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Preparación de un archivo BACPAC
> * Apertura de una plantilla existente
> * Edición de la plantilla
> * Implementación de la plantilla
> * Comprobar la implementación

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

* Visual Studio Code con la extensión Resource Manager Tools. Consulte [Uso de Visual Studio Code para crear plantillas de Azure Resource Manager](./resource-manager-tools-vs-code.md).
* Revise [Tutorial: Importación de archivos BACPAC de SQL con plantillas de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). La plantilla usada en este tutorial es lo que se desarrolló en ese mismo tutorial. En este artículo se proporciona un vínculo de descarga de la plantilla completa.
* Para aumentar la seguridad, utilice una contraseña generada para la cuenta de administrador de SQL Server. Este es un ejemplo para generar una contraseña:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault está diseñado para proteger las claves criptográficas y otros secretos. Para más información, consulte [Tutorial: Integración de Azure Key Vault en Resource Manager Template Deployment](./resource-manager-tutorial-use-key-vault.md). También se recomienda actualizar la contraseña cada tres meses.

## <a name="prepare-a-bacpac-file"></a>Preparación de un archivo BACPAC

En esta sección, preparará el archivo BACPAC para que se pueda acceder a él de forma segura al implementar la plantilla de Resource Manager. Esta sección tiene cinco procedimientos:

* Descargue el archivo BACPAC.
* Cree una cuenta de Azure Storage.
* Cree un contenedor de blobs en una cuenta de almacenamiento.
* Cargue el archivo BACPAC en el contenedor.
* Recupere el token de SAS del archivo BACPAC.

1. Seleccione **Pruébelo** para abrir Cloud Shell y pegue el siguiente script de PowerShell en la ventana del shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Anote la dirección URL del archivo BACPAC y el token de SAS. Necesitará estos valores al implementar la plantilla.

## <a name="open-an-existing-template"></a>Apertura de una plantilla existente

En esta sesión, modificará la plantilla que creó en [Tutorial: Importación de archivos BACPAC de SQL con plantillas de Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) para llamar al archivo BACPAC con un token de SAS.  La plantilla desarrollada en el tutorial de la extensión de SQL está compartida en [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. En Visual Studio Code, seleccione **Archivo**>**Abrir archivo**.
2. En **Nombre de archivo**, pegue el código URL siguiente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

3. Seleccione **Abrir** para abrir el archivo.

    Hay cuatro recursos definidos en la plantilla:

   * `Microsoft.Sql/servers`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Resulta útil obtener cierta información básica de la plantilla antes de personalizarla.
4. Seleccione **Archivo**>**Guardar como** para guardar una copia del archivo en la máquina local con el nombre **azuredeploy.json**.

## <a name="edit-the-template"></a>Edición de la plantilla

1. Reemplace la definición del parámetro storageAccountKey por la siguiente definición de parámetro:

    ```json
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the SAS token required to access the artifact location."
      }
    },
    ```

    ![Parámetros de protección de artefactos del tutorial de Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

2. Actualice el valor de los tres elementos siguientes del recurso de extensión de SQL:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

La plantilla completada tiene este aspecto:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>Implementación de la plantilla

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte la sección [Implementación de la plantilla](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para conocer el procedimiento de implementación. En su lugar, use el siguiente script de implementación de PowerShell.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Use una contraseña generada. Consulte [Requisitos previos](#prerequisites).
Para los valores de _artifactsLocation, _artifactsLocationSasToken y bacpacFileName, consulte [Preparación de un archivo BACPAC](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Comprobar la implementación

En el portal, seleccione la base de datos SQL del grupo de recursos recién implementado. Seleccione **Editor de consultas (versión preliminar)** y, después, escriba las credenciales de administrador. Verá dos tablas importadas en la base de datos:

![Azure Resource Manager implementa extensiones de sql BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, limpie los recursos que implementó eliminando el grupo de recursos.

1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda.
2. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
3. Seleccione el nombre del grupo de recursos.  Verá un total de seis recursos en el grupo de recursos.
4. Seleccione **Eliminar grupo de recursos** del menú superior.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado una instancia de SQL Server y una instancia de SQL Database y, además, ha importado un archivo BACPAC mediante un token de SAS. Para información sobre cómo crear una canalización de Azure para desarrollar e implementar de manera continua las plantillas de Resource Manager, consulte

> [!div class="nextstepaction"]
> [Integración continua con Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md)
