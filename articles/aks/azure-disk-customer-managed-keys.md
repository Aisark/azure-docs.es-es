---
title: Uso de una clave administrada por el cliente para cifrar discos de Azure en Azure Kubernetes Service (AKS)
description: Traiga sus propias claves (BYOK) para cifrar el sistema operativo y los discos de datos en AKS.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 23a5dbf2333ca86c2d51d54bf983b00a71936eec
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547955"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traiga sus propias claves (BYOK) con discos de Azure en Azure Kubernetes Service (AKS)

Azure Storage cifra todos los datos de las cuentas de almacenamiento en reposo. De manera predeterminada, los datos se cifran con claves administradas por Microsoft. Para tener un mayor control sobre las claves de cifrado, puede proporcionar [claves administradas por el cliente][customer-managed-keys] que puede usar para el cifrado tanto del sistema operativo como de los discos de datos para los clústeres de AKS.

> [!NOTE]
> Los clústeres de AKS basados en Linux y Windows BYOK están disponibles en [regiones de Azure][supported-regions] que admiten el cifrado del lado servidor de Azure Managed Disks.

## <a name="before-you-begin"></a>Antes de empezar

* En este artículo se da por supuesto que va a crear un *nuevo clúster de AKS*.

* Debe habilitar la protección contra el purgado y la eliminación temporal para *Azure Key Vault* al usar Key Vault para cifrar los discos administrados.

* Necesita la versión 2.0.79 de la CLI de Azure u otra versión posterior y la versión 0.4.26 de la extensión aks-preview.

> [!IMPORTANT]
> Las características en vista previa de AKS son de autoservicio y se tienen que habilitar. Las versiones preliminares se proporcionan "tal cual" y "como están disponibles", y están excluidas de los contratos de nivel de servicio y la garantía limitada. Las versiones preliminares de AKS reciben cobertura parcial del soporte al cliente en la medida de lo posible. Por lo tanto, estas características no están diseñadas para usarse en producción. Para obtener información adicional, consulte los siguientes artículos de soporte:
>
> * [Directivas de soporte técnico para AKS](support-policies.md)
> * [Preguntas más frecuentes de soporte técnico de Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instalación de la extensión de la versión preliminar de la CLI de AKS más reciente

Para usar claves administradas por el cliente, necesita la versión 0.4.26 o posterior de la extensión *aks-preview* de la CLI. Instale la extensión de la CLI de Azure *aks-preview* con el comando [az extension add][az-extension-add] y, a continuación, busque las actualizaciones disponibles con el comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Creación de una instancia de Azure Key Vault

Use una instancia de Azure Key Vault para almacenar las claves.  Opcionalmente, puede usar Azure Portal para [configurar claves administradas por el cliente con Azure Key Vault mediante Azure Portal][byok-azure-portal].

Cree un nuevo *grupo de recursos* y luego cree una nueva instancia de *Key Vault* y habilite la protección contra el purgado y la eliminación temporal.  Asegúrese de usar los mismos nombres de región y grupo de recursos para cada comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Creación de una instancia de DiskEncryptionSet

Reemplace *myKeyVaultName* por el nombre del almacén de claves.  También necesitará una *clave* almacenada en Azure Key Vault para completar los pasos siguientes.  Almacene la clave existente en la instancia de Key Vault que creó en los pasos anteriores, o [genere una nueva clave][key-vault-generate] y reemplace *myKeyName* a continuación por el nombre de la clave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Concesión de acceso a DiskEncryptionSet al almacén de claves

Use DiskEncryptionSet y los grupos de recursos que creó en los pasos anteriores y conceda al recurso DiskEncryptionSet acceso a Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Creación de un nuevo clúster de AKS y cifrado del disco del sistema operativo

Cree una **nuevo grupo de recursos** y el clúster de AKS y luego use la clave para cifrar el disco del sistema operativo. Las claves administradas por el cliente solo se admiten en versiones de Kubernetes posteriores a la 1.17. 

> [!IMPORTANT]
> Asegúrese de crear un nuevo grupo de recursos para el clúster de AKS.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Cuando se agregan nuevos grupos de nodos al clúster creado anteriormente, la clave administrada por el cliente proporcionada durante la creación se usa para cifrar el disco del sistema operativo.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Cifrado del disco de datos del clúster de AKS

También puede cifrar los discos de datos de AKS con sus propias claves.

> [!IMPORTANT]
> Asegúrese de que tiene las credenciales de AKS adecuadas. La entidad de servicio deberá tener acceso de colaborador al grupo de recursos donde se implementa diskencryptionset. De lo contrario, obtendrá un error que sugiere que la entidad de servicio no tiene permisos.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Cree un archivo llamado **byok-azure-disk.yaml** que contenga la siguiente información.  Reemplace myAzureSubscriptionId, myResourceGroup y myDiskEncrptionSetName por sus valores y aplique YAML.  Asegúrese de usar el grupo de recursos donde DiskEncryptionSet está implementado.  Si usa Azure Cloud Shell, este archivo se puede crear mediante vi o nano como si funcionara en un sistema físico o virtual:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Después, ejecute esta implementación en el clúster de AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitaciones

* BYOK solo está disponible actualmente en disponibilidad general y versión preliminar en determinadas [regiones de Azure][supported-regions].
* Cifrado de disco del sistema operativo compatible con la versión 1.17 de Kubernetes y versiones posteriores   
* Disponible solo en las regiones en las que se admite BYOK
* Actualmente, el cifrado con claves administradas por el cliente solo es para los nuevos clústeres de AKS; los clústeres existentes no se pueden actualizar
* Se requiere un clúster de AKS que use Virtual Machine Scale Sets; no se admite para Virtual Machine Scale Sets


## <a name="next-steps"></a>Pasos siguientes

Revise los [procedimientos recomendados para la seguridad de los clústeres de AKS][best-practices-security].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
