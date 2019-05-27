---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158696"
---
Si tiene problemas al realizar cualquier operación en galerías de imágenes compartidas, definiciones de imágenes y versiones de imágenes, vuelva a ejecutar el comando con errores en modo de depuración. El modo de depuración se activa al pasar el conmutador **-debug** con la CLI y el conmutador **-debug** con PowerShell. Una vez que haya encontrado el error, siga este documento para solucionar los errores.


## <a name="unable-to-create-a-shared-image-gallery"></a>No se puede crear una galería de imágenes compartidas

Causas posibles:

*El nombre de la galería no es válido.*

Los caracteres permitidos para el nombre de la Galería son letras mayúsculas o minúsculas, números y puntos. El nombre de la Galería no puede contener guiones. Cambie el nombre de la galería y vuelva a intentarlo. 

*El nombre de la galería no es único dentro de la suscripción.*

Elija otro nombre de la galería y vuelva a intentarlo.


## <a name="unable-to-create-an-image-definition"></a>No es posible crear la definición de una imagen 

Causas posibles:

*El nombre de la definición de la imagen no es válido.*

Caracteres permitidos para la definición de la imagen son letras mayúsculas o minúsculas, dígitos, puntos, guiones y períodos. Cambie el nombre de la definición de la imagen y vuelva a intentarlo.

*No se rellenan las propiedades obligatorias para crear la definición de una imagen.*

Las propiedades como el nombre, publicador, oferta, SKU y tipo de sistema operativo son obligatorias. Compruebe si se pasan todas las propiedades.

Asegúrese de que el valor de **OSType**, Linux o Windows, de la definición de la imagen sea el mismo que el de la imagen administrada de origen que usa para crear la versión de la imagen. 


## <a name="unable-to-create-an-image-version"></a>No es posible crear la versión de una imagen 

Causas posibles:

*El nombre de la versión de la imagen no es válido.*

Los caracteres permitidos para la versión de una imagen son números y puntos. Los números deben estar dentro del rango de un entero de 32 bits. Formato: *VersiónPrincipal.VersiónSecundaria.Revisión*. Cambie el nombre de la versión de la imagen y vuelva a intentarlo.

*No se encuentra la imagen administrada de origen a partir la que se crea la versión de la imagen.* 

Compruebe si la imagen de origen existe y está en la misma región que la versión de la imagen.

*No se ha terminado de aprovisionar la imagen administrada.*

Asegúrese de que el estado de aprovisionamiento de la imagen administrada de origen sea **Correcto**.

*La lista de regiones de destino no incluye la región de origen.*

La lista de regiones de destino debe incluir la región de origen de la versión de la imagen. Asegúrese de haber incluido la región de origen en la lista de regiones de destino donde quiere que Azure replique la versión de la imagen.

*No se ha completado la replicación en todas las regiones de destino.*

Use la marca **--expand ReplicationStatus** para comprobar si se ha completado la replicación en todas las regiones de destino especificadas. Si no es así, espere hasta 6 horas para que se complete el trabajo. Si se produce un error, ejecute el comando de nuevo para crear y replicar la versión de la imagen. Si va a replicar la versión de la imagen en una gran cantidad de regiones de destino, tenga en cuenta la posibilidad de realizar la replicación en fases.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>No se puede crear una VM o un conjunto de escalado 

Causas posibles:

*El usuario que intenta crear una VM o un conjunto de escalado de máquinas virtuales no tiene acceso de lectura a la versión de la imagen.*

Póngase en contacto con el propietario de la suscripción y pídale que le conceda acceso de lectura a la versión de imagen o a los recursos principales (por ejemplo, la galería de imágenes compartidas o la definición de la imagen) a través de [Control de acceso basado en roles](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*No se encuentra la versión de la imagen.*

Compruebe que la región en la que intenta crear una VM o un conjunto de escalado de máquinas virtuales esté incluida en la lista de regiones de destino de la versión de la imagen. Si la región ya está en la lista de regiones de destino, compruebe si se ha completado el trabajo de replicación. Puede usar la marca **-ReplicationStatus** para comprobar si se ha completado la replicación en todas las regiones de destino especificadas. 

*La creación de la VM o el conjunto de escalado de máquinas virtuales tarda mucho tiempo.*

Compruebe que el valor de **OSType** de la versión de la imagen a partir de la cual intenta crear la VM o el conjunto de escalado de máquinas virtuales tiene el mismo valor de **OSType** que la imagen administrada de origen que usó para crear la versión de la imagen. 

## <a name="unable-to-share-resources"></a>No se pueden compartir los recursos

El uso compartido de recursos de la versión de imagen, definición de la imagen y Galería de imágenes compartidas entre suscripciones se habilita mediante [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>La replicación es lenta

Use la marca **--expand ReplicationStatus** para comprobar si se ha completado la replicación en todas las regiones de destino especificadas. Si no es así, espere hasta 6 horas para que se complete el trabajo. Si se produce un error, desencadene el comando de nuevo para crear y replicar la versión de la imagen. Si va a replicar la versión de la imagen en una gran cantidad de regiones de destino, tenga en cuenta la posibilidad de realizar la replicación en fases.

## <a name="azure-limits-and-quotas"></a>Límites y cuotas de Azure 

Los[límites y cuotas de Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) se aplican a todos los recursos de galería de imágenes compartidas, las versiones de imágenes, definiciones de imágenes. Asegúrese de encontrarse dentro de los límites para las suscripciones. 



