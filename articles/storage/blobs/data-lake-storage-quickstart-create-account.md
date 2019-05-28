---
title: Creación de una cuenta de almacenamiento de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Aprenda rápidamente a crear una cuenta de almacenamiento con acceso a Data Lake Storage Gen2 en Azure Portal, Azure PowerShell o la CLI de Azure.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 18132ac4c218c766efdc9a9afae2cc3508c4f732
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939420"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>Inicio rápido: Creación de una cuenta de almacenamiento de Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 [admite un espacio de nombres jerárquico](data-lake-storage-introduction.md) que proporciona un sistema de archivos nativo basado en directorios que se ha diseñado para trabajar con el sistema de archivos distribuido de Hadoop (HDFS). El acceso a los datos de Data Lake Storage Gen2 desde HDFS está disponible en el [controlador ABFS](data-lake-storage-abfs-driver.md).

En esta guía de inicio rápido, se muestra cómo crear una cuenta en [Azure Portal](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o la [CLI de Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. 

|           | Requisito previo |
|-----------|--------------|
|Portal     | Ninguno         |
|PowerShell | Para realizar los pasos de esta guía, se requiere la versión **0.7** o posterior de Az.Storage del módulo de PowerShell. Para saber cuál es su versión actual, ejecute el comando `Get-Module -ListAvailable Az.Storage`. Si después de ejecutar este comando no aparece ningún resultado o si aparece una versión distinta de **0.7**, deberá actualizar el módulo de PowerShell. Consulte la sección [Actualización del módulo de PowerShell](#upgrade-your-powershell-module) de esta guía.
|CLI        | Puede iniciar sesión en Azure y ejecutar los comandos de la CLI de Azure de dos maneras: <ul><li>Puede ejecutar los comandos de la CLI desde Azure Portal, en Azure Cloud Shell. </li><li>Puede instalar la CLI y ejecutar los comandos localmente.</li></ul>|

Si utiliza la línea de comandos, puede ejecutar Azure Cloud Shell o instalar la CLI localmente.

### <a name="use-azure-cloud-shell"></a>Uso de Azure Cloud Shell

Azure Cloud Shell es un shell de Bash gratuito que puede ejecutarse directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizarla con la cuenta. Haga clic en el botón **Cloud Shell** del menú situado en la parte superior derecha de Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Este botón inicia un shell interactivo que se puede utilizar para ejecutar los pasos de esta guía de inicio rápido:

[![Captura de pantalla en la que aparece la ventana de Cloud Shell del portal](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalación local de la CLI

También puede instalar y usar la CLI de Azure localmente. En esta guía de inicio rápido, es preciso que use la versión 2.0.38 de la CLI de Azure u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Creación de una cuenta de almacenamiento con Azure Data Lake Storage Gen2 habilitado

Para poder crear una cuenta, primero debe crear un grupo de recursos que actúe como un contenedor lógico para las cuentas de almacenamiento o cualquier otro recurso de Azure que cree. Si desea limpiar los recursos creados por esta guía de inicio rápido, basta con eliminar el grupo de recursos. Al eliminar el grupo de recursos, también se elimina la cuenta de almacenamiento asociada y cualquier otro recurso que esté asociado a dicho grupo. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Debe crear nuevas cuentas de almacenamiento de tipo **StorageV2 (V2 de propósito general)** para aprovechar las características de Data Lake Storage Gen2.  

Para más información sobre las cuentas de almacenamiento, consulte [Introducción a las cuentas de Azure Storage](../common/storage-account-overview.md).

Cuando especifique un nombre para la cuenta de almacenamiento, tenga en cuenta estas reglas:

- Los nombres de las cuentas de almacenamiento deben tener entre 3 y 24 caracteres y solo pueden incluir números y letras en minúscula.
- El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. No puede haber dos cuentas de almacenamiento con el mismo nombre.

## <a name="create-an-account-using-the-azure-portal"></a>Creación de una cuenta en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos en Azure Portal, siga estos pasos:

1. En Azure Portal, expanda el menú situado en el lado izquierdo para abrir el menú de servicios y elija **Grupos de recursos**.
2. Haga clic en el botón **Agregar** para agregar un nuevo grupo de recursos.
3. Escriba un nombre para el nuevo grupo de recursos.
4. Seleccione la suscripción en la que desea crear el nuevo grupo de recursos.
5. Elija la ubicación del grupo de recursos.
6. Haga clic en el botón **Crear**.  

   ![Captura de pantalla que muestra cómo se crea un grupo de recursos en Azure Portal](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Creación de una cuenta de almacenamiento de uso general v2

Para crear una cuenta de almacenamiento de uso general v2 en Azure Portal, siga estos pasos:

> [!NOTE]
> El espacio de nombres jerárquico está actualmente disponible en todas las regiones pública.

1. En Azure Portal, expanda el menú de la izquierda para abrir el menú de servicios y elija **Todos los servicios**. Desplácese hacia abajo hasta **Almacenamiento** y elija **Cuentas de almacenamiento**. En la ventana **Cuentas de almacenamiento** que aparece, elija **Agregar**.
2. Seleccione la **suscripción** y el **grupo de recursos** que creó anteriormente.
3. Escriba un nombre para la cuenta de almacenamiento.
4. Establezca **Ubicación** a **Oeste de EE. UU. 2**
5. Deje estos campos con sus valores predeterminados: **Rendimiento**, **Tipo de cuenta**, **Replicación**, **Nivel de acceso**.
6. Elija la suscripción en la que desea crear la nueva cuenta de almacenamiento.
7. Seleccione **Siguiente : Avanzado >**
8. Deje los campos **SEGURIDAD** y **REDES VIRTUALES** en sus valores predeterminados.
9. En la sección **Data Lake Storage Gen2**, establezca **Espacio de nombres jerárquico** en **Habilitado**.
10. Haga clic en **Revisar + Crear** para crear la cuenta de almacenamiento.

    ![Captura de pantalla en la que se muestra la creación de una cuenta de almacenamiento en Azure Portal](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

De este modo, ya ha creado la cuenta de almacenamiento en el portal.

### <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar un grupo de recursos desde Azure Portal:

1. En Azure Portal, expanda el menú de la izquierda para abrir el menú de servicios y elija **Grupos de recursos** para ver una lista con sus grupos de recursos.
2. Busque el grupo de recursos que desea eliminar y haga clic con el botón derecho en el botón **Más** ( **...** ) situado en la parte derecha de la lista.
3. Seleccione **Eliminar grupo de recursos** y confirme.

## <a name="create-an-account-using-powershell"></a>Creación de una cuenta con PowerShell

En primer lugar, instale la versión más reciente del módulo [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).

A continuación, actualice el módulo de PowerShell, inicie sesión en la suscripción de Azure, cree un grupo de recursos y, luego, una cuenta de almacenamiento.

### <a name="upgrade-your-powershell-module"></a>Actualización del módulo de Powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para interactuar con Data Lake Storage Gen2 mediante PowerShell, deberá instalar la versión **0.7** o posterior de Az.Storage del módulo.

Para empezar, abra una sesión de PowerShell con permisos elevados.

Instalación del módulo Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure

Use el comando `Login-AzAccount` y siga las instrucciones en pantalla para realizar la autenticación.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos con PowerShell, use el comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): 

> [!NOTE]
> El espacio de nombres jerárquico está actualmente disponible en todas las regiones pública.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Creación de una cuenta de almacenamiento de uso general v2

Para crear una cuenta de almacenamiento de uso general v2 en PowerShell con almacenamiento con redundancia local (LRS), use el comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount):

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar el grupo de recursos y sus recursos asociados, incluida la nueva cuenta de almacenamiento, use el comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Creación de una cuenta con la CLI de Azure

Para iniciar Azure Cloud Shell, inicie sesión en [Azure Portal](https://portal.azure.com).

Si quiere iniciar sesión en la instalación local de la CLI, ejecute el comando de inicio de sesión:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Incorporación de la extensión de la CLI para Azure Data Lake Gen 2

Para interactuar con Data Lake Storage Gen2 mediante la CLI, deberá agregar una extensión al shell.

Para ello, mediante Cloud Shell o un shell local, escriba el siguiente comando: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos con la CLI de Azure, use el comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > El espacio de nombres jerárquico está actualmente disponible en todas las regiones pública.

### <a name="create-a-general-purpose-v2-storage-account"></a>Creación de una cuenta de almacenamiento de uso general v2

Para crear una cuenta de almacenamiento de uso general v2 desde la CLI de Azure con redundancia local, use el comando [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar el grupo de recursos y sus recursos asociados, incluida la nueva cuenta de almacenamiento, use el comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una cuenta de almacenamiento con funcionalidades de Data Lake Storage Gen2. Para información sobre cómo cargar y descargar blobs en la cuenta de almacenamiento, consulte el tema siguiente.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
