---
title: Cifrado del lado servidor de Azure Managed Disks - CLI de Azure
description: Azure Storage protege los datos mediante su cifrado en reposo antes de guardarlos en los clústeres de Storage. Puede confiar en las claves administradas por Microsoft para el cifrado de los discos administrados, o puede usar claves administradas por el cliente para administrar el cifrado con sus propias claves.
author: roygara
ms.date: 01/13/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 495bdcfb619ff17a4a4b074fa673c5d2fb185730
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970529"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Cifrado del lado servidor de Azure Managed Disks

Los discos administrados de Azure cifran automáticamente los datos de forma predeterminada cuando se guardan en la nube. El cifrado del lado servidor protege los datos y le ayuda a cumplir los compromisos de cumplimiento y seguridad de la organización. Los datos de los discos administrados de Azure se cifran de forma transparente mediante [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más sólidos que hay disponibles, y son compatibles con FIPS 140-2.   

El cifrado no afecta al rendimiento de los discos administrados. No hay ningún coste adicional por el cifrado.

Para más información sobre de los módulos criptográficos subyacentes en los discos administrados de Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Información sobre la administración de claves de cifrado

Puede confiar en las claves administradas por la plataforma para el cifrado del disco administrado o puede administrar el cifrado con sus propias claves. Si opta por administrar el cifrado con sus propias claves, puede especificar una *clave administrada por el cliente* que se usará para cifrar y descifrar todos los datos de discos administrados. 

En las secciones siguientes se describe cada una de las opciones de administración de claves con mayor detalle.

## <a name="platform-managed-keys"></a>Claves administradas por la plataforma

De forma predeterminada, los discos administrados usan claves de cifrado administradas por la plataforma. A partir del 10 de junio de 2017, todos los nuevos discos administrados, instantáneas e imágenes, así como los datos nuevos que se escriban en discos administrados existentes, se cifran automáticamente en reposo con claves administradas por la plataforma. 

## <a name="customer-managed-keys"></a>Claves administradas por el cliente

Puede optar por administrar el cifrado en el nivel de cada disco administrado, con sus propias claves. El cifrado del lado servidor de discos administrados con claves administradas por el cliente ofrece una experiencia integrada con Azure Key Vault. Puede importar [las claves RSA](../../key-vault/key-vault-hsm-protected-keys.md) a su instancia de Key Vault o generar nuevas claves RSA en Azure Key Vault. Azure Managed Disks controla el cifrado y descifrado de forma totalmente transparente con [cifrado de sobre](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Cifra los datos con una clave de cifrado de datos (DEK) basada en [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, que, a su vez, está protegida con las claves del usuario. Tiene que conceder acceso a los discos administrados en su instancia de Key Vault para usar sus propias claves para cifrar y descifrar la clave DEK. Esto le permite controlar completamente los datos y las claves. Puede deshabilitar las claves o revocar el acceso a los discos administrados en cualquier momento. También puede auditar el uso de la clave de cifrado con la supervisión de Azure Key Vault para asegurarse de que solo los discos administrados u otros servicios de Azure de confianza tengan acceso a las claves.

En el siguiente diagrama se muestra cómo los discos administrados utilizan Azure Active Directory y Azure Key Vault para realizar solicitudes con la clave administrada por el cliente:

![Flujo de trabajo de discos administrados y claves administradas por el cliente. Un administrador crea una instancia de Azure Key Vault, después crea un conjunto de cifrado de discos y configura el conjunto de cifrado de discos. El conjunto está asociado a una VM que permite que el disco haga uso de Azure AD para autenticarse.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


En la lista siguiente se explica el diagrama más detalladamente:

1. Un administrador de Azure Key Vault crea recursos de almacén de claves.
1. El administrador del almacén de claves importa sus claves RSA a Key Vault o genera nuevas claves RSA en Key Vault.
1. Ese administrador crea una instancia del recurso Conjunto de cifrado de disco y especifica un identificador de Azure Key Vault y una dirección URL de clave. Conjunto de cifrado de disco es un nuevo recurso que se ha introducido para simplificar la administración de claves de los discos administrados. 
1. Cuando se crea un conjunto de cifrado de disco, se crea una [identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/overview.md) en Azure Active Directory (AD) y se asocia al conjunto de cifrado de disco. 
1. El administrador de Azure Key Vault concede entonces al administrador de la identidad administrada permiso para realizar operaciones en el almacén de claves.
1. Un usuario de máquina virtual crea discos asociándolos con el conjunto de cifrado de disco. El usuario de la máquina virtual también puede habilitar el cifrado del lado servidor con claves administradas por el cliente para los recursos existentes asociándolos al conjunto de cifrado de disco. 
1. Los discos administrados usan la identidad administrada para enviar solicitudes a la instancia de Azure Key Vault.
1. Para leer o escribir datos, los discos administrados envían solicitudes a Azure Key Vault para cifrar (encapsular) y descifrar (desencapsular) la clave de cifrado de datos con el fin de realizar el cifrado y descifrado de los datos. 

Para revocar el acceso a las claves administradas por el cliente, vea [PowerShell de Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) y [CLI de Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revocación del acceso bloquea de manera eficaz el acceso a todos los datos de la cuenta de almacenamiento, ya que Azure Storage no puede acceder a la clave de cifrado.

### <a name="supported-regions"></a>Regiones admitidas

Actualmente, solo se admiten las siguientes regiones:

- Disponible como una oferta de disponibilidad general en las regiones del Este de EE. UU., Oeste de EE. UU. 2 y Centro-sur de EE. UU.
- Disponible como versión preliminar pública en las regiones Centro-oeste de EE. UU., Este de EE. UU. 2, Centro de Canadá y Norte de Europa.

### <a name="restrictions"></a>Restricciones

Por ahora, las claves administradas por el cliente tienen las siguientes restricciones:

- Solo se admiten las [claves RSA "suaves" y "fuertes"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) con el tamaño 2080, sin incluir ninguna otra clave ni tamaño.
- Los discos creados a partir de imágenes personalizadas que están cifradas con cifrado del lado servidor y las claves administradas por el cliente deben cifrarse con las mismas claves administradas por el cliente y deben estar en la misma suscripción.
- Las instantáneas creadas a partir de discos que están cifrados con cifrado del lado servidor y claves administradas por el cliente deben cifrarse con las mismas claves administradas por el cliente.
- Las imágenes personalizadas cifradas con cifrado del lado servidor y claves administradas por el cliente no se pueden usar en la galería de imágenes compartidas.
- Todos los recursos relacionados con las claves administradas por el cliente (instancias de Azure Key Vault, conjuntos de cifrado de disco, máquinas virtuales, discos e instantáneas) deben estar en la misma suscripción y región.
- Los discos, las instantáneas y las imágenes cifrados con claves administradas por el cliente no se pueden trasladar a otra suscripción.
- Si usa Azure Portal para crear el conjunto de cifrado de disco, no puede usar instantáneas por ahora.
- Los discos administrados cifrados mediante claves administradas por el cliente tampoco se pueden cifrar con Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuración de Azure Key Vault y DiskEncryptionSet

1. Asegúrese de que ha instalado la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) e iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).

1. Cree una instancia de Azure Key Vault y la clave de cifrado.

    Al crear la instancia de Key Vault, debe habilitar la eliminación temporal y la protección de purgas. La eliminación temporal garantiza que la instancia de Key Vault conserva una clave eliminada durante un período de retención determinado (valor predeterminado de 90 días). La protección de purgas garantiza que una clave eliminada no se puede eliminar permanentemente hasta que transcurra el período de retención. Esta configuración le protege contra la pérdida de datos debido a la eliminación accidental. Estos valores son obligatorios cuando se usa una instancia de Key Vault para cifrar discos administrados.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  Cree una instancia de un objeto DiskEncryptionSet. 
    
    ```azurecli
    keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
    ```

1.  Conceda al recurso DiskEncryptionSet acceso al almacén de claves. 

    > [!NOTE]
    > Azure puede tardar unos minutos en crear la identidad del elemento DiskEncryptionSet en Azure Active Directory. Si recibe un error similar a "No se encuentra el objeto en Active Directory" al ejecutar el siguiente comando, espere unos minutos y vuelva a intentarlo.

    ```azurecli
    desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creación de una máquina virtual con una imagen de Marketplace, cifrado del sistema operativo y de los discos de datos con claves administradas por el cliente

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Cifrado de discos administrados no conectados existentes 

Los discos existentes no deben estar asociados a una máquina virtual en ejecución para que los cifre mediante el siguiente script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creación de un conjunto de escalado de máquinas virtuales con una imagen de Marketplace, cifrado del sistema operativo y de los discos de datos con claves administradas por el cliente

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Creación de un disco vacío cifrado con cifrado del lado servidor y claves administradas por el cliente y su conexión a una máquina virtual

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> Las claves administradas por el cliente dependen de identidades administradas para los recursos de Azure, una característica de Azure Active Directory (Azure AD). Al configurar claves administradas por el cliente, se asigna automáticamente una identidad administrada a los recursos en segundo plano. Si posteriormente mueve la suscripción, el grupo de recursos o el disco administrado de un directorio de Azure AD a otro, la identidad administrada asociada a los discos administrados no se transfiere al nuevo inquilino, por lo que es posible que las claves administradas por el cliente dejen de funcionar. Para obtener más información, consulte [Transferencia de una suscripción entre directorios de Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Las claves administradas por el cliente dependen de identidades administradas para los recursos de Azure, una característica de Azure Active Directory (Azure AD). Al configurar claves administradas por el cliente, se asigna automáticamente una identidad administrada a los recursos en segundo plano. Si posteriormente mueve la suscripción, el grupo de recursos o el disco administrado de un directorio de Azure AD a otro, la identidad administrada asociada a los discos administrados no se transfiere al nuevo inquilino, por lo que es posible que las claves administradas por el cliente dejen de funcionar. Para obtener más información, consulte [Transferencia de una suscripción entre directorios de Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Cifrado del lado servidor frente a Azure Disk Encryption

[Azure Disk Encryption para máquinas virtuales y conjuntos de escalado de máquinas virtuales](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aprovecha la característica [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para cifrar los discos administrados con claves administradas por el cliente dentro de la máquina virtual invitada.  El cifrado del lado servidor con claves administradas por el cliente mejora en ADE al permitir el uso de cualquier tipo de sistema operativo y de imágenes para las máquinas virtuales mediante el cifrado de datos en el servicio Storage.

## <a name="next-steps"></a>Pasos siguientes

- [Explore las plantillas de Azure Resource Manager para crear discos cifrados con claves administradas por el cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [¿Qué es Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Replicación de máquinas con discos habilitados para claves administradas por el cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configuración de la recuperación ante desastres de máquinas virtuales de VMware en Azure con PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configuración de la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V mediante PowerShell y Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
