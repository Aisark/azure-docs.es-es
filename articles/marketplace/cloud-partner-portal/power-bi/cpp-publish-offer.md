---
title: Publicación de una oferta de aplicación de Power BI | Azure Marketplace
description: Publique una oferta de aplicación de Power BI en Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: 324be960bd2d22623763ca3e24b99be92ff04174
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826032"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicación de una oferta de aplicación de Power BI

El último paso, después de haber definido la oferta en Cloud Partner Portal y creado los recursos técnicos asociados, consiste en enviar la oferta para la publicación. Para iniciar este proceso, en el panel izquierdo de la ventana **Nueva oferta**, seleccione **Publicar**. Para más información, consulte [Publicación de ofertas de Azure Marketplace y AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Pasos para la publicación

Estos son los pasos principales del proceso de publicación:

![Pasos del proceso de publicación para la oferta de aplicación de Power BI](./media/publishing-process-steps.png)

En esta tabla se describe cada paso y se proporciona su tiempo de finalización estimado:

|   Paso de publicación            |   Hora     |   DESCRIPCIÓN                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar requisitos previos       | 15 minutos     | Se validan la información y la configuración de la oferta.                            |
| Certificación                | 1-7 días   | El equipo de certificación de Power BI analiza su oferta. El equipo ejecuta la aplicación de Power BI a través de una prueba de verificación manual al instalar la aplicación a través de la dirección URL de instalación proporcionada. Las validaciones principales se realizan como parte del proceso de certificación de la aplicación (que se describe más adelante en este documento).         |
| Packaging                    | \< 1 hora  | Los recursos técnicos de la oferta se empaquetan para uso del cliente.                        |
| Registro de la generación de clientes potenciales | \< 1 hora  | Los sistemas de clientes potenciales se configuran e implementan.                                      |
| Aprobación del publicador            | \-         | Completa una revisión final y la confirmación antes del lanzamiento de la oferta. Ahora también tendrá un vínculo para obtener una versión preliminar de la oferta. Una vez que esté satisfecho con el aspecto de la versión preliminar, seleccione **Transmitir** en la pestaña **Estado**. Con esto, se envía una solicitud al equipo de incorporación para que muestre su aplicación en AppSource.    |
| En vivo                         | \< 3 horas | La oferta ya se ha publicado ("en vivo") en AppSource, y los clientes pueden verla e implementarla en sus suscripciones a Power BI. También recibirá un correo electrónico de confirmación. En la columna derecha de la pestaña **Todas las ofertas**, puede ver el estado de todas las ofertas. En la pestaña **Estado**, puede ver el estado detallado del flujo de publicación para su oferta. |
|   |   |

Espere hasta ocho días a que este proceso se complete. Después de completar estos pasos de publicación, la oferta de aplicación de Power BI se mostrará en la sección de aplicaciones de Power BI de [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20).


### <a name="app-certification-process"></a>Proceso de certificación de aplicaciones

El equipo de incorporación de Microsoft usa este proceso para validar el envío de la oferta de aplicaciones de Power BI:

1. Se revisan los vínculos de ayuda y los documentos legales.
2. Se valida la información de contacto del soporte técnico.
3. Se usa la dirección URL del instalador para verificar la correcta instalación.
4. Se analiza la aplicación en busca de malware y otro contenido malintencionado.
5. Se verifica que el contenido mostrado coincida con la descripción de la aplicación.
6. Se verifica que las operaciones relacionadas con la aplicación funcionen según lo previsto en Power BI. El equipo abre paneles e informes con datos de ejemplo, se conecta a orígenes de datos personalizados, actualiza los datos, etc.

El equipo de certificación proporciona comentarios si encuentra algún problema.  Para más información sobre los requisitos de las aplicaciones de Power BI, consulte la [documentación de las aplicaciones de Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Pasos siguientes

Le recomendamos supervisar periódicamente la aplicación en [AppSource Marketplace](https://appsource.microsoft.com).  Además, debe usar la característica [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) (Conclusiones sobre los vendedores) de [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) para obtener perspectivas sobre los clientes y el uso de las aplicaciones en Marketplace. Por último, puede [actualizar su oferta](./cpp-update-existing-offer.md).
