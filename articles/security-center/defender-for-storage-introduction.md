---
title: 'Azure Defender para Storage: ventajas y características'
description: Obtenga información sobre las ventajas y características de Azure Defender para Storage.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516523"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introducción a Azure Defender para Storage


**Azure Defender para Storage** es una capa de inteligencia de seguridad nativa de Azure que detecta intentos poco habituales y potencialmente peligrosos de acceder a las cuentas de almacenamiento o vulnerarlas. Utiliza las funcionalidades avanzadas de inteligencia artificial de seguridad y [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) para ofrecer recomendaciones y alertas de seguridad contextuales.

Las alertas de seguridad se desencadenan cuando se producen anomalías en una actividad. Estas alertas se integran en Azure Security Center y también se envían por correo electrónico a los administradores de las suscripciones, con detalles de actividad sospechosa y recomendaciones sobre cómo investigar y solucionar las amenazas.


## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponible con carácter general|
|Precios:|**Azure Defender para Storage** se factura como se muestra en la [página de precios](security-center-pricing.md).|
|Tipos de almacenamiento protegido:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Archivos de Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Nubes:|![Sí](./media/icons/yes-icon.png) Nubes comerciales<br>![Sí](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, otros gobiernos|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>¿Qué tipos de alertas ofrece Azure Defender para Storage?

Las alertas de seguridad se desencadenan en los escenarios siguientes:

- **Patrones de acceso sospechosos**: como el acceso correcto desde un nodo de salida de Tor o desde una dirección IP considerada sospechosa por Microsoft Threat Intelligence.
- **Actividades sospechosas**: como la extracción de datos anómala o el cambio inusual de permisos de acceso.
- **Carga de contenido malintencionado**: como archivos de malware potenciales (basados en el análisis de reputación de hash) u hospedaje de contenido de suplantación de identidad (phishing).

Las alertas incluyen detalles acerca del incidente que las desencadenó, así como recomendaciones acerca de cómo investigar y corregir las amenazas. Las alertas se pueden exportar no solo a Azure Sentinel, sino también a cualquier otra SIEM de terceros o cualquier otra herramienta externa.

> [!TIP]
> Se recomienda [configurar Azure Defender para Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) en el nivel de suscripción, pero también se puede [configurar en cuentas de almacenamiento individuales](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>¿Qué es el análisis de reputación de hash para malware?

Para determinar si un archivo cargado es sospechoso, Azure Defender para Storage usa el análisis de reputación de hash compatible con la [inteligencia sobre amenazas de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). Las herramientas de protección contra amenazas no examinan los archivos cargados, sino que examinan los registros de almacenamiento y comparan los valores hash de los archivos recién cargados con los de virus conocidos, troyanos, spyware y ransomware. 

Cuando se sospecha que un archivo contiene malware, Security Center muestra una alerta y, opcionalmente, puede enviar un correo electrónico al propietario del almacenamiento para que elimine el archivo sospechoso. Para configurar esta eliminación automática de archivos que el análisis de reputación de hash indica que contienen malware, implemente una [automatización del flujo de trabajo para desencadenar alertas que contengan el "malware potencial cargado en una cuenta de almacenamiento"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Para habilitar las funcionalidades de protección contra amenazas de Security Center, debe habilitar Azure Defender en la suscripción que contiene las cargas de trabajo aplicables.
>
> Puede habilitar **Azure Defender para Storage** en el nivel de suscripción o de recursos.



## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Azure Defender para Storage.

Para obtener material relacionado, consulte los siguientes artículos: 

- Tanto si Security Center genera una alerta, como si la recibe de un producto de seguridad diferente, puede exportarla. Para exportar las alertas a Azure Sentinel, a cualquier SIEM de terceros o a cualquier otra herramienta externa, siga las instrucciones de [Exportación de alertas a un SIEM](continuous-export.md).
- [Habilitar la protección avanzada de Azure Defender para Storage](../storage/common/azure-defender-storage-configure.md)
- [Lista de alertas de Azure Defender para Storage](alerts-reference.md#alerts-azurestorage)
- [Funcionalidades de inteligencia sobre amenazas de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)
