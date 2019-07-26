---
title: 'Azure Backup: Creación de directivas de copia de seguridad mediante la API REST'
description: Administración de directivas de copia de seguridad (programación y retención) mediante la API REST
author: pvrk
manager: shivamg
keywords: API REST; copia de seguridad de Azure VM; restauración de Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: f0729a49c3dc72a28431d711e6783abda96d2ce3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466814"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Creación de directivas de copia de seguridad de Azure Recovery Services mediante API REST

Los pasos para crear una directiva de copia de seguridad para un almacén de Azure Recovery Services se describen en el [documento de API REST de directiva](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Vamos a usar este documento como referencia para crear una directiva para la copia de seguridad de Azure VM.

## <a name="backup-policy-essentials"></a>Fundamentos de la directiva de copia de seguridad

- Se crea una directiva de copia de seguridad por almacén.
- Se puede crear una directiva de copia de seguridad para la copia de seguridad de las cargas de trabajo siguientes:
  - MV de Azure
  - SQL en Azure VM
  - Recurso compartido de archivos de Azure
- Una directiva puede asignarse a muchos recursos. Una directiva de copia de seguridad de Azure VM puede usarse para proteger varias máquinas virtuales de Azure.
- Una directiva consta de dos componentes:
  - Programación: cuándo realizar la copia de seguridad
  - Retención: cuánto tiempo debe retenerse cada copia de seguridad.
- La programación puede definirse como "diaria" o "semanal" con un punto específico en el tiempo.
- La retención puede definirse para los puntos de copia de seguridad "diarios", "semanal", "mensual" o "anual" .
- "Semanal" se refiere a una copia de seguridad en un determinado día de la semana, "mensual" significa una copia de seguridad en un determinado día del mes y "anual" a una copia de seguridad en un determinado día del año.
- La retención de los puntos de copia de seguridad "anuales", y "mensuales" se conoce como "LongTermRetention".
- Cuando se crea un almacén, también se crea una directiva para las copias de seguridad de máquinas virtuales de Azure denominada "DefaultPolicy" que se puede usar para dichas copias de seguridad .

Para crear o actualizar una directiva de Azure Backup, use la siguiente operación *PUT*:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

Los valores de `{policyName}` y `{vaultName}` se proporcionan en el URI. Se proporciona información adicional en el cuerpo de la solicitud.

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Por ejemplo, para crear una directiva para la copia de seguridad de las máquinas virtuales de Azure, los siguientes son los componentes del cuerpo de la solicitud.

|NOMBRE  |Obligatorio  |type  |DESCRIPCIÓN  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Propiedades de ProtectionPolicyResource        |
|etiquetas     |         | Object        |  Etiquetas del recurso       |

Para obtener una lista completa de las definiciones en el cuerpo de la solicitud, consulte el [documento de la directiva de copia de seguridad de API REST](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

El cuerpo de la solicitud siguiente define una directiva de copia de seguridad para las copias de seguridad de máquinas virtuales de Azure.

La directiva indica:

- Realizar una copia de seguridad semanal todos los lunes, miércoles y jueves a las 10:00 a.m. hora estándar del Pacífico.
- Conservar las copias de seguridad realizadas los lunes, miércoles o jueves durante una semana.
- Conservar las copias de seguridad realizadas el primer miércoles y el tercer jueves del mes durante dos meses (invalida las condiciones de retención anteriores, si hubieran).
- Conservar las copias de seguridad realizadas el cuarto lunes y el cuarto jueves de los meses de febrero y de noviembre durante cuatro años (invalida las condiciones de retención anteriores, si hubieran).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Los formatos de fecha y hora para la programación y la retención admiten solo DateTime. No admiten el formato Time solo.

## <a name="responses"></a>Respuestas

La creación o actualización de las directivas de copia de seguridad es una [operación asincrónica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa que esta operación crea otra que tiene que ser seguida por separado.

Devuelve las dos respuestas: 202 (Accepted) (aceptado) cuando se crea otra operación y, a continuación, 200 (OK) cuando se completa dicha operación.

|NOMBRE  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|200 OK     |    [Protection PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 - Aceptado     |         |     Accepted    |

### <a name="example-responses"></a>Respuestas de ejemplo

Una vez enviada la solicitud *PUT* para la creación o actualización de directivas, la respuesta inicial es 202 (Accepted) (aceptado) con un encabezado de ubicación o Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

A continuación, realice un seguimiento de la operación resultante con el encabezado de ubicación o el encabezado Azure-AsyncOperation y un simple comando *GET*.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

Una vez completada la operación, devuelve 200 (OK) con el contenido de la directiva en el cuerpo de respuesta.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Si una directiva ya se usa para proteger un elemento, su actualización dará como resultado [modificar la protección](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) para todos estos elementos asociados.

## <a name="next-steps"></a>Pasos siguientes

[Habilitación de la protección de una máquina virtual de Azure sin protección](backup-azure-arm-userestapi-backupazurevms.md).

Para más información sobre las API REST de Azure Backup, consulte los siguientes documentos:

- [API REST del proveedor de Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)