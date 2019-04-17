---
title: Inicio de sesión en Azure Notebooks
description: Configure su cuenta de usuario de Azure Notebooks con una cuenta Microsoft o una cuenta profesional o educativa.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608017"
---
# <a name="your-user-account-for-azure-notebooks"></a>Su cuenta de usuario de Azure Notebooks

Puede usar Azure Notebooks iniciando sesión o no con una cuenta de usuario:

- Sin iniciar sesión, puede crear y ejecutar cuadernos, pero no podrá retener dichos cuadernos o archivos de datos como parte de proyectos. Los usuarios que reciben un vínculo a un cuaderno de Azure Notebook, por ejemplo, pueden disfrutar de ellos sin tener que iniciar sesión.
- Cuando se ha iniciado sesión, Azure Notebooks conserva todos los proyectos con su cuenta. Los usuarios con sesión iniciada tienen también un identificador de usuario que les permite compartir sus proyectos y cuadernos con otras personas.
  - Cuando la cuenta usada para Azure Notebooks también está asociada con una suscripción a Azure, disfrutará de ventajas adicionales como la ejecución de cuadernos en los servidores más eficaces, la creación de cuadernos privados y la concesión de permisos para los cuadernos a usuarios individuales.

Iniciar sesión en Azure Notebooks requiere una cuenta de Microsoft o una cuenta "profesional o educativa". Se le pide su cuenta al seleccionar el comando **Iniciar sesión** en la parte superior derecha de la página de cuadernos:

![Comando de inicio de sesión de Azure Notebooks](media/accounts/sign-in-command.png)

Todo el trabajo que haga en Azure Notebooks se asociará con la cuenta que use para iniciar sesión. Cada cuenta debe tener también un identificador de usuario único en el [perfil de usuario](azure-notebooks-user-profile.md). Como resultado, puede iniciar sesión en Azure Notebooks con distintas cuentas si necesita mantener conjuntos separados de proyectos e identidades independientes. Por ejemplo, todos los miembros de un equipo de ciencia de datos podrían tener tanto cuentas individuales como una cuenta compartida de grupo para presentar su trabajo a personas de fuera de su empresa. De igual manera, los profesores pueden mantener una cuenta para su rol de enseñanza que es diferente del de la cuenta que se usa en una consultoría externa o en un trabajo de código abierto.

## <a name="microsoft-accounts"></a>Cuentas de Microsoft

Las cuentas de Microsoft se usan para iniciar sesión en cualquier número de productos y servicios de Microsoft, como Windows, Azure, outlook.com, OneDrive y XBox Live. Si usa alguno de estos servicios, es probable que ya tenga una cuenta de Microsoft que puede usar con Azure Notebooks.

Si no está seguro, seleccione el comando **Crear una** en el símbolo del sistema de la cuenta. Puede crear una nueva cuenta de Microsoft con cualquier dirección de correo electrónico de cualquier proveedor.

![Comando para crear una nueva cuenta de Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Si intenta crear una nueva cuenta con una dirección de correo electrónico que ya tiene una cuenta asociada con él, es posible que vea el mensaje "no se puede iniciar sesión aquí con un trabajo o una dirección de correo electrónico escolar. Utilizar un correo electrónico personal, como Gmail o Yahoo!, u obtenga un nuevo correo electrónico de Outlook." En ese caso, intente iniciar sesión con la dirección de correo electrónico de trabajo sin necesidad de crear una nueva cuenta.

Para cuentas de menores, el acceso a Azure Notebooks se bloquea por defecto. Al iniciar sesión con una cuenta de un menor, se muestra el error siguiente:

![Error when attempting to sign in with a child account: something went wrong, your parent has blocked access (Error al intentar iniciar sesión con una cuenta de un menor: hubo un problema, tus padres han bloqueado el acceso)](media/accounts/child-account-error.png)

Para habilitar el acceso, uno de los padres debe efectuar los pasos siguientes:

1. Visitar `https://account.live.com/mk` e iniciar sesión con una cuenta de adulto.
1. En la sección del menor en cuestión, seleccione **Administrar el acceso a aplicaciones de terceros de este niño**.
1. En la siguiente página, seleccione **Habilitar acceso**.
1. Cuando la cuenta del menor se vuelva a usar para iniciar sesión en Azure Notebooks, seleccione **Sí** en el símbolo del sistema de permisos que aparece.

> [!Warning]
> Habilitar el acceso a aplicaciones de terceros para Azure Notebooks también habilita el acceso a todas las demás aplicaciones de terceros. Se aconseja a los padres que sean discretos al permitir el acceso y que vigilen más de cerca la actividad de sus hijos.

## <a name="work-or-school-accounts"></a>Cuentas profesionales o educativas

El administrador de la organización crea una cuenta profesional o educativa para permitir a un miembro de la organización que acceda a los servicios en la nube de Microsoft, como Office 365, y también como una cuenta para iniciar sesión en Windows en un equipo unido al dominio. Una cuenta profesional o educativa normalmente usa una dirección de correo electrónico de una organización, tal como any-user@contoso.com.

Iniciar sesión en Azure Notebooks con una cuenta profesional o educativa puede requerir el consentimiento del administrador porque Azure Notebooks recopila o usa (pero no divulga) información como la dirección de correo electrónico de la cuenta ni la información del explorador del usuario. (Los datos del explorador se usan para optimizar las características de acuerdo con el uso popular.)

El administrador de una cuenta organizativa debe proporcionar el consentimiento en nombre de los usuarios si estos no pueden hacerlo individualmente. En este caso, los usuarios ven el mensaje "No puede acceder a esta aplicación":

![Mensaje "No puede acceder a esta aplicación" al usar una cuenta profesional o educativa](media/accounts/consent-permissions-denied.png)

Para dar su consentimiento como administrador, use la [página de consentimiento del administrador](https://notebooks.azure.com/account/adminConsent), que le guiará a través del proceso.

## <a name="next-steps"></a>Pasos siguientes  

> [!div class="nextstepaction"]
> [Editar el perfil y el identificador de usuario](azure-notebooks-user-profile.md)
