---
title: Elemento de interfaz de usuario PasswordBox de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.PasswordBox para Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 944f59da680c3a058a3cd245cca48d903e44ab87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251953"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.PasswordBox
Control que puede usarse para proporcionar una contraseña y confirmarla.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Este elemento no admite la propiedad `defaultValue`.
- Para más detalles sobre la implementación de `constraints`, consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Si `options.hideConfirmation` está establecido en **true**, se oculta el segundo cuadro de texto para confirmar la contraseña del usuario. El valor predeterminado es **false**.

## <a name="sample-output"></a>Salida de ejemplo
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
