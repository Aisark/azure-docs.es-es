---
title: Uso de Terraform con Azure
description: Introducción al uso de Terraform para versionar e implementar la infraestructura de Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: d1754594c651206a0d4d15e659e2926557f9e912
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158993"
---
# <a name="terraform-with-azure"></a>Terraform con Azure

[Hashicorp Terraform](https://www.terraform.io/) es una herramienta de código abierto para aprovisionar y administrar la infraestructura de nube. Programa la infraestructura en archivos de configuración que describen la topología de los recursos en la nube. Estos recursos incluyen máquinas virtuales, cuentas de almacenamiento e interfaces de red. La CLI de Terraform proporciona un mecanismo sencillo para implementar y versionar los archivos de configuración en Azure.

En este artículo se describen las ventajas de usar Terraform para administrar la infraestructura de Azure.

## <a name="automate-infrastructure-management"></a>Automatice la administración de la infraestructura.

Los archivos de configuración basados en plantilla de Terraform permiten definir, aprovisionar y configurar recursos de Azure de forma repetible y predecible. Automatizar la infraestructura tiene varias ventajas:

- Reduce el potencial de errores humanos al implementar y administrar la infraestructura.
- Implementa la misma plantilla varias veces para crear entornos de desarrollo, prueba y producción idénticos.
- Reduce el costo de entornos de desarrollo y prueba al crearlos a petición.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Comprensión de los cambios de infraestructura antes de que se apliquen

A medida que la topología de los recursos se vuelve más compleja, comprender el significado y el efecto de los cambios de infraestructura puede resultar difícil.

La CLI de Terraform permite a los usuarios validar y obtener una vista previa de los cambios de la infraestructura antes de la aplicación. Obtener una vista previa de los cambios de infraestructura de forma segura tiene varias ventajas:
- Los miembros del equipo pueden colaborar de forma más eficaz al comprender rápidamente los cambios propuestos y su impacto.
- Los cambios no deseados se pueden detectar al principio del proceso de desarrollo

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implementación de la infraestructura en varias nubes

Terraform es ideal para implementar una infraestructura en varios proveedores de servicios en la nube. Permite a los desarrolladores usar herramientas coherentes para administrar cada definición de infraestructura.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una visión general de Terraform y sus ventajas, estos son los próximos pasos sugeridos:

- Para empezar, [instale Terraform y configúrelo para que use Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Cree una máquina virtual de Azure con Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm).
- Explore el [módulo Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/). 