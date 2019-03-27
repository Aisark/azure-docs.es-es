---
title: Integración de Azure Key Vault en la implementación de la plantilla de Resource Manager | Microsoft Docs
description: Aprenda a usar Azure Key Vault para pasar valores de parámetros seguros durante la implementación de la plantilla de Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 50882fc383d4dfab85b5afb919b24c7940eb5ee0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122748"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integración de Azure Key Vault en la implementación de la plantilla de Resource Manager

Aprenda a recuperar los secretos de Azure Key Vault y a usarlos como parámetros durante la implementación de Resource Manager. El valor nunca se expone debido a que solo hace referencia a su identificador de almacén de claves. Para más información, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](./resource-manager-keyvault-parameter.md)

En el tutorial [Establecimiento del orden de implementación de los recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), se crean una máquina virtual, una red virtual y algunos otros recursos dependientes. En este tutorial se personaliza la plantilla para recuperar la contraseña de administrador de la máquina virtual desde un almacén de claves.

![Diagrama de la integración de Key Vault en la plantilla de Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Preparación de un almacén de claves
> * Apertura de una plantilla de inicio rápido
> * Edición del archivo de parámetros
> * Implementación de la plantilla
> * Validación de la implementación
> * Limpieza de recursos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

* [Visual Studio Code](https://code.visualstudio.com/) con la [extensión Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar la seguridad, utilice una contraseña generada para la cuenta de administrador de máquina virtual. Este es un ejemplo para generar una contraseña:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Compruebe que la contraseña generada reúne los requisitos de las contraseñas de máquinas virtuales. Cada servicio de Azure tiene unos requisitos de contraseña concretos. Para los requisitos de contraseña de una máquina virtual, consulte [¿Cuáles son los requisitos de contraseña cuando se crea una VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparación de un almacén de claves

En esta sección se usa una plantilla de Resource Manager para crear un almacén de claves y un secreto. Esta plantilla:

* Cree un almacén de claves con la propiedad `enabledForTemplateDeployment` habilitada. El valor de esta propiedad debe ser true para que el proceso de implementación de plantilla pueda acceder a los secretos definidos en este almacén de claves.
* Agregue un secreto al almacén de claves.  El secreto almacena la contraseña del administrador de la máquina virtual.

Si usted (como usuario que va a implementar la plantilla de máquina virtual) no es propietario ni colaborador del almacén de claves, para poder acceder a Microsoft.KeyVault/vaults/deploy/action el propietario o colaborador debe concederle el permiso pertinente. Para más información, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](./resource-manager-keyvault-parameter.md)

La plantilla necesita el identificador de objeto de usuario de Azure AD para configurar los permisos. El procedimiento siguiente permite obtener el identificador de objeto (GUID).

1. Ejecute el siguiente comando de Azure PowerShell o de la CLI de Azure.  

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
    o
    ```azurepowershell-interactive
    $displayName = Read-Host -Prompt "Enter your user display name (i.e. John Dole, see the upper right corner of the Azure portal)"
    (Get-AzADUser -DisplayName $displayName).Id
    ```
    ---
2. Anote el identificador de objeto. Los necesitará más adelante en este tutorial.

Para crear un almacén de claves:

1. Seleccione la imagen siguiente para iniciar sesión en Azure y abrir una plantilla. La plantilla crea un almacén de claves y un secreto.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Seleccione o escriba los siguientes valores.  No seleccione **Comprar** después de escribir los valores.

    ![Portal de implementación de la integración de Key Vault en la plantilla de Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Suscripción**: seleccione una suscripción de Azure.
    * **Grupo de recursos**: asigne un nombre único. Anote este nombre y utilice el mismo grupo de recursos para implementar la máquina virtual en la siguiente sesión. La colocación del almacén de claves y la máquina virtual en el mismo grupo de recursos facilita la limpieza del recurso al final del tutorial.
    * **Ubicación**: seleccione una ubicación.  La ubicación predeterminada es **Centro de EE. UU.**
    * **Nombre del almacén de claves**: asigne un nombre único. 
    * **Id. de inquilino**: la función de plantilla recupera automáticamente el identificador del inquilino.  No cambie el valor predeterminado
    * **Id. de usuario de AD**: escriba el identificador del objeto de usuario de Azure AD que recuperó del último procedimiento.
    * **Nombre del secreto**: el nombre predeterminado es **vmAdminPassword**. Si cambia el nombre de secreto aquí, deberá actualizar el nombre de secreto al implementar la máquina virtual.
    * **Valor del secreto**: escriba el secreto.  El secreto es la contraseña utilizada para iniciar sesión en la máquina virtual. Se recomienda usar la contraseña generada que creó en el último procedimiento.
    * **Acepto los términos y condiciones anteriores**: Seleccionar.
3. Seleccione **Editar parámetros** en la parte superior para echar un vistazo a la plantilla.
4. Vaya a la línea 28 del archivo JSON de la plantilla. Se trata de la definición de recursos del almacén de claves.
5. Vaya a la línea 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` es una propiedad de Key Vault. El valor de esta propiedad debe ser true para poder recuperar los secretos de este almacén de claves durante la implementación.
6. Vaya a la línea 89. Se trata de la definición de secreto de Key Vault.
7. Seleccione **Descartar** en la parte inferior de la página. No ha realizado ningún cambio.
8. Compruebe que ha especificado todos los valores como se muestra en la captura de pantalla anterior y haga clic en **Comprar** en la parte inferior de la página.
9. Seleccione el icono de la campana (notificación) en la parte superior de la página para abrir el panel **Notificaciones**. Espere hasta que el recurso se implemente correctamente.
10. Seleccione **Ir al grupo de recursos** en el panel **Notificaciones**. 
11. Seleccione el nombre del almacén de claves para abrirlo.
12. Seleccione **Secretos** en el panel izquierdo. **vmAdminPassword** debe aparecer allí.
13. Seleccione **Directivas de acceso** en el panel izquierdo. Su nombre (Active Directory) debe aparecer en la lista. Si no aparece, significa que no tiene permiso para acceder al almacén de claves.
14. Seleccione **Haga clic para mostrar las directivas de acceso avanzado**. Observe que **Habilitar el acceso a Azure Resource Manager para la implementación de plantillas** está seleccionado. Este valor es otra condición para que la integración con Key Vault funcione.

    ![Directivas de acceso de la integración de Key Vault en la plantilla de Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. Seleccione **Propiedades** en el panel izquierdo.
16. Realice una copia del valor de **Identificador de recurso**. Este identificador es necesario cuando se implementa la máquina virtual.  El formato del identificador de recurso es:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>Abra una plantilla de inicio rápido.

Las plantillas de inicio rápido de Azure consisten en un repositorio de plantillas de Resource Manager. En lugar de crear una plantilla desde cero, puede buscar una plantilla de ejemplo y personalizarla. La plantilla que se usa en este tutorial se denomina [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. En Visual Studio Code, seleccione **Archivo**>**Abrir archivo**.
2. En **Nombre de archivo**, pegue el código URL siguiente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Seleccione **Abrir** para abrir el archivo. Es el mismo escenario que se ha usado en [Tutorial: Creación de plantillas de Azure Resource Manager con recursos dependientes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. La plantilla define cinco recursos:

   * `Microsoft.Storage/storageAccounts`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consulte la [referencia de plantilla](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Resulta útil obtener cierta información básica de la plantilla antes de personalizarla.
5. Seleccione **Archivo**>**Guardar como** para guardar una copia del archivo en la máquina local con el nombre **azuredeploy.json**.
6. Repita los pasos 1 a 4 para abrir la dirección URL siguiente y, después, guarde el archivo como **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edición del archivo de parámetros

No es necesario hacer ningún cambio en el archivo de plantilla.

1. Abra **azuredeploy.parameters.json** en Visual Studio Code, en caso de que no esté abierto.
2. Actualice el parámetro **adminPassword** a:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    Reemplace **id** por el identificador del recurso del almacén de claves que creó en el último procedimiento.  

    ![Integrar el almacén de claves y el archivo de parámetros de implementación de máquina virtual de la plantilla de Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Asigne los valores a:

    * **adminUsername**: nombre de la cuenta de administrador de la máquina virtual.
    * **dnsLabelPrefix**: nombre de dnsLabelPrefix.
4. Guarde los cambios.

## <a name="deploy-the-template"></a>Implementación de la plantilla

Para implementar la plantilla, siga las instrucciones que se indican en [Implementación de la plantilla](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Es preciso cargar **azuredeploy.json** y **azuredeploy.parameters.json** en el shell en la nube y, después, usar el siguiente script de PowerShell para implementar la plantilla:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Al implementar la plantilla, use el mismo grupo de recursos que el almacén de claves. Esto facilita la limpieza de los recursos. Solo hay que eliminar un grupo de recursos, en lugar de dos.

## <a name="valid-the-deployment"></a>Validación de la implementación

Cuando haya implementado correctamente la máquina virtual, use la contraseña almacenada en el almacén de claves para probar el inicio de sesión.

1. Abra [Azure Portal](https://portal.azure.com).
2. Seleccione **Grupos de recursos**/**NombreDeSuGrupoDeRecursos>**/**simpleWinVM**
3. Seleccione **conectar** en la parte superior.
4. Seleccione **Descargar archivo RDP** y siga las instrucciones para iniciar sesión en la máquina virtual con la contraseña almacenada en el almacén de claves.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, limpie los recursos que implementó eliminando el grupo de recursos.

1. En Azure Portal, seleccione **Grupos de recursos** en el menú de la izquierda.
2. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
3. Seleccione el nombre del grupo de recursos.  Verá un total de seis recursos en el grupo de recursos.
4. Seleccione **Eliminar grupo de recursos** del menú superior.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha recuperado un secreto de Azure Key Vault y lo ha usado en la implementación de una plantilla.  Para aprender a crear plantillas vinculadas, consulte:

> [!div class="nextstepaction"]
> [Creación de plantillas vinculadas](./resource-manager-tutorial-create-linked-templates.md)
