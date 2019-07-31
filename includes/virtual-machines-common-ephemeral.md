---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805862"
---
Los discos del sistema operativo efímeros se crean en el almacenamiento local de la máquina virtual y no se guardan en la instancia remota de Azure Storage. Estos discos están indicados para cargas de trabajo sin estado, donde las aplicaciones toleran errores de máquinas virtuales individuales, pero tienen más en cuenta el tiempo de implementación de las máquinas virtuales o el restablecimiento de la imagen inicial de dichas máquinas. Con los discos del sistema operativo efímeros, observará una latencia de lectura y escritura inferior en el disco del sistema operativo y un restablecimiento más rápido de la imagen inicial de la máquina virtual. 
 
Las principales características de los discos efímeros son: 
- Perfecto para las aplicaciones sin estado.
- Se pueden usar con imágenes de Marketplace y personalizadas.
- Posibilidad de restablecimiento rápido o de restablecimiento de la imagen inicial de las máquinas virtuales y de las instancias de conjunto de escalado al estado de arranque original.  
- Latencia inferior, similar a la de un disco temporal. 
- Los discos del sistema operativo efímeros son gratuitos, es decir, no generan costos de almacenamiento.
- Están disponibles en todas las regiones de Azure. 
- El disco de sistema operativo efímero es compatible con [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Principales diferencias entre discos del sistema operativo efímeros y persistentes:

|                             | Disco del sistema operativo persistente                          | Disco de sistema operativo efímero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Límite de tamaño del disco del sistema operativo      | 2 TiB                                                                                        | El tamaño de caché para el tamaño de máquina virtual o 2 TiB, el que sea menor. Para el **tamaño de caché en GiB**, consulte [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md) y [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series).              |
| Tamaños de máquina virtual admitidos          | Todo                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Compatibilidad con los tipos de discos           | Disco del sistema operativo administrado y no administrado                                                                | Solo disco del sistema operativo administrado                                                               |
| Regiones admitidas              | Todas las regiones                                                                                  | Todas las regiones                              |
| Persistencia de los datos            | Los datos del disco del sistema operativo escritos en un disco del sistema operativo se almacenan en Azure Storage.                                  | Los datos escritos en un disco del sistema operativo se almacenan en el almacenamiento de máquina virtual local y no se conservan en Azure Storage. |
| Estado detenido (desasignado)      | Las máquinas virtuales y las instancias del conjunto de escalado pueden estar detenidas (desasignadas) y reiniciarse a partir de este estado. | Las máquinas virtuales y las instancias del conjunto de escalado no pueden estar detenidas (desasignadas).                                  |
| Compatibilidad con discos del sistema operativo especializados | Sí                                                                                          | Sin                                                                                 |
| Cambio de tamaño del disco del sistema operativo              | Se admite durante la creación de la máquina virtual y después de que esta se detiene (desasigna).                                | Se admite solo durante la creación de la máquina virtual                                                  |
| Cambio a un nuevo tamaño de máquina virtual   | Se conservan los datos del disco del sistema operativo                                                                    | Se eliminan los datos del disco del sistema operativo, se vuelve a aprovisionar el sistema operativo.                                      |

## <a name="size-requirements"></a>Requisitos de tamaño

Puede implementar imágenes de instancias y de máquinas virtuales hasta el tamaño de la caché de máquina virtual. Por ejemplo, las imágenes estándar de Windows Server de Marketplace son de aproximadamente 127 GiB, lo que significa que necesita un tamaño de máquina virtual que tenga una caché de más de 127 GiB. En este caso, las máquinas virtuales [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) tienen un tamaño de caché de 86 GiB, que no es lo suficientemente grande. Las máquinas virtuales Standard_DS2_v2 tienen un tamaño de caché de 172 GiB, que es lo suficientemente grande. En este caso, Standard_DS3_v2 tienen el tamaño menor de la serie DSv2 que se puede usar con esta imagen. Las imágenes básicas de Linux en Marketplace y las imágenes de Windows Server indicadas por `[smallsize]` tienden a tener alrededor de 30 GiB y pueden usar la mayoría de los tamaños de máquinas virtuales disponibles.

Los discos efímeros también requieren que el tamaño de la máquina virtual admita Premium Storage. Los tamaños normalmente (pero no siempre) tienen un `s` en el nombre, como DSv2 y EsV3. Para más información, consulte [Tamaños de máquinas virtuales de Azure](../articles/virtual-machines/linux/sizes.md) para más información sobre qué tamaños se admiten Premium Storage.

## <a name="powershell"></a>PowerShell

Para usar un disco efímero para una implementación de máquina virtual de PowerShell, utilice [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) en la configuración de la máquina virtual. Establezca `-DiffDiskSetting` en `Local` y `-Caching` en `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para las implementaciones de conjunto de escalado, use el cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) en la configuración. Establezca `-DiffDiskSetting` en `Local` y `-Caching` en `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar un disco efímero para una implementación de la máquina virtual de la CLI, establezca el parámetro `--ephemeral-os-disk` de [az vm create](/cli/azure/vm#az-vm-create) en `true` y el parámetro `--os-disk-caching` en `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Para los conjuntos de escalado, use el mismo parámetro `--ephemeral-os-disk true` para [az vmss create](/cli/azure/vmss#az-vmss-create) y establezca el parámetro `--os-disk-caching` en `ReadOnly`.

## <a name="portal"></a>Portal   

En Azure Portal, puede elegir usar discos efímeros al implementar una máquina virtual, abra la sección **Advanced** (Avanzadas) de la pestaña **Disks** (Discos). Para **Use ephemeral OS disk** (Utilizar disco de sistema operativo efímero) seleccione **Yes** (Sí).

![Captura de pantalla que muestra el botón de radio para elegir un disco de sistema operativo efímero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Si la opción de utilizar un disco efímero está atenuada, es posible que haya seleccionado un tamaño de máquina virtual que no tenga un tamaño de caché mayor que la imagen del sistema operativo o que no admita Premium Storage. Vuelva a la página **Basics** (Aspectos básicos) y pruebe con otro tamaño de máquina virtual.

También puede crear conjuntos de escalado con discos de sistema operativo efímeros mediante el portal. Asegúrese de seleccionar un tamaño de máquina virtual que tenga un tamaño de caché lo suficientemente grande y, a continuación, en **Use ephemeral OS disk** (Utilizar disco de sistema operativo efímero) seleccione **Yes** (Sí).

![Captura de pantalla que muestra el botón de radio para elegir un disco de sistema operativo efímero para el conjunto de escalado](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementación de plantillas de conjunto de escalado  
El proceso para crear un conjunto de escalado que use un disco del sistema operativo efímero consiste en agregar la propiedad `diffDiskSettings` al tipo de recurso `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` en la plantilla. Además, la directiva de almacenamiento en caché debe establecerse en `ReadOnly` para el disco del sistema operativo efímero. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Implementación de plantillas de máquinas virtuales 
Puede implementar una máquina virtual con un disco del sistema operativo efímero mediante una plantilla. El proceso para crear una máquina virtual que use discos del sistema operativo efímeros consiste en agregar la propiedad `diffDiskSettings` al tipo de recurso Microsoft.COMPUTE/virtualmachines en la plantilla. Además, la directiva de almacenamiento en caché debe establecerse en `ReadOnly` para el disco del sistema operativo efímero. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Restablecimiento de la imagen inicial de una máquina virtual mediante REST
Actualmente, el único método para restablecer la imagen inicial de una instancia de máquina virtual con el disco del sistema operativo efímero consiste en usar la API REST. Para los conjuntos de escalado, el restablecimiento de la imagen inicial ya está disponible mediante PowerShell, la CLI y el portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P: ¿Cuál es el tamaño de los discos del sistema operativo locales?**

R: Admitiremos la plataforma o las imágenes personalizadas, hasta el tamaño de caché de la máquina virtual, donde todas las operaciones de lectura y escritura en el disco del sistema operativo serán locales en el mismo nodo que la máquina virtual. 

**P: ¿Se puede cambiar el tamaño del disco del sistema operativo efímero?**

R: No, una vez que se aprovisiona el disco del sistema operativo efímero, no puede cambiarse su tamaño. 

**P: ¿Puedo asociar un disco administrado a una máquina virtual efímera?**

R: Sí, puede asociar un disco de datos administrado a una máquina virtual que use un disco del sistema operativo efímero. 

**P: ¿Se admitirán todos los tamaños de máquina virtual con los discos del sistema operativo efímeros?**

R: No, se admiten todos los tamaños de máquina virtual Premium Storage (DS, ES, FS, GS y M), excepto los tamaños de las series B, N y H.  
 
**P: ¿Se puede aplicar el disco del sistema operativo efímero a máquinas virtuales y conjuntos de escalado existentes?**

R: No, el disco del sistema operativo efímero solo se puede usar durante la creación de la máquina virtual y el conjunto de escalado. 

**P: ¿Se pueden combinar discos del sistema operativo efímeros y normales en un conjunto de escalado?**

R: No, no se puede tener una combinación de instancias de disco del sistema operativo efímeras y persistentes dentro del mismo conjunto de escalado. 

**P: ¿Se puede crear el disco del sistema operativo efímero con PowerShell o la CLI?**

R: Sí, puede crear máquinas virtuales con discos del sistema operativo efímeros mediante REST, plantillas, PowerShell y la CLI.

**P: ¿Qué características no se admiten con los discos del sistema operativo efímeros?**

R: Los discos efímeros no admiten:
- Captura de imágenes de máquina virtual
- Instantáneas de disco. 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Intercambio de discos del sistema operativo 
