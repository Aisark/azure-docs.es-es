---
title: Envío y recepción de mensajes AS2 para B2B
description: Intercambio de mensajes AS2 para escenarios de integración empresarial B2B en Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 08/22/2019
ms.openlocfilehash: 9f72edecc07c34a0f176e52f6b70644f9ceb16e0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666710"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Intercambio de mensajes AS2 para la integración empresarial B2B en Azure Logic Apps con Enterprise Integration Pack

Para trabajar con mensajes AS2 en Azure Logic Apps, puede usar el conector AS2, que proporciona los desencadenadores y acciones necesarios para administrar las comunicaciones AS2. Por ejemplo, para garantizar la seguridad y confiabilidad al transmitir mensajes, puede utilizar estas acciones:

* La acción [**Codificar con AS2**](#encode) para proporcionar firmas digitales, cifrado y confirmaciones mediante las notificaciones de disposición del mensaje (MDN), lo que ayuda a admitir la recepción sin rechazo. Por ejemplo, esta acción se aplica a los encabezados AS2/HTTP y realiza estas tareas cuando se configura:

  * Firma los mensajes salientes.
  * Cifra los mensajes salientes.
  * Comprime el mensaje.
  * Transmite el nombre de archivo en el encabezado MIME.

* La acción [**Descodificar con AS2**](#decode) para proporcionar firmas digitales, descifrado y confirmaciones a través de las notificaciones de disposición de mensajes (MDN). Por ejemplo, esta acción lleva a cabo estas tareas:

  * Procesa los encabezados AS2/HTTP.
  * Reconcilia los MDN recibidos con los mensajes de salida originales.
  * Actualiza y correlaciona registros en la base de datos sin rechazo.
  * Escribe registros para los informes de estado de AS2.
  * Devuelve el contenido de la carga útil con codificación base64.
  * Determina si el MDN es necesario. En función del contrato AS2, determina si el MDN deben ser sincrónico o asincrónico.
  * Genera el MDN sincrónico o asincrónico según el contrato AS2.
  * Establece las propiedades y los token de correlación en los MDN.

  Esta acción también realiza estas tareas cuando se configura:

  * Comprueba la firma.
  * Descifra los mensajes.
  * Descomprime el mensaje.
  * Comprueba y prohíbe duplicados de identificador de mensaje.

En este artículo se muestra cómo agregar la codificación AS2 y las acciones de descodificación a una aplicación lógica existente.

> [!IMPORTANT]
> El conector AS2 original va a quedar en desuso, por lo que debe asegurarse de usar el conector **AS2 (v2)** en su lugar. Esta versión proporciona las mismas funcionalidades que la versión original, es nativa en el entorno de tiempo de ejecución de Logic Apps y proporciona mejoras de rendimiento significativas en cuanto al rendimiento y el tamaño de los mensajes. Además, el conector v2 nativo no requiere la creación de una conexión a la cuenta de integración. En su lugar, tal y como se describe en los requisitos previos, asegúrese de vincular la cuenta de integración a la aplicación lógica donde planea usar el conector.

## <a name="prerequisites"></a>Prerequisites

* Suscripción a Azure. Si aún no tiene ninguna suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* La aplicación lógica desde la que quiere usar el conector AS2 y un desencadenador que inicie el flujo de trabajo de la aplicación lógica. El conector AS2 proporciona únicamente las acciones, no los desencadenadores. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) asociada con la suscripción a Azure y vinculada a las aplicaciones lógicas en las que planea usar el conector AS2. Tanto la cuenta de integración como la aplicación de lógica deben existir en la misma ubicación o región de Azure.

* Al menos dos [entidades](../logic-apps/logic-apps-enterprise-integration-partners.md) que ya haya creado en su cuenta de integración mediante el calificador de identidad AS2.

* Para poder usar el conector AS2, debe crear un [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre las entidades y almacenarlo en la cuenta de integración.

* Si usa [Azure Key Vault](../key-vault/key-vault-overview.md) para la administración de certificados, compruebe que las claves de almacén permitan las operaciones **Cifrar** y **Descifrar**. En caso contrario, las acciones de codificación y descodificación producirán un error.

  En Azure Portal, vaya al almacén de claves, consulte las **operaciones permitidas** de la clave de almacén y confirme que las operaciones **Cifrar** y **Descifrar** están seleccionadas.

  ![Comprobar las operaciones con claves de almacén](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificación de mensajes AS2

1. Si aún no lo ha hecho, en [Azure Portal](https://portal.azure.com), abra la aplicación lógica en el Diseñador de aplicación lógica.

1. En el diseñador, agregue una nueva acción a la aplicación lógica.

1. En **Elegir una acción** y en el cuadro de búsqueda, seleccione **Todas**. En el cuadro de búsqueda, escriba "codificación con AS2" y asegúrese de que selecciona la acción AS2 (v2): **Codificación con AS2**

   ![Seleccionar "Codificación con AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Ahora, proporcione la información de las siguientes propiedades:

   | Propiedad | Descripción |
   |----------|-------------|
   | **Mensaje que se codificará** | Carga útil del mensaje. |
   | **AS2 desde** | Identificador del remitente del mensaje según lo especificado por su contrato AS2. |
   | **AS2 hasta** | Identificador del receptor del mensaje según lo especificado por su contrato AS2. |
   |||

   Por ejemplo:

   ![Propiedades de codificación del mensaje.](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Descodificación de mensajes AS2

1. Si aún no lo ha hecho, en [Azure Portal](https://portal.azure.com), abra la aplicación lógica en el Diseñador de aplicación lógica.

1. En el diseñador, agregue una nueva acción a la aplicación lógica.

1. En **Elegir una acción** y en el cuadro de búsqueda, seleccione **Todas**. En el cuadro de búsqueda, escriba "descodificación con AS2" y asegúrese de que selecciona la acción AS2 (v2): **Descodificación con AS2**

   ![Seleccionar "Descodificación con AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. En las propiedades **Mensaje que se codificará** y **Encabezados del mensaje**, seleccione estos valores de las salidas anteriores del desencadenador o acción.

   Por ejemplo, suponga que la aplicación lógica recibe mensajes a través de un desencadenador de solicitud. Puede seleccionar las salidas de ese desencadenador.

   ![Seleccione el cuerpo y los encabezados de las salidas de la solicitud](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Muestra

Para intentar implementar una aplicación lógica totalmente operativa y conocer el escenario de ejemplo de AS2, consulte el artículo sobre [escenario y plantilla de aplicaciones lógicas de AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referencia de conectores

Para obtener datos técnicos, como los desencadenadores, las acciones y los límites, tal como lo describe el archivo OpenAPI (antes Swagger) del conector, consulte la [página de referencia del conector](/connectors/as2/).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
