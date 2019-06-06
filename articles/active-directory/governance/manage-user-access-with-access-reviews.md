---
title: Administrar el acceso de usuario con revisiones de acceso - Azure Active Directory | Microsoft Docs
description: Aprenda a administrar el acceso de los usuarios, como la pertenencia a un grupo o la asignación a una aplicación con revisiones de acceso de Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d873f1dace3e3be6a8767e77d57db5ac554afd7
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473809"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Administración del acceso de los usuarios con las revisiones de acceso de Azure AD

Con Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen el acceso adecuado. Puede pedir a los propios usuarios o a quien decida en su lugar que participen en una revisión de acceso y vuelvan a certificar (o atestiguar) el acceso de los usuarios. Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando una revisión de acceso haya terminado, es posible hacer cambios y retirar la concesión de acceso a los usuarios que ya no lo necesitan.

> [!NOTE]
> Si desea revisar solo el acceso de los usuarios invitados y no el de todos los tipos de usuarios, consulte [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](manage-guest-access-with-access-reviews.md). Si desea revisar la pertenencia de los usuarios a roles administrativos tales como administrador global, consulte [Inicio de una revisión de acceso en Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Requisitos previos

- Azure AD Premium P2

Para obtener más información, consulte [qué usuarios deben tener licencias?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review"></a>Creación y realización de una revisión de acceso

Puede tener uno o más usuarios como revisores en una revisión de acceso.  

1. Seleccione un grupo de Azure AD con uno o más miembros. O bien, seleccione una aplicación conectada a Azure AD con uno o más usuarios asignados a ella. 

2. Decida si cada usuario revisará su propio acceso o bien si uno o más usuarios revisarán el acceso de todos.

3. Como administrador global o administrador de usuarios, vaya a la [página del gobierno de identidades](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Cree la revisión de acceso. Para obtener más información, consulte [crear una revisión de acceso de grupos o aplicaciones](create-access-review.md).

5. Cuando se inicie la revisión de acceso, pida a los revisores que proporcionen una entrada. De forma predeterminada, cada uno de ellos recibe un correo electrónico de Azure AD con un vínculo al panel de acceso, donde se [revisen el acceso a grupos o aplicaciones](perform-access-review.md).

6. Si los revisores no han proporcionado información, puede pedir a Azure AD que les envíe un recordatorio. De forma predeterminada, Azure AD envía automáticamente un recordatorio hacia la mitad del plazo de finalización a los revisores que no hayan respondido.

7. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para obtener más información, consulte [revisión de acceso de grupos o aplicaciones](complete-access-review.md).


## <a name="next-steps"></a>Pasos siguientes

[Creación de una revisión de acceso de grupos o aplicaciones](create-access-review.md)




