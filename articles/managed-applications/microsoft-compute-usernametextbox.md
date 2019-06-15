---
title: Elemento de interfaz de usuario UserNameTextBox de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.UserNameTextBox para Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700856"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento de interfaz de usuario Microsoft.Compute.UserNameTextBox
Control de cuadro de texto con validación integrada para nombres de usuario de Windows y Linux.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Si `constraints.required` está establecido en **true**, el cuadro de texto debe contener un valor para que la validación sea correcta. El valor predeterminado es **true**.
- `osPlatform` debe especificarse y puede ser **Windows** o **Linux**.
- `constraints.regex` es un patrón de expresión regular de JavaScript. Si se especifica, el valor del cuadro de texto debe coincidir con el patrón para que la validación sea correcta. El valor predeterminado es **null**.
- `constraints.validationMessage` es una cadena que se muestra cuando el valor del cuadro de texto produce un error en la validación especificada por `constraints.regex`. Si no se especifica, se utilizan los mensajes de validación integrados del cuadro de texto. El valor predeterminado es **null**.
- Este elemento tiene validación integrada que se basa en el valor especificado para `osPlatform`. La validación integrada puede usarse junto con una expresión regular personalizada. Si se especifica un valor para `constraints.regex`, se activan las validaciones integradas y personalizadas.

## <a name="sample-output"></a>Salida de ejemplo
```json
"Example name"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
