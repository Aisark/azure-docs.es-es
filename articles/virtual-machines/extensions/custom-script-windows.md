---
title: Extensión de script personalizado de Azure para Windows | Microsoft Docs
description: Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/15/2019
ms.author: gwallace
ms.openlocfilehash: e2b36633996f961d100f0a98abb09135fd4393e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869867"
---
# <a name="custom-script-extension-for-windows"></a>Extensión de la secuencia de comandos personalizada para Windows

La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración de implementación de post, instalación de software, cualquier configuración o las tareas de administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. La extensión Script personalizado se integra con las plantillas de Azure Resource Manager y se pueden ejecutar con la API de REST de máquina Virtual de Azure, Azure portal, PowerShell o la CLI de Azure.

En este documento se detalla cómo usar la extensión de script personalizado mediante el módulo de Azure PowerShell y plantillas de Azure Resource Manager, y se detallan también los pasos para solucionar problemas en los sistemas Windows.

## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]  
> No use la extensión de script personalizado para ejecutar Update-AzVM con la misma VM como su parámetro, ya tendrá que hacerlo ella misma.  

### <a name="operating-system"></a>Sistema operativo

La extensión de Script personalizado para Windows se ejecuta en la extensión de la extensión admite sistemas operativos, para obtener más información, consulte este [sistemas operativos compatibles con la extensión Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Ubicación del script

Puede configurar la extensión para usar las credenciales de almacenamiento de blobs de Azure para acceder al almacenamiento de blobs de Azure. La ubicación del script puede ser cualquier lugar, siempre y cuando la máquina virtual puede enrutar a ese punto final, como GitHub o un servidor de archivos interno.

### <a name="internet-connectivity"></a>Conectividad de Internet

Si necesita descargar un script externamente, como desde GitHub o almacenamiento de Azure, a continuación, adicional de firewall y los puertos del grupo de seguridad de red deben abrirse. Por ejemplo, si el script se encuentra en el almacenamiento de Azure, puede permitir el acceso mediante etiquetas de servicio de NSG de Azure para [almacenamiento](../../virtual-network/security-overview.md#service-tags).

Si el script está en un servidor local, a continuación, puede que deba adicional de firewall y es necesario abrir puertos del grupo de seguridad de red.

### <a name="tips-and-tricks"></a>Trucos y sugerencias

* La mayor tasa de error para esta extensión es debido a errores de sintaxis en el script de prueba, el script se ejecuta sin errores y también coloca en el inicio de sesión en el script para que sea más fácil encontrar dónde se produjo un error adicional.
* Escribir scripts que son idempotentes. Esto garantiza que si se ejecutan de nuevo accidentalmente, no hará que los cambios del sistema.
* Asegúrese de que los scripts no requieran intervención del usuario cuando se ejecutan.
* Los scripts tienen permitido un plazo de 90 minutos para ejecutarse; todo lo que dure más provocará un error de aprovisionamiento de la extensión.
* No incluya reinicios en el script, ya que esta acción provocará errores con otras extensiones que se instalen. Después del reinicio, la extensión no continuará después del reinicio.
* Si tiene un script que se provoca un rearranque, a continuación, instalar aplicaciones y ejecutar scripts, puede programar el reinicio con una tarea programada de Windows, o usar herramientas como las extensiones de DSC, Chef o Puppet.
* La extensión solo ejecutará un script una vez. Si quiere ejecutar un script en cada inicio, debe usar la extensión para crear una tarea programada de Windows.
* Si quiere programar cuándo se ejecutará un script, debe usar la extensión para crear una tarea programada de Windows.
* Cuando el script se esté ejecutando, solo verá un estado de extensión "en transición" desde Azure Portal o la CLI. Si quiere recibir actualizaciones de estado más frecuentes de un script en ejecución, debe crear su propia solución.
* La extensión de script personalizada no admite de forma nativa servidores proxy, pero puede usar una herramienta de transferencia de archivos que admita servidores proxy en el script, como *Curl*.
* Tenga en cuenta las ubicaciones de directorio no predeterminadas en las que se puedan basar los scripts o comandos y aplique lógica para controlar esta situación.

## <a name="extension-schema"></a>Esquema de extensión

La configuración de la extensión de script personalizado especifica aspectos como la ubicación del script y el comando que se ejecutará. Esta configuración se puede almacenar en archivos de configuración, o se puede especificar en la línea de comandos o en una plantilla de Azure Resource Manager.

Los datos confidenciales se pueden almacenar en una configuración protegida, que se cifra y se descifra solo dentro de la máquina virtual. La configuración protegida es útil cuando el comando de ejecución incluye secretos tales como una contraseña.

Estos elementos se deben tratar como datos confidenciales y se deben especificar en la configuración protegida de las extensiones. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Solo una versión de una extensión puede instalarse en una máquina virtual en un momento dado, especificar un script personalizado dos veces en la misma plantilla de Resource Manager para la misma máquina virtual se producirá un error.

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo | Tipo de datos |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| Tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (p. ej.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (p. ej.) | 123456789 | Entero de 32 bits |
| commandToExecute (p. ej.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (p. ej.) | examplestorageacct | string |
| storageAccountKey (p. ej.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Para evitar problemas de implementación, use los nombres como se muestran aquí.

#### <a name="property-value-details"></a>Detalles del valor de propiedad

* `commandToExecute` (**necesario**, cadena): script de punto de entrada que se va a ejecutar. Use este campo si el comando contiene secretos, como contraseñas, o si los fileUris son confidenciales.
* `fileUris` (opcional, matriz de cadenas): direcciones URL de los archivos que se van a descargar.
* `timestamp` (opcional, entero de 32 bits) use este campo solo para desencadenar una nueva ejecución del script; para ello, cambie el valor de este campo.  Se acepta cualquier valor entero; solo debe ser distinto del valor anterior.
* `storageAccountName` (opcional, cadena): nombre de la cuenta de almacenamiento. Si especifica credenciales de almacenamiento, todos los valores de `fileUris` deben ser direcciones URL de blobs de Azure.
* `storageAccountKey` (opcional, cadena): clave de acceso de la cuenta de almacenamiento.

Los valores siguientes se pueden establecer en la configuración pública o protegida. La extensión rechazará una configuración si los valores siguientes están establecidos en la configuración tanto pública como protegida.

* `commandToExecute`

Mediante la configuración pública puede resultar útil para la depuración, pero se recomienda que use configuración protegida.

La configuración pública se envía en texto no cifrado a la máquina virtual donde se ejecutará el script.  La configuración protegida se cifra con una clave que solo conocen Azure y la máquina virtual. La configuración se guarda en la máquina virtual que se enviaron, es decir, si los valores cifrados se guardan cifrada en la máquina virtual. El certificado usado para descifrar los valores cifrados se almacena en la máquina virtual y se usa para descifrar la configuración (si es necesario) en tiempo de ejecución.

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON, que se detalla en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión Script personalizado durante la implementación. En los ejemplos siguientes se muestra cómo usar la extensión de script personalizado:

* [Tutorial: Implementación de extensiones de máquina virtual con plantillas de Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Deploy Two Tier Application on Windows and Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Implementación de aplicaciones de dos niveles en Windows y Azure SQL DB)

## <a name="powershell-deployment"></a>Implementación de PowerShell

El comando `Set-AzVMCustomScriptExtension` se puede usar para agregar la extensión de script personalizado a una máquina virtual existente. Para más información, consulte [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Ejemplos adicionales

### <a name="using-multiple-scripts"></a>Usar varios scripts

En este ejemplo, tener tres secuencias de comandos que se usan para el servidor de compilación. El **commandToExecute** llama a la primera secuencia de comandos, tendrá opciones sobre cómo se llaman a los demás. Por ejemplo, puede tener un script maestro que controla la ejecución con el control de errores adecuado, registro y administración de Estados. Los scripts se descargan en el equipo local para ejecutar. Por ejemplo, en `1_Add_Tools.ps1` se llamaría a `2_Add_Features.ps1` agregando `.\2_Add_Features.ps1` la secuencia de comandos y repita este proceso para los demás scripts se define en `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Ejecutar scripts desde un recurso compartido local

En este ejemplo, desea utilizar un servidor local de SMB para la ubicación del script. Al hacerlo, no es necesario proporcionar cualquier otra configuración, excepto **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Cómo ejecutar un script personalizado más de una vez con la CLI

Si quiere ejecutar la extensión de script personalizado más de una vez, solo puede hacer esta acción si se cumplen las condiciones siguientes:

* La extensión **nombre** parámetro es igual que la implementación anterior de la extensión.
* Actualice la configuración en caso contrario, que no se vuelve a ejecutar el comando. Puede agregar una propiedad dinámica en el comando, como una marca de tiempo.

## <a name="classic-vms"></a>Máquinas virtuales clásicas

Para implementar la extensión de Script personalizado en las máquinas virtuales clásicas, puede usar el portal de Azure o los cmdlets de PowerShell de Azure clásico.

### <a name="azure-portal"></a>Azure Portal

Navegue al recurso de máquina virtual clásica. Seleccione **extensiones** en **configuración**.

Haga clic en **+ agregar** y en la lista de recursos, elija **extensión de Script personalizado**.

En el **instalar extensión** , seleccione el archivo de PowerShell local y rellene los argumentos y haga clic en **Aceptar**.

### <a name="powershell"></a>PowerShell

Use la [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet puede usarse para agregar la extensión de Script personalizado a una máquina virtual existente.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y soporte técnico

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante el módulo Azure PowerShell. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Resultados de la extensión se registran en los archivos que se encuentra en la siguiente carpeta en la máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Se descargan los archivos especificados en la siguiente carpeta en la máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

donde `<n>` es un entero decimal que puede variar entre las ejecuciones de la extensión.  El valor `1.*` coincide con el valor `typeHandlerVersion` actual y real de la extensión.  Por ejemplo, el directorio real podría ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Cuando se ejecute el comando `commandToExecute`, la extensión establece este directorio (por ejemplo, `...\Downloads\2`) como directorio de trabajo actual. Este proceso permite el uso de rutas de acceso relativas para buscar los archivos descargados mediante la propiedad `fileURIs`. En la tabla siguiente se muestran algunos ejemplos.

Dado que la ruta de acceso absoluta de descarga puede variar con el paso del tiempo, es mejor optar por rutas de acceso relativas de archivo o script en la cadena `commandToExecute`, siempre que sea posible. Por ejemplo: 

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Información de ruta de acceso después de que se mantiene el primer segmento URI para los archivos descargados a través de la `fileUris` lista de propiedades.  Como se muestra en la tabla siguiente, los archivos descargados se asignan en los subdirectorios de descarga para reflejar la estructura de los valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Ejemplos de archivos descargados

| URI en fileUris | Ubicación descargada relativa | Ubicación descargada absoluta <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> cambian las rutas de acceso absoluta del directorio durante la vigencia de la máquina virtual, pero no dentro de una ejecución única de la extensión CustomScript.

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). También puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).