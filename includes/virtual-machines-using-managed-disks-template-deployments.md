---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581091"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Uso de discos administrados en plantillas de Azure Resource Manager

En este documento se describen las diferencias entre discos administrados y discos no administrados cuando se usan plantillas de Azure Resource Manager para aprovisionar máquinas virtuales. Los ejemplos le ayudarán a actualizar las plantillas existentes que usan discos no administrados a discos administrados. Como referencia, usamos la plantilla [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como guía. Puede ver la plantilla usando tanto [discos administrados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) como una versión usando [discos no administrados](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) si desea compararlas directamente.

## <a name="unmanaged-disks-template-formatting"></a>Formato de plantilla de discos no administrados

Para comenzar, veamos cómo se implementan los discos no administrados. Cuando se crean discos no administrados, se necesita una cuenta de almacenamiento para contener los archivos VHD. Puede crear una cuenta de almacenamiento o usar una ya existente. En este artículo aprenderá a crear una cuenta de almacenamiento. Cree un recurso de cuenta de almacenamiento en el bloque de recursos, como se muestra a continuación.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dentro del objeto de máquina virtual, agregue una dependencia de la cuenta de almacenamiento para garantizar que se cree antes que la máquina virtual. En la sección `storageProfile`, se especifica el URI completo de la ubicación de VHD, que hace referencia a la cuenta de almacenamiento y se necesita para el disco de OS y cualquier disco de datos.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formato de plantilla de discos administrados

Con Azure Managed Disks, el disco se convierte en un recurso de nivel superior y ya no es necesario que el usuario cree una cuenta de almacenamiento. Los discos de Managed Disks se publicaron primero en la versión de API `2016-04-30-preview`; están disponibles en todas las versiones posteriores de API y, ahora, son el tipo de disco predeterminado. En las secciones siguientes se describe la configuración predeterminada y se detalla cómo personalizar aún más los discos.

> [!NOTE]
> Se recomienda usar una versión de API posterior a `2016-04-30-preview`, ya que se produjeron cambios importantes entre `2016-04-30-preview` y `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Configuración predeterminada de discos administrados

Para crear una máquina virtual con discos administrados, ya no es necesario crear el recurso de cuenta de almacenamiento y es posible actualizar el recurso de máquina virtual como se indica a continuación. Observe específicamente que `apiVersion` refleja `2017-03-30` y ni `osDisk` ni `dataDisks` hacen referencia ya a un URI específico para el VHD. Cuando se realiza la implementación sin especificar propiedades adicionales, el disco usará un tipo de almacenamiento acorde con el tamaño de la máquina virtual. Por ejemplo, si usa un tamaño de máquina virtual compatible con Premium (tamaños con "s" en el nombre, como Standard_D2s_v3) el sistema usará un almacenamiento Premium_LRS. Use la configuración de la SKU del disco para especificar un tipo de almacenamiento. Si no se especifica ningún nombre, toma el formato de `<VMName>_OsDisk_1_<randomstring>` para el disco de SO y `<VMName>_disk<#>_<randomstring>` para cada disco de datos. De manera predeterminada, Azure Disk Encryption está deshabilitado; el almacenamiento en caché es Lectura/escritura en el caso del disco de SO y Ninguno para los discos de datos. En el ejemplo siguiente, puede observar que todavía existe una dependencia de cuenta de almacenamiento, pero es solo para el almacenamiento de diagnósticos y no es necesaria para almacenamiento en disco.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Uso de un recurso de disco administrado de nivel superior

Como alternativa para especificar la configuración de disco en el objeto de máquina virtual, puede crear un recurso de disco de nivel superior y adjuntarlo como parte de la creación de la máquina virtual. Por ejemplo, puede crear un recurso de disco de la manera siguiente para usarlo como un disco de datos.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro del objeto de máquina virtual, puede hacer referencia a este objeto de disco para adjuntarlo. Especificar el identificador de recurso del disco administrado que creamos en la propiedad `managedDisk` permite adjuntar el disco cuando se crea la máquina virtual. El valor de la propiedad `apiVersion` del recurso de máquina virtual está establecido en `2017-03-30`. Se agrega una dependencia del recurso de disco para garantizar que se cree correctamente antes de la creación de la máquina virtual. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Creación de conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados

Para crear conjuntos de disponibilidad administrados con máquinas virtuales con discos administrados, agregue el objeto `sku` al recurso de conjunto de disponibilidad y establezca la propiedad `name` en `Aligned`. Esta propiedad garantiza que los discos de cada máquina virtual estén suficientemente aislados entre sí para evitar puntos únicos de error. Tenga en cuenta también que el valor de la propiedad `apiVersion` del recurso del conjunto de disponibilidad está establecido en `2017-03-30`.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Discos SSD estándar

A continuación se muestran los parámetros necesarios de la plantilla de Resource Manager para crear discos SSD estándar:

* La *apiVersion* para Microsoft.Compute se debe establecer en `2018-04-01` (o posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

El siguiente ejemplo muestra la sección *properties.storageProfile.osDisk* de una máquina virtual que usa un disco SSD estándar:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Para obtener un ejemplo de plantilla completa de cómo crear un disco SSD estándar con una plantilla, consulte [Create a VM from a Windows Image with Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Creación de una máquina virtual a partir de una imagen de Windows con discos de datos SSD estándar).

### <a name="additional-scenarios-and-customizations"></a>Personalizaciones y escenarios adicionales

Para información completa sobre las especificaciones de API de REST, revise la [documentación sobre cómo crear una API de REST de disco administrado](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará escenarios adicionales, además de los valores predeterminados y aceptables que se pueden enviar a la API a través de implementaciones de plantilla. 

## <a name="next-steps"></a>Pasos siguientes

* Para conocer las plantillas completas que usan discos administrados, viste los vínculos siguientes al repositorio de inicio rápido de Azure.
    * [Máquina virtual Windows con disco administrado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Máquina virtual Linux con disco administrado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Lista completa de plantillas de discos administrados](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Visite el documento [Introducción a Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) para más información sobre los discos administrados.
* Revise la documentación de referencia de plantilla de los recursos de máquina virtual en el documento de [referencia de plantilla Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines).
* Revise la documentación de referencia de plantilla de los recursos de disco en el documento de [referencia de plantilla Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks).
* Para más información acerca del uso de discos administrados en Azure Virtual Machine Scale Sets, consulte el documento [Uso de discos de datos con conjuntos de escalado](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks).
