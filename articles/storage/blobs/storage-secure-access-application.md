---
title: Proteger el acceso a los datos de una aplicación en la nube con Azure Storage | Microsoft Docs
description: Use tokens de SAS, cifrado y HTTPS para proteger los datos de la aplicación en la nube.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 48bd21ad2f78879091419477dcc3c278d6e74711
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236636"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Proteger el acceso a los datos de una aplicación en la nube

Este tutorial es la tercera parte de una serie. Aprenda a proteger el acceso a la cuenta de almacenamiento. 

En la tercera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Usar tokens de SAS para acceder a imágenes en miniatura
> * Activar el cifrado de servidor
> * Habilitar el transporte solo HTTPS

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) proporciona un servicio sólido para almacenar archivos de aplicaciones. Este tutorial amplía [el tema anterior][previous-tutorial] para mostrar cómo proteger el acceso a la cuenta de almacenamiento desde una aplicación web. Al terminar, las imágenes están cifradas y la aplicación web usa tokens de SAS seguros para acceder a las imágenes en miniatura.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe haber completado el tutorial anterior sobre el almacenamiento: [Automatización del cambio de tamaño de imágenes cargadas mediante Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Establecimiento del acceso público a contenedores

En esta parte de la serie de tutoriales, se usan tokens de SAS para acceder a las vistas en miniatura. En este paso se establece el acceso público del contenedor _thumbnails_ a `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configuración de los tokens de SAS para las vistas en miniatura

En la primera parte de esta serie de tutoriales, la aplicación web mostraba imágenes desde un contenedor público. En esta parte de la serie, se usan tokens de [Firma de acceso compartido (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) para recuperar las imágenes en miniatura. Los tokens de SAS permiten proporcionar acceso restringido a un contenedor o blob en función del IP, el protocolo, el intervalo de tiempo o los derechos permitidos.

En este ejemplo, el repositorio de código fuente usa la rama `sasTokens`, que tiene un ejemplo de código actualizado. Elimine la implementación de GitHub existente con [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Luego configure la implementación de GitHub en la aplicación web con el comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

En el siguiente comando, `<web-app>` es el nombre de la aplicación web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

La rama `sasTokens` del repositorio actualiza el archivo `StorageHelper.cs`. Reemplaza la tarea `GetThumbNailUrls` por el ejemplo de código siguiente. La tarea actualizada recupera las direcciones URL de las vistas en miniatura al establecer un elemento [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) para especificar la hora de inicio, la hora de expiración y los permisos del token de SAS. Una vez implementada, la aplicación web recupera las vistas en miniatura con una dirección URL mediante un token de SAS. La tarea actualizada se muestra en el ejemplo siguiente:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

En la tarea anterior se usan las clases, las propiedades y los métodos siguientes:

|Clase  |Properties (Propiedades)| Métodos  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Resultados](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Permisos](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Cifrado del servidor

[Cifrado del servicio de Azure Storage (SSE)](../common/storage-service-encryption.md) ayuda a proteger y salvaguardar los datos. SSE cifra los datos en reposo y así controla el cifrado, el descifrado y la administración de claves. A todos los datos se les aplica el [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uno de los cifrados de bloques más seguros disponibles.

SSE cifra automáticamente los datos de todos los niveles de rendimiento (Estándar y Premium), todos los modelos de implementación (Azure Resource Manager y clásico) y todos los servicios de Azure Storage (Blob, Queue, Table y File). 

## <a name="enable-https-only"></a>Habilitación de solo HTTPS

Para asegurarse de que las solicitudes de datos a y desde una cuenta de almacenamiento están protegidas, puede limitar las solicitudes a solo HTTPS. Actualice el protocolo necesario de la cuenta de almacenamiento mediante el comando [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Pruebe la conexión con `curl` mediante el protocolo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Ahora que se necesita una transferencia segura, recibe el mensaje siguiente:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Pasos siguientes

En la tercera parte de la serie, ha aprendido a proteger el acceso a la cuenta de almacenamiento, por ejemplo a:

> [!div class="checklist"]
> * Usar tokens de SAS para acceder a imágenes en miniatura
> * Activar el cifrado de servidor
> * Habilitar el transporte solo HTTPS

Vaya a la cuarta parte de la serie para aprender a supervisar una aplicación de almacenamiento en la nube y a solucionar los problemas que plantee.

> [!div class="nextstepaction"]
> [Supervisar aplicaciones de almacenamiento en la nube y solucionar problemas](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
