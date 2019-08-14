---
title: Personalización de idioma en Azure Active Directory B2C | Microsoft Docs
description: Más información acerca de cómo personalizar la experiencia de lenguaje.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5dd82cbb8370fd4c3ee0dca6a9acd0046c73c0ef
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716796"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalización de idioma en Azure Active Directory B2C

La personalización de idioma en Azure Active Directory B2C (Azure AD B2C) permite que el flujo de usuario albergue distintos idiomas a fin de satisfacer las necesidades de los clientes.  Microsoft proporciona las traducciones de [36 idiomas](#supported-languages), pero el usuario también puede proporcionar sus propias traducciones para cualquier idioma. Incluso si su experiencia se proporciona únicamente para un idioma, puede personalizar cualquier texto de las páginas.

## <a name="how-language-customization-works"></a>¿Cómo funciona la personalización de idioma?
Se utiliza la personalización de idioma para seleccionar los idiomas en los que está disponible el flujo de usuario. Cuando la característica está habilitada, puede proporcionar el parámetro de cadena de consulta, `ui_locales`, desde la aplicación. Al llamar en Azure AD B2C, la página se traduce a la configuración regional que haya indicado. Este tipo de configuración le proporciona un control completo sobre los idiomas del flujo de usuario y omite la configuración de idioma del explorador del cliente.

Quizás no necesite ese nivel de control sobre los idiomas que ve el cliente. Si no proporciona un parámetro `ui_locales`, la experiencia del cliente está determinada por la configuración de su explorador.  Aun así, puede controlar los idiomas a los que se traduce el flujo de usuario si los agrega como idioma admitido. Si el explorador del cliente está establecido para mostrar un idioma que no desea admitir, se muestra en su lugar el idioma que ha seleccionado como predeterminado en las referencias culturales admitidas.

- **Idioma especificado por configuraciones regionales de interfaz de usuario**: después de habilitar la personalización de idioma, el recorrido del usuario se traduce al idioma especificado aquí.
- **Idioma solicitado por el explorador**: si no se especificó ningún parámetro `ui_locales`, el recorrido del usuario se traduce al idioma solicitado por el explorador, *si se admite ese idioma*.
- **Idioma predeterminado de directiva**: si el explorador no especifica un idioma, o si especifica uno que no se admite, el recorrido del usuario se traduce al idioma predeterminado de la directiva.

>[!NOTE]
>Si va a usar atributos de usuario personalizados, debe proporcionar sus propias traducciones. Para más información, consulte [Personalización de las cadenas](#customize-your-strings).
>

## <a name="support-requested-languages-for-ui_locales"></a>Compatibilidad con idiomas solicitados de configuraciones regionales de interfaz de usuario
Las directivas que se crearon antes de la disponibilidad general de la personalización de idioma deben habilitar primero esta característica. Las directivas y los flujos de usuario que se crearon después tienen habilitada de forma predeterminada la personalización de idioma.

La habilitación de la personalización de idioma en un flujo de usuario le permite controlar ahora el idioma del flujo de usuario agregando el parámetro `ui_locales`.
1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
2. Haga clic en el flujo de usuario que desea habilitar para las traducciones.
3. Seleccione **Idiomas**.
4. Seleccione **Habilitación de la personalización de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Seleccione los idiomas que están habilitados en su flujo de usuario.
Habilite un conjunto de idiomas para la traducción del flujo de usuario cuando lo solicite el explorador sin el parámetro `ui_locales`.
1. Asegúrese de que el flujo de usuario tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la página **Idiomas** del flujo de usuario, seleccione un idioma que desee admitir.
3. En el panel de propiedades, cambie **Habilitado** a **Sí**.
4. Seleccione **Guardar** en la parte superior del panel de propiedades.

>[!NOTE]
>Si no se proporciona un parámetro `ui_locales`, la página se traduce al idioma del explorador del cliente solo si está habilitado.
>

## <a name="customize-your-strings"></a>Personalización de las cadenas
La personalización de idioma le permite personalizar cualquier cadena del flujo de usuario.
1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la página **Idiomas** del flujo de usuario, seleccione un idioma que desee personalizar.
3. En **Page-level-resources files** (Archivos de recursos de nivel de página), seleccione la página que desea editar.
4. Seleccione **Descargar valores predeterminados** (o **Descargar invalidaciones** si ha editado anteriormente este idioma).

Estos pasos le proporcionan un archivo JSON que puede usar para comenzar a editar las cadenas.

### <a name="change-any-string-on-the-page"></a>Cambio de cualquier cadena en la página
1. Abra el archivo JSON descargado según las instrucciones anteriores en un editor JSON.
2. Busque el elemento que desea cambiar.  Puede encontrar el valor de `StringId` de la cadena que busca o buscar el atributo `Value` que quiere cambiar.
3. Actualice el atributo `Value` con el que quiere que se muestre.
4. Para cada cadena que desea cambiar, cambie `Override` a `true`.
5. Guarde el archivo y cargue los cambios. (Puede encontrar el control de carga en el mismo lugar que donde descargó el archivo JSON).

>[!IMPORTANT]
>Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`.  Si no se cambia el valor, se omite la entrada.
>

### <a name="change-extension-attributes"></a>Cambio de los atributos de extensión
Si quiere cambiar la cadena de un atributo de usuario personalizado, o quiere agregar una a JSON, debe estar en el siguiente formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Reemplace `<ExtensionAttribute>` por el nombre de su atributo de usuario personalizado.

Reemplace `<ExtensionAttributeValue>` por la nueva cadena que se mostrará.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Entrega de una lista de valores mediante LocalizedCollections
Si desea proporcionar una lista establecida de valores para respuestas, debe crear un atributo `LocalizedCollections`.  `LocalizedCollections` es una matriz de pares de `Name` y `Value`. El orden de los elementos será el orden en el que se muestran.  Para agregar `LocalizedCollections`, utilice el siguiente formato:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` es el atributo de usuario para el que este atributo `LocalizedCollections` es una respuesta.
* `Name` es el valor que se muestra al usuario.
* `Value` es lo que se devuelve en la notificación cuando se selecciona esta opción.

### <a name="upload-your-changes"></a>Carga de los cambios
1. Una vez completados los cambios en el archivo JSON, vuelva al inquilino B2C.
2. Seleccione **Flujos de usuario** y haga clic en el flujo de usuario que desea habilitar para las traducciones.
3. Seleccione **Idiomas**.
4. Seleccione el idioma al que quiere traducir.
5. Seleccione la página en donde quiere proporcionar traducciones.
6. Seleccione el icono de carpeta y el archivo JSON para cargar.

Este cambio se guarda en el flujo de usuario automáticamente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalización de la interfaz de usuario de la página mediante la personalización de idioma

Hay dos maneras de localizar el contenido HTML. Una manera consiste en activar la [personalización de idioma](active-directory-b2c-reference-language-customization.md). Al habilitar esta característica, Azure AD B2C puede reenviar el parámetro de OpenID Connect, `ui-locales`, al punto de conexión.  El servidor de contenido puede usar este parámetro para proporcionar páginas HTML personalizadas específicas del idioma.

Como alternativa, puede extraer contenido de distintos lugares, según la configuración regional usada. En el punto de conexión habilitado para CORS, puede configurar una estructura de carpetas para hospedar contenido de idiomas concretos. Se llamará al adecuado si usa el valor de carácter comodín `{Culture:RFC5646}`.  Por ejemplo, supongamos que se trata del URI de la página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Puede cargar la página en `fr`. Cuando la página extrae contenido HTML y CSS, se extrae desde:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Incorporación de idiomas personalizados

También puede agregar idiomas para los que Microsoft no proporciona actualmente traducciones. Deberá proporcionar las traducciones para todas las cadenas del flujo de usuario.  Los idiomas y los códigos de configuración regional se limitan a los indicados en la norma ISO 639-1.

1. En el inquilino de Azure AD B2C, seleccione **Flujos de usuario**.
2. Haga clic en el flujo de usuario donde desea agregar idiomas personalizados y, luego, haga clic en **Idiomas**.
3. Seleccione **Agregar un idioma personalizado** en la parte superior de la página.
4. En el panel de contexto que se abre, identifique el idioma para el que va a proporcionar traducciones mediante la especificación de un código de configuración regional válido.
5. Puede descargar para cada página un conjunto de invalidaciones para inglés y trabajar en las traducciones.
6. Después de haber terminado con los archivos JSON, puede cargarlos para cada página.
7. Seleccione **Habilitar** y el flujo de usuario mostrará ahora este idioma para sus usuarios.
8. Guarde el idioma.

>[!IMPORTANT]
>Debe habilitar los idiomas personalizados o cargar invalidaciones para poder guardar.
>

## <a name="additional-information"></a>Información adicional

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalización de la interfaz de usuario de página como invalidaciones
Cuando se habilita la personalización de idioma, las ediciones anteriores de etiquetas que usan la personalización de la interfaz de usuario de página se almacenan en un archivo JSON para inglés (en). Para seguir cambiando las etiquetas y otras cadenas, cargue los recursos de idioma en Personalización de idioma.
### <a name="up-to-date-translations"></a>Actualización de traducciones
Microsoft se compromete a proporcionar las traducciones más actualizadas para que haga uso de ellas. Microsoft mejora continuamente las traducciones y garantiza su cumplimiento. Microsoft identificará errores y cambios en la terminología global y creará actualizaciones que funcionen perfectamente en su flujo de usuario.
### <a name="support-for-right-to-left-languages"></a>Compatibilidad con idiomas que se leen de derecha a izquierda
Microsoft no proporciona actualmente compatibilidad con idiomas que se leen de derecha a izquierda. Para ello, puede usar configuraciones regionales personalizadas y CSS para cambiar la manera en la que se muestran las cadenas.  Si necesita esta característica, vote por ella en [Comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traducciones de proveedores de identidades sociales
Microsoft proporciona el parámetro OIDC `ui_locales` a los inicios de sesión de redes sociales. Pero algunos proveedores de identidades de redes sociales, incluidas Facebook y Google, no los respetan.
### <a name="browser-behavior"></a>Comportamiento del explorador
Tanto Chrome como Firefox solicitan su idioma establecido. Si es un idioma admitido, se muestra antes el valor predeterminado. Microsoft Edge no solicita actualmente un idioma y va directamente al predeterminado.

### <a name="supported-languages"></a>Idiomas admitidos

| Idioma              | Código de idioma |
|-----------------------|---------------|
| Bangla                | bn            |
| Checo                 | cs            |
| Danés                | da            |
| Alemán                | de            |
| Griego                 | el            |
| English               | en            |
| Español               | es            |
| Finés               | fi            |
| Francés                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonés              | ja            |
| Canarés               | kn            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Maratí               | mr            |
| Malayo                 | ms            |
| Noruego Bokmal      | nb            |
| Neerlandés                 | nl            |
| Punjabi               | pa            |
| Polaco                | pl            |
| Portugués (Brasil)   | pt-br         |
| Portugués (Portugal) | pt-pt         |
| Rumano              | ro            |
| Ruso               | ru            |
| Eslovaco                | sk            |
| Sueco               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Tailandés                  | th            |
| Turco               | tr            |
| Chino (simplificado)  | zh-hans       |
| Chino (tradicional) | zh-hant       |
