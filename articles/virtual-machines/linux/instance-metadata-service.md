---
title: Servicio de metadatos de instancia de Azure
description: Interfaz RESTful para obtener información sobre eventos de proceso, red y mantenimiento próximos de máquinas virtuales Linux.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 0e04f7e190ef22fb5c2b288e478cac5ffaf89141
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962516"
---
# <a name="azure-instance-metadata-service"></a>Servicio de metadatos de instancia de Azure

Instance Metadata Service (IMDS) de Azure proporciona información sobre instancias de máquina virtual actualmente en ejecución y se puede usar para administrar y configurar las máquinas virtuales.
La información proporcionada incluye los eventos de SKU, configuración de red y mantenimiento próximos. Para una lista completa de los datos que están disponibles, consulte [API de metadatos](#metadata-apis).

El servicio de metadatos de instancia de Azure es un punto de conexión REST al que pueden tener acceso todas las máquinas virtuales IaaS creadas a través de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
El punto de conexión está disponible en una dirección IP no enrutable conocida (`169.254.169.254`) a la que solo se puede tener acceso desde dentro de la máquina virtual.

> [!IMPORTANT]
> Este servicio está **disponible con carácter general** en todas las regiones de Azure.  Recibe actualizaciones periódicas para exponer información nueva sobre instancias de máquina virtual. En esta página se reflejan las [API de metadatos](#metadata-apis) actualizadas que hay disponibles.

## <a name="service-availability"></a>Disponibilidad del servicio

El servicio está disponible con carácter general en las regiones de Azure. Puede que no todas las versiones de API estén disponibles en todas las regiones de Azure.

Regions                                        | ¿Disponibilidad?                                 | Versiones admitidas
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas las regiones globales de Azure disponibles con carácter general](https://azure.microsoft.com/regions/)     | Disponibilidad general | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Disponibilidad general | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure China 21Vianet](https://www.azure.cn/)                                            | Disponibilidad general | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)                    | Disponibilidad general | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01

Esta tabla cambia cuando hay actualizaciones del servicio o cuando hay nuevas versiones compatibles disponibles.

Para probar el servicio de metadatos de instancia, cree una máquina virtual desde [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) o [Azure Portal](https://portal.azure.com) en las regiones anteriores, y siga los ejemplos siguientes.
Puede encontrar más ejemplos de cómo consultar IMDS en [Ejemplos de Azure Instance Metadata Service](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Uso

### <a name="versioning"></a>Control de versiones

Instance Metadata Service tiene versiones y es obligatorio especificar la versión de la API en la solicitud HTTP.

Puede ver las versiones más recientes enumeradas en esta [tabla de disponibilidad](#service-availability).

A medida que se agreguen versiones más recientes, todavía se podrá acceder a las versiones anteriores por motivos de compatibilidad si los scripts tienen dependencias en formatos de datos específicos.

Cuando no se especifica ninguna versión, se devuelve un error con una lista de las versiones más recientes que son compatibles.

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Respuesta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Uso de encabezados

Al consultar el servicio de metadatos de instancia, debe proporcionar el encabezado `Metadata: true` para asegurarse de que la solicitud no se ha redirigido de manera involuntaria.

### <a name="retrieving-metadata"></a>Recuperar metadatos

Los metadatos de instancia están disponibles para ejecutar máquinas virtuales creadas o administradas mediante [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Para tener acceso a todas las categorías de datos de una instancia de máquina virtual, use la solicitud siguiente:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Todas las consultas de metadatos de instancia distinguen mayúsculas de minúsculas.

### <a name="data-output"></a>Salida de datos

De forma predeterminada, el servicio de metadatos de instancia devuelve datos en formato JSON (`Content-Type: application/json`). Pero las distintas API devuelven los datos en formatos diferentes si se solicita.
En la tabla siguiente se muestra una referencia de otros formatos de datos que las API pueden admitir.

API | Formato predeterminado de datos | Otros formatos
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | None
/attested | json | None

Para acceder a un formato de respuesta que no sea el predeterminado, especifique el formato solicitado como un parámetro de cadena de consulta en la solicitud. Por ejemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para los nodos hoja, `format=json` no funciona. Para estas consultas, `format=text` debe especificarse explícitamente si el formato predeterminado es JSON.

### <a name="security"></a>Seguridad

El punto de conexión del servicio de metadatos de instancia solo es accesible desde la instancia de máquina virtual en ejecución en una dirección IP no enrutable. Además, el servicio rechaza cualquier solicitud que tenga un encabezado `X-Forwarded-For`.
Las solicitudes tienen que incluir también un encabezado `Metadata: true` para garantizar que la solicitud actual esté dirigida directamente y no como parte de un redireccionamiento accidental.

### <a name="error"></a>Error

Si no se encuentra un elemento de datos o hay una solicitud con formato incorrecto, el servicio de metadatos de instancia devuelve errores HTTP estándar. Por ejemplo:

Código de estado HTTP | Motivo
----------------|-------
200 OK |
400 - Solicitud incorrecta | Falta el encabezado `Metadata: true` o el formato al consultar un nodo hoja
404 No encontrado | El elemento solicitado no existe
405 Método no permitido | Solo se admiten solicitudes `GET`
429 Demasiadas solicitudes | La API es compatible actualmente con un máximo de cinco consultas por segundo
500 Error de servicio     | Vuelva a intentarlo más tarde

### <a name="examples"></a>Ejemplos

> [!NOTE]
> Todas las respuestas de las API son cadenas JSON. Todas las respuestas de ejemplo siguientes se han imprimido correctamente para mejorar la legibilidad.

#### <a name="retrieving-network-information"></a>Recuperación de información de red

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Recuperación de dirección IP pública

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperación de todos los metadatos para una instancia

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperación de metadatos en una máquina virtual con Windows

**Solicitud**

Los metadatos de instancia se pueden recuperar en Windows a través de la utilidad `curl` de PowerShell: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

O bien a través del cmdlet `Invoke-RestMethod`:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get 
```

**Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>API de metadatos

Las siguientes API están disponibles a través del punto de conexión de metadatos:

data | Descripción | Versión de introducción
-----|-------------|-----------------------
attested | Consulte [Datos atestiguados](#attested-data) | 2018-10-01
identity | Identidades administradas de recursos de Azure. Consulte [Obtener un token de acceso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Vea [API de instancia](#instance-api). | 2017-04-02
scheduledevents | Consulte [Scheduled Events](scheduled-events.md). | 2017-08-01

#### <a name="instance-api"></a>API de instancia

Las siguientes categorías de procesos están disponibles a través de la API de instancia:

> [!NOTE]
> Mediante el punto de conexión de metadatos, se accede a las siguientes categorías a través de la instancia y los procesos:

data | Descripción | Versión de introducción
-----|-------------|-----------------------
azEnvironment | Entorno de Azure donde se está ejecutando la máquina virtual | 2018-10-01
customData | En la actualidad esta característica está deshabilitada y actualizaremos esta documentación cuando esté disponible | 01-02-2019
ubicación | La región de Azure donde se ejecuta la máquina virtual | 2017-04-02
name | Nombre de la máquina virtual | 2017-04-02
offer | Ofrece información de la imagen de la máquina virtual y solo está presente para imágenes implementadas desde la galería de imágenes de Azure. | 2017-04-02
osType | Linux o Windows | 2017-04-02
placementGroupId | [Grupo de selección de ubicación](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de su conjunto de escalado de máquina virtual | 2017-08-01
plan | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) que contiene el nombre, el producto y el editor de una máquina virtual si es una imagen de Azure Marketplace | 2018-04-02
platformUpdateDomain |  El [dominio de actualización](manage-availability.md) en que se ejecuta la máquina virtual. | 2017-04-02
platformFaultDomain | El [dominio de error](manage-availability.md) en que se ejecuta la máquina virtual. | 2017-04-02
provider | Proveedor de la máquina virtual | 2018-10-01
publicKeys | [Colección de claves públicas](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) asignada a la máquina virtual y rutas de acceso | 2018-04-02
publisher | Publicador de la imagen de VM | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para su máquina virtual | 2017-08-01
resourceId | El identificador [completo](https://docs.microsoft.com/rest/api/resources/resources/getbyid) del recurso | 2019-03-11
sku | SKU específica de la imagen de VM | 2017-04-02
storageProfile | Consulte [Perfil de almacenamiento](#storage-profile) | 2019-06-01
subscriptionId | Suscripción de Azure para la máquina virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/management/tag-resources.md) para su máquina virtual  | 2017-08-01
tagsList | Etiquetas con formato de matriz de JSON para facilitar el análisis mediante programación  | 2019-06-04
version | Versión de la imagen de máquina virtual | 2017-04-02
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para la máquina virtual. | 2017-04-02
vmScaleSetName | [Nombre del conjunto de escalado de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) del conjunto de escalado de la máquina virtual | 2017-12-01
vmSize | [Tamaño de VM](sizes.md) | 2017-04-02
zona | [Zona de disponibilidad](../../availability-zones/az-overview.md) de la máquina virtual | 2017-12-01

Las siguientes categorías de redes están disponibles a través de la API de instancia:

> [!NOTE]
> Mediante el punto de conexión de metadatos, se accede a las siguientes categorías a través de la instancia, la red o la interfaz.

data | Descripción | Versión de introducción
-----|-------------|-----------------------
ipv4/privateIpAddress | Dirección IPv4 local de la máquina virtual | 2017-04-02
ipv4/publicIpAddress | Dirección IPv4 pública de la máquina virtual | 2017-04-02
subnet/address | Dirección de subred de la máquina virtual | 2017-04-02
subnet/prefix | Prefijo de la subred, ejemplo, 24 | 2017-04-02
ipv6/ipAddress | Dirección IPv6 local de la máquina virtual | 2017-04-02
macAddress | Dirección de MAC de la VM | 2017-04-02

## <a name="attested-data"></a>Datos atestiguados

Parte del escenario que sirve Instance Metadata Service está destinado a garantizar que los datos proporcionados provienen de Azure. Firmamos una parte de esta información para que las imágenes de Marketplace puedan tener la seguridad de que su imagen se ejecuta en Azure.

### <a name="example-attested-data"></a>Datos atestiguados de ejemplo

> [!NOTE]
> Todas las respuestas de las API son cadenas JSON. Las siguientes respuestas de ejemplo se han impreso correctamente para facilitar su lectura.

 **Solicitud**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-version es un campo obligatorio. Consulte la [sección de disponibilidad del servicio](#service-availability) para ver las versiones de API admitidas.
Nonce es una cadena opcional de 10 dígitos. Si no se proporciona, IMDS devuelve en su lugar la marca de tiempo UTC actual. Debido al mecanismo de almacenamiento en caché de IMDS, es posible que se devuelva un valor de nonce previamente almacenado en caché.

 **Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

El blob de firma es una versión con la firma [pkcs7](https://aka.ms/pkcs7) del documento. Contiene el certificado que se usa para la firma junto con detalles de la máquina virtual como vmld, sku, nonce, subscriptionId y timeStamp para la creación y expiración del documento, y la información del plan sobre la imagen. La información del plan solo se rellena para las imágenes de Azure Marketplace. El certificado se puede extraer de la respuesta y usarse para validar que la respuesta es válida y viene de Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Recuperación de metadatos atestiguados en una máquina virtual Windows

 **Solicitud**

Los metadatos de instancia se pueden recuperar en Windows a través de la utilidad `curl` de PowerShell:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 O bien a través del cmdlet `Invoke-RestMethod`:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Api-version es un campo obligatorio. Consulte la sección de disponibilidad del servicio para ver las versiones de API admitidas.
Nonce es una cadena opcional de 10 dígitos. Si no se proporciona, IMDS devuelve en su lugar la marca de tiempo UTC actual. Debido al mecanismo de almacenamiento en caché de IMDS, es posible que se devuelva un valor de nonce previamente almacenado en caché.

 **Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

El blob de firma es una versión con la firma [pkcs7](https://aka.ms/pkcs7) del documento. Contiene el certificado que se usa para la firma junto con detalles de la máquina virtual como vmld, sku, nonce, subscriptionId y timeStamp para la creación y expiración del documento, y la información del plan sobre la imagen. La información del plan solo se rellena para las imágenes de Azure Marketplace. El certificado se puede extraer de la respuesta y usarse para validar que la respuesta es válida y viene de Azure.


## <a name="example-scenarios-for-usage"></a>Escenarios de ejemplo de uso  

### <a name="tracking-vm-running-on-azure"></a>Seguimiento de una máquina virtual que se ejecuta en Azure

Como proveedor de servicios, es posible que necesite hacer seguimiento de la cantidad de máquinas virtuales que ejecutan su software o que tenga agentes que deban hacer seguimiento de la unicidad de la máquina virtual. Para poder obtener un identificador único para una máquina virtual, use el campo `vmId` del servicio de metadatos de instancia.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Respuesta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Ubicación de los contenedores y el dominio de error/actualización basado en particiones de datos

Para ciertos escenarios, la ubicación de las distintas réplicas de datos es de máxima importancia. Por ejemplo, para [la ubicación de réplicas de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o la ubicación de contenedores a través de un [orquestador](https://kubernetes.io/docs/user-guide/node-selection/) se debe saber en qué `platformFaultDomain` y `platformUpdateDomain` se ejecuta la máquina virtual.
También puede usar las [zonas de disponibilidad](../../availability-zones/az-overview.md) para las instancias para tomar estas decisiones.
Puede consultar directamente estos datos a través del servicio de metadatos de instancia.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Respuesta**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtención de más información sobre la VM durante el caso de soporte técnico

Como proveedor de servicios, es posible que reciba una llamada de soporte técnico en la que le gustaría tener más información sobre la máquina virtual. Pedirle al cliente que comparta los metadatos del equipo puede proporcionar información básica para que el profesional de soporte técnico conozca la variante de máquina virtual en Azure.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Obtención del entorno de Azure donde se está ejecutando la máquina virtual

Azure tiene varias nubes soberanas, como [Azure Government](https://azure.microsoft.com/overview/clouds/government/). En ocasiones, necesitará el entorno de Azure para tomar algunas decisiones acerca del tiempo de ejecución. En el siguiente ejemplo se muestra cómo lograr este comportamiento.

**Solicitud**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Respuesta**
```bash
AzurePublicCloud
```
Aquí se muestran las nubes y los valores del entorno de Azure.

 Nube   | Entorno de Azure
---------|-----------------
[Todas las regiones globales de Azure disponibles con carácter general](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Obtención de las etiquetas para la máquina virtual

Es posible que se hayan aplicado etiquetas a las máquinas virtuales de Azure para organizarlas de forma lógica en una taxonomía. Las etiquetas asignadas a una máquina virtual se pueden recuperar mediante esta solicitud.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Respuesta**

```text
Department:IT;Environment:Test;Role:WebRole
```

El campo `tags` es una cadena con las etiquetas delimitadas por puntos y coma. Esto puede ser un problema si se usan puntos y coma en las propias etiquetas. Si se escribe un analizador para extraer las etiquetas mediante programación, debe basarse en el campo `tagsList`, que es una matriz de JSON sin delimitadores y, por tanto, más fácil de analizar.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Respuesta**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Validación de que la máquina virtual se ejecuta en Azure

Los proveedores de Marketplace desean asegurarse de que su software tiene licencia para ejecutarse solo en Azure. Si alguien copia el disco duro virtual en un entorno local, debería disponer de la capacidad para detectarlo. Mediante una llamada al servicio de metadatos de instancia, los proveedores pueden obtener datos firmados que garantizan la respuesta únicamente de Azure.

> [!NOTE]
> Requiere jq para instalarse.

**Solicitud**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Respuesta**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

data | Descripción
-----|------------
valor de seguridad | El usuario especificó una cadena opcional con la solicitud. Si no se proporcionó ningún nonce en la solicitud, se devuelve la marca de tiempo UTC actual
plan | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) en una máquina virtual es una imagen de Azure Marketplace que contiene el nombre, producto y editor
timestamp/createdOn | La marca de tiempo UTC en la que se ha creado el primer documento firmado
timestamp/expiresOn | La marca de tiempo UTC en la que expira el documento firmado
vmId |  [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para la máquina virtual.
subscriptionId | Suscripción de Azure para la máquina virtual publicada con fecha `2019-04-30`
sku | SKU específica de la imagen de VM, presentada en `2019-11-01`

#### <a name="verifying-the-signature"></a>Comprobación de la firma

Una vez que obtenga la firma anterior, podrá comprobar que esta es de Microsoft. También podrá comprobar el certificado intermedio y la cadena de certificados. Por último, puede comprobar que el identificador de suscripción es correcto.

> [!NOTE]
> El certificado para la nube pública y la nube soberana serán diferentes.

 Nube | Certificado
---------|-----------------
[Todas las regiones globales de Azure disponibles con carácter general](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | metadata.azure.cn
[Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

En aquellos casos en los que el certificado intermedio no se puede descargar debido a restricciones de red durante la validación, es posible anclarlo. No obstante, Azure sustituirá los certificados según el procedimiento PKI estándar. Los certificados anclados deberán actualizarse cuando se produzca la sustitución. Cada vez que se planee un cambio para actualizar el certificado intermedio, se actualizará el blog de Azure y se notificará a los clientes de Azure. Encontrará los certificados intermedios [aquí](https://www.microsoft.com/pki/mscorp/cps/default.htm). Los certificados intermedios para cada una de las regiones pueden ser diferentes.

### <a name="failover-clustering-in-windows-server"></a>Clústeres de conmutación por error de Windows Server

Para determinados escenarios, al consultar Instance Metadata Service con los clústeres de conmutación por error, es necesario agregar una ruta a la tabla de enrutamiento.

1. Abra un símbolo del sistema con privilegios de administrador.

2. Ejecute el siguiente comando y anote la dirección de la interfaz de red de destino (`0.0.0.0`) en la tabla de enrutamiento IPv4.

```bat
route print
```

> [!NOTE] 
> La siguiente salida de ejemplo de una máquina virtual de Windows Server con un clúster de conmutación por error habilitado contiene solo la tabla de rutas IPv4 por motivos de simplicidad.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Ejecute el siguiente comando y use la dirección de la interfaz de red de destino (`0.0.0.0`) que es (`10.0.1.10`) en el ejemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Perfil de almacenamiento

Instance Metadata Service puede proporcionar detalles sobre los discos de almacenamiento asociados a la máquina virtual. Estos datos se pueden encontrar en el punto de conexión instance/compute/storageProfile.

El perfil de almacenamiento de una máquina virtual se divide en tres categorías: referencia de imagen, disco del sistema operativo y discos de datos.

El objeto de referencia de imagen contiene la información siguiente sobre la imagen del sistema operativo:

data    | Descripción
--------|-----------------
id      | Id. de recurso
offer   | Oferta de la plataforma o imagen de Marketplace
publisher | Editor de la imagen
sku     | SKU de la imagen
version | Versión de la plataforma o imagen de Marketplace

El objeto de disco del sistema operativo contiene la información siguiente sobre el disco del sistema operativo que usa la máquina virtual:

data    | Descripción
--------|-----------------
caching | Requisitos de almacenamiento en caché
createOption | Información sobre cómo se ha creado la máquina virtual
diffDiskSettings | Configuración de discos efímeros
diskSizeGB | Tamaño del disco en GB
imagen   | Disco duro virtual de la imagen de usuario de origen
lun     | Número de unidad lógica del disco
managedDisk | Parámetros de disco administrado
name    | Nombre del disco
vhd     | Disco duro virtual
writeAcceleratorEnabled | Si writeAccelerator está habilitado o no en el disco

La matriz de discos de datos contiene una lista de discos de datos conectados a la máquina virtual. Cada objeto de disco de datos contiene la información siguiente:

data    | Descripción
--------|-----------------
caching | Requisitos de almacenamiento en caché
createOption | Información sobre cómo se ha creado la máquina virtual
diffDiskSettings | Configuración de discos efímeros
diskSizeGB | Tamaño del disco en GB
encryptionSettings | Configuración de cifrado para el disco
imagen   | Disco duro virtual de la imagen de usuario de origen
managedDisk | Parámetros de disco administrado
name    | Nombre del disco
osType  | Tipo de sistema operativo incluido en el disco
vhd     | Disco duro virtual
writeAcceleratorEnabled | Si writeAccelerator está habilitado o no en el disco

El siguiente es un ejemplo de cómo consultar la información de almacenamiento de la máquina virtual.

**Solicitud**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Respuesta**

> [!NOTE]
> La respuesta es una cadena JSON. La respuesta de ejemplo siguiente se ha impreso correctamente para mejorar la legibilidad.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Ejemplos de llamadas al servicio de metadatos mediante lenguajes diferentes dentro de la máquina virtual

Idioma | Ejemplo
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Preguntas más frecuentes

1. Obtengo el error `400 Bad Request, Required metadata header not specified`. ¿Qué significa?
   * El servicio de metadatos de instancia requiere que el encabezado `Metadata: true` se transmita en la solicitud. Transmitir este encabezado en la llamada de REST permite tener acceso al servicio de metadatos de instancia.
2. ¿Por qué no recibo información de proceso de mi máquina virtual?
   * Actualmente el servicio de metadatos de instancia solo admite instancias creadas con Azure Resource Manager. En el futuro, es posible que se agregue compatibilidad para máquinas virtuales de servicio en la nube.
3. Creé mi máquina virtual mediante Azure Resource Manager hace un tiempo. ¿Por qué no veo la información de metadatos de proceso?
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../../azure-resource-manager/management/tag-resources.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
4. No veo todos los datos rellenados para la nueva versión
   * En el caso de las máquinas virtuales que se crearon después de septiembre de 2016, agregue una [etiqueta](../../azure-resource-manager/management/tag-resources.md) para empezar a ver los metadatos de proceso. En el caso de máquinas virtuales anteriores (creadas antes de septiembre de 2016), agregue o quite extensiones o discos de datos a la máquina virtual para actualizar los metadatos.
5. ¿Por qué recibo el error `500 Internal Server Error`?
   * Vuelva a intentar la solicitud en función del sistema de interrupción exponencial. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure.
6. ¿Dónde puedo compartir preguntas o comentarios adicionales?
   * Envíe sus comentarios en https://feedback.azure.com.
7. ¿Esto funciona para la instancia del conjunto de escala de máquinas virtuales?
   * Sí, el servicio de metadatos está disponible para las instancias del conjunto de escala.
8. ¿Cómo puedo obtener soporte técnico para el servicio?
   * Para obtener soporte técnico para el servicio, cree un problema de compatibilidad en Azure Portal en la máquina virtual en la que no obtiene respuesta de metadatos después de reintentos prolongados.
9. ¿Por qué se agotó el tiempo de espera de solicitud para mi llamada al servicio?
   * Se deben realizar llamadas de metadatos desde la dirección IP principal asignada a la tarjeta de red principal de la máquina virtual; además, en caso de que haya cambiado las rutas, debe tener una ruta para la dirección 169.254.0.0/16 desde su tarjeta de red.
10. He actualizado mis etiquetas en el conjunto de escalado de máquinas virtuales, pero no aparecen en las instancias, a diferencia de las máquinas virtuales.
    * Actualmente, las etiquetas para ScaleSets solo se muestran a la máquina virtual durante un reinicio, restablecimiento de imagen o cambio de disco en la instancia.

    ![Soporte técnico de metadatos de instancia](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [eventos programados](scheduled-events.md).
