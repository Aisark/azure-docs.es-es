---
title: Habilitación del acceso de lectura público para los contenedores y blobs en Azure Blob Storage | Microsoft Docs
description: Obtenga información acerca de cómo permitir el acceso anónimo a contenedores y blobs y cómo tener acceso a ellos mediante programación.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 3996f22db2f5dc597939995a2699c4fe228821e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392574"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Administración del acceso de lectura anónimo a contenedores y blobs
Puede habilitar el acceso de lectura anónimo y público a un contenedor y sus blobs en Azure Blob Storage. Al hacerlo, puede conceder acceso de solo lectura a estos recursos sin compartir la clave de cuenta y sin necesidad de una firma de acceso compartido (SAS).

El acceso de lectura público es mejor para escenarios donde desea que ciertos blobs estén siempre disponibles para el acceso de lectura anónimo. Para un control más minucioso, puede crear una firma de acceso compartido. Las firmas de acceso compartido le permiten proporcionar acceso restringido con distintos permisos para un período específico. Para obtener más información sobre la creación de firmas de acceso compartido, vea [Uso de firmas de acceso compartido (SAS) en Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concesión de permisos a usuarios anónimos a contenedores y blobs
De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a un contenedor y a todos los blobs en su interior. Para dar a los usuarios anónimos permisos de lectura para un contenedor y sus blobs, puede establecer los permisos del contenedor de forma que se permita el acceso público. Los usuarios anónimos pueden leer los blobs que estén en un contenedor con acceso público sin necesidad de tener que autenticar la solicitud.

Puede configurar un contenedor con los permisos siguientes:

* **No hay acceso de lectura público:** El contenedor y sus blobs pueden tener acceso a solo el propietario de la cuenta de almacenamiento. Este es el valor predeterminado para todos los contenedores nuevos.
* **Acceso de lectura público solo para blobs:** Los blobs del contenedor pueden leerse mediante una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes anónimos no pueden enumerar los blobs dentro del contenedor.
* **Acceso de lectura público completo:** Todos los datos de blob y contenedor pueden leerse mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

Puede usar lo siguiente para establecer permisos de contenedor:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [CLI de Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Mediante programación, con una de las bibliotecas de cliente de almacenamiento o la API de REST.

### <a name="set-container-permissions-in-the-azure-portal"></a>Establecimiento de permisos de contenedor en Azure Portal
Para configurar los permisos del contenedor en [Azure Portal](https://portal.azure.com), siga estos pasos:

1. Abra la hoja **Cuenta de almacenamiento** en el portal. Puede encontrar la cuenta de almacenamiento seleccionando **Cuentas de almacenamiento** en la hoja del menú del portal principal.
1. En **SERVICIO BLOB** en la hoja de menú, seleccione **Blobs**.
1. Haga doble clic en la fila del contenedor o seleccione los tres puntos para abrir el **menú contextual** del contenedor.
1. Seleccione **Directiva de acceso** en el menú contextual.
1. Seleccione un **tipo de acceso** en el menú desplegable.

    ![Cuadro de diálogo Editar metadatos del contenedor](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Establecimiento de los permisos del contenedor con .NET
Para configurar permisos para un contenedor con C# y la Biblioteca del cliente de almacenamiento para .NET, primero recupere los permisos existentes del contenedor llamando al método **GetPermissions**. A continuación, establezca la propiedad **PublicAccess** para el objeto **BlobContainerPermissions** devuelto por el método **GetPermissions**. Por último, llame al método **SetPermissions** con los permisos actualizados.

El ejemplo siguiente establece los permisos del contenedor para el acceso de lectura público completo. Para establecer permisos de acceso de lectura público solo para blobs, establezca la propiedad **PublicAccess** en **BlobContainerPublicAccessType.Blob**. Para quitar todos los permisos para los usuarios anónimos, establezca la propiedad en **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Acceso anónimo a contenedores y blobs
Un cliente que tiene acceso a contenedores y blobs de forma anónima puede utilizar constructores que no requieren credenciales. En los ejemplos siguientes se muestran varias maneras diferentes de hacer referencia a recursos de Blob service de forma anónima.

### <a name="create-an-anonymous-client-object"></a>Creación de un objeto de cliente anónimo
Puede crear un nuevo objeto de cliente de servicio para el acceso anónimo proporcionando el punto de conexión de Blob service para la cuenta. Sin embargo, también debe conocer el nombre de un contenedor en esa cuenta que esté disponible para el acceso anónimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Referencia a un contenedor de forma anónima
Si tiene la dirección URL de un contenedor que está disponible de forma anónima, puede utilizarla para hacer referencia al contenedor directamente.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Referencia a un blob de forma anónima
Si tiene la dirección URL a un blob que está disponible para el acceso anónimo, puede hacer referencia al blob directamente con esa dirección URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Características disponibles para los usuarios anónimos
En la siguiente tabla, se indican las operaciones a las que pueden llamar los usuarios anónimos cuando la ACL de un contenedor se establece para permitir un acceso público.

| Operación REST | Permiso con acceso de lectura público completo | Permiso con acceso de lectura público para solo para los blobs |
| --- | --- | --- |
| List Containers |Solo el propietario |Solo el propietario |
| Create Container |Solo el propietario |Solo el propietario |
| Get Container Properties |Todo |Solo el propietario |
| Get Container Metadata |Todo |Solo el propietario |
| Set Container Metadata |Solo el propietario |Solo el propietario |
| Get Container ACL |Solo el propietario |Solo el propietario |
| Set Container ACL |Solo el propietario |Solo el propietario |
| Delete Container |Solo el propietario |Solo el propietario |
| List Blobs |Todo |Solo el propietario |
| Put Blob |Solo el propietario |Solo el propietario |
| Get Blob |Todo |Todo |
| Get Blob Properties |Todo |Todo |
| Set Blob Properties |Solo el propietario |Solo el propietario |
| Get Blob Metadata |Todo |Todo |
| Set Blob Metadata |Solo el propietario |Solo el propietario |
| Put Block |Solo el propietario |Solo el propietario |
| Get Block List (solo bloques confirmados) |Todo |Todo |
| Get Block List (solo bloques sin confirmar o todos los bloques) |Solo el propietario |Solo el propietario |
| Put Block List |Solo el propietario |Solo el propietario |
| Delete Blob |Solo el propietario |Solo el propietario |
| Copia de blobs |Solo el propietario |Solo el propietario |
| Instantánea de blob |Solo el propietario |Solo el propietario |
| Lease Blob |Solo el propietario |Solo el propietario |
| Put Page |Solo el propietario |Solo el propietario |
| Get Page Ranges |Todo |Todo |
| Append Blob |Solo el propietario |Solo el propietario |

## <a name="next-steps"></a>Pasos siguientes

* [Autenticación para los servicios de Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Uso de Firmas de acceso compartido (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegación de acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx)
