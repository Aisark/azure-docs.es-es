---
title: Copia de datos de Google AdWords con Azure Data Factory (versión preliminar) | Microsoft Docs
description: Obtenga información sobre cómo copiar datos de Google AdWords en almacenes de datos receptores compatibles a través de una actividad de copia en una canalización de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 615a2dedd8a4698bab5eaa237184266e5bef1a1b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720791"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Copia de datos de Google AdWords con Azure Data Factory (versión preliminar)

En este artículo se describe el uso de la actividad de copia de Azure Data Factory para copiar datos de Google AdWords. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!IMPORTANT]
> Este conector está actualmente en versión preliminar. Puede probarlo y enviarnos sus comentarios. Si desea depender de los conectores de versión preliminar en la solución, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Google AdWords en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas para el conector Google AdWords.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las propiedades siguientes son compatibles con el servicio vinculado de Google AdWords:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **GoogleAdWords** | Sí |
| clientCustomerID | El identificador de cliente del cliente de la cuenta de AdWords para la que quiere capturar datos de informes.  | Sí |
| developerToken | El token de desarrollador asociado con la cuenta de administrador que usó para conceder acceso a la API AdWords.  Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia de ADF incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sí |
| authenticationType | Mecanismo de autenticación OAuth 2.0 que se usa para autenticar. ServiceAuthentication solo puede utilizarse en IR autohospedados. <br/>Los valores permitidos son: **ServiceAuthentication** y **UserAuthentication** | Sí |
| refreshToken | Token de actualización obtenido de Google para autorizar el acceso a AdWords para UserAuthentication. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia de ADF incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sin |
| clientId | El identificador de cliente de la aplicación de Google que se ha usado para adquirir el token de actualización. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia de ADF incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sin |
| clientSecret | El secreto de cliente de la aplicación de Google que se ha usado para adquirir el token de actualización. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia de ADF incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sin |
| email | Id. de correo electrónico de la cuenta de servicio usado para ServiceAuthentication y que solo puede utilizarse en IR autohospedados.  | Sin |
| keyFilePath | Ruta de acceso completa al archivo de clave. p12 que se usa para autenticar la dirección de correo electrónico de la cuenta de servicio y que solo puede utilizarse en IR autohospedados.  | Sin |
| trustedCertPath | Ruta de acceso completa del archivo .pem que contiene certificados de CA de confianza para comprobar el servidor al conectarse a través de SSL. Esta propiedad solo puede establecerse al utilizar SSL en IR autohospedados. El valor predeterminado es el archivo cacerts.pem instalado con el IR.  | Sin |
| useSystemTrustStore | Especifica si se utiliza un certificado de CA del almacén de confianza del sistema o de un archivo PEM especificado. El valor predeterminado es false.  | Sin |

**Ejemplo:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Google AdWords.

Para copiar datos de Google AdWords, establezca la propiedad type del conjunto de datos en **GoogleAdWordsObject**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **GoogleAdWordsObject** | Sí |
| tableName | Nombre de la tabla. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el origen de Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords como origen

Para copiar datos de Google AdWords, establezca el tipo de origen de la actividad de copia en **GoogleAdWordsSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **GoogleAdWordsObject** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
