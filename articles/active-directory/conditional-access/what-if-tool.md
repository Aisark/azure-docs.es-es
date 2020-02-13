---
title: 'Herramienta What if de acceso condicional: Azure Active Directory'
description: Obtenga información sobre cómo puede entender el impacto de las directivas de Acceso condicional en el entorno.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb2e98ad9bbd35f3ec507e36e958c5ce6ad2198
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185879"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Solución de problemas con la herramienta What If en el acceso condicional

[Acceso condicional](../active-directory-conditional-access-azure-portal.md) es una funcionalidad de Azure Active Directory (Azure AD) que le permite controlar cómo acceden los usuarios autorizados a las aplicaciones en la nube. ¿Cómo sabe qué esperar de las directivas de Acceso condicional en su entorno? Puede usar la **herramienta What If de Acceso condicional** para responder esta pregunta.

En este artículo se explica cómo puede usar esta herramienta para probar las directivas de Acceso condicional.

## <a name="what-it-is"></a>¿Qué es?

La **herramienta What If de directiva de Acceso condicional** le permite comprender el impacto que las directivas de Acceso condicional tienen en su entorno. En lugar de probar las directivas mediante varios inicios de sesión manuales, esta herramienta le permite evaluar el inicio de sesión simulado de un usuario. La simulación calcula el impacto que este inicio de sesión tiene en las directivas y genera un informe de simulación. En el informe no solo se muestran las directivas de Acceso condicional aplicadas, sino también las [directivas clásicas](policy-migration.md#classic-policies), si existen.    

La herramienta **What If** también ofrece una manera de determinar rápidamente las directivas que se aplican a un usuario específico. Por ejemplo, puede usar la información si necesita solucionar un problema.    

## <a name="how-it-works"></a>Funcionamiento

En la **herramienta What If de Acceso condicional**, primero debe configurar los valores del escenario de inicio de sesión que desea simular. Esta configuración incluye:

- El usuario que desea probar. 
- Las aplicaciones en la nube a las que el usuario intentaría acceder.
- Las condiciones en las cuales se realiza el acceso a las aplicaciones en la nube.
     
Como próximo paso, puede iniciar una ejecución de la simulación que evalúe la configuración. Solo las directivas habilitadas forman parte de una ejecución de evaluación.

Cuando la evaluación finaliza, la herramienta genera un informe de las directivas afectadas.

## <a name="running-the-tool"></a>Ejecución de la herramienta

La herramienta **What If** se encuentra en la página **[Acceso condicional: Directivas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** de Azure Portal.

Para iniciar la herramienta, en la barra de herramientas que aparece en la parte superior de la lista de directivas, haga clic en **What If**.

![What If](./media/what-if-tool/01.png)

Antes de ejecutar una evaluación, debe configurar los valores.

## <a name="settings"></a>Configuración

En esta sección se proporcionan detalles sobre los ajustes de la ejecución de simulación.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Usuario

Solo puede seleccionar un usuario. Este es el único campo obligatorio.

### <a name="cloud-apps"></a>Aplicaciones de nube

El valor predeterminado de esta configuración es **Todas las aplicaciones en la nube**. Con el valor predeterminado se realiza una evaluación de todas las directivas disponibles en el entorno. Puede restringir el ámbito a directivas que afecten aplicaciones en la nube específicas.

### <a name="ip-address"></a>Dirección IP

La dirección IP es una dirección IPv4 única que imita la [condición de ubicación](location-condition.md). La dirección representa la dirección para la conexión a Internet del dispositivo que el usuario usa para iniciar sesión. Por ejemplo, para comprobar la dirección IP de un dispositivo, navegue al sitio [What is my IP address](https://whatismyipaddress.com) (¿Cuál es mi dirección IP?).    

### <a name="device-platforms"></a>Plataformas de dispositivo

Esta configuración imita la [condición de plataformas de dispositivo](concept-conditional-access-conditions.md#device-platforms) y representa el equivalente de **Todas las plataformas (incluidas las no admitidas)** . 

### <a name="client-apps"></a>Aplicaciones cliente

Esta configuración imita la [condición de aplicaciones cliente](concept-conditional-access-conditions.md#client-apps-preview).
De manera predeterminada, esta configuración genera una evaluación de todas las directivas con las opciones **Explorador** o **Aplicaciones móviles y clientes de escritorio** seleccionadas en conjunto o de manera individual. También detecta las directivas que aplican **Exchange ActiveSync (EAS)** . Para restringir esta configuración, seleccione:

- **Explorador** para evaluar todas las directivas que tienen seleccionada al menos la opción **Explorador**. 
- **Aplicaciones móviles y clientes de escritorio** para evaluar todas las directivas que tienen seleccionada al menos la opción **Aplicaciones móviles y clientes de escritorio**. 

### <a name="sign-in-risk"></a>Riesgo de inicio de sesión

Esta configuración imita la [condición de riesgo de inicio de sesión](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Evaluación 

Haga clic en **What If** para iniciar una evaluación. El resultado de la evaluación le brinda un informe que consta de: 

![What If](./media/what-if-tool/03.png)

- Un indicador que le informa si existen directivas clásicas en su entorno.
- Directivas que se aplican al usuario.
- Directivas que no se aplican al usuario.

Si existen [directivas clásicas](policy-migration.md#classic-policies) en las aplicaciones en la nube seleccionadas, verá un indicador. Haga clic en el indicador y se le redirigirá a la página de directivas clásicas. En la página de directivas clásicas, puede migrar una directiva clásica o simplemente deshabilitarla. Cierre esta página para volver al resultado de la evaluación.

En la lista de las directivas que se aplican al usuario seleccionado, también puede encontrar una lista de [controles de concesión](controls.md#grant-controls) y [controles](controls.md#session-controls) de sesión con los que el usuario debe cumplir.

En la lista de las directivas que no se aplican al usuario, también puede encontrar los motivos por los cuales no se aplican estas directivas. Para cada directiva de la lista, el motivo representa la primera condición que no se cumplió. Un motivo posible para no aplicar una directiva es que se encuentre deshabilitada porque ya no se evalúa.   

## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, vea [Requerir MFA para aplicaciones específicas con acceso condicional a Azure Active Directory](app-based-mfa.md).
- Si está listo para configurar directivas de acceso condicional para su entorno, vea [Procedimientos recomendados para el acceso condicional en Azure Active Directory](best-practices.md). 
- Si desea migrar directivas clásicas, consulte [Migración de directivas clásicas en Azure Portal](policy-migration.md).  
