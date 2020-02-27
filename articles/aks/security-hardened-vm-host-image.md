---
title: Protección de la seguridad en hosts de máquina virtual de AKS
description: Más información sobre la protección de la seguridad en el sistema operativo de los hosts de máquina virtual de AKS
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594387"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Protección de la seguridad en hosts de máquina virtual de AKS 

Azure Kubernetes Service (AKS) es un servicio seguro compatible con los estándares SOC, ISO, PCI DSS e HIPAA. En este artículo se explica la protección de la seguridad que se aplica a los hosts de máquina virtual de AKS. Para más información acerca de la seguridad de AKS, consulte [Conceptos de seguridad de las aplicaciones y los clústeres en Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

Los clústeres de AKS se implementan en máquinas virtuales de host, que ejecutan un sistema operativo optimizado para la seguridad. El sistema operativo de este host se basa en una imagen de Ubuntu 16.04.LTS a la que se aplica un conjunto de pasos adicionales para proteger la seguridad (consulte Detalles de protección de la seguridad).   

El objetivo del sistema operativo del host con protección de la seguridad es reducir el área expuesta de ataque y permitir la implementación de contenedores de forma segura. 

> [!Important]
> El sistema operativo con protección de la seguridad NO ha pasado el banco de pruebas de CIS. Aunque hay superposiciones con los bancos de pruebas de CIS, el objetivo no es ser compatible con CIS. El objetivo de la protección del sistema operativo del host es converger en un nivel de seguridad coherente con los estándares de seguridad del host interno de Microsoft. 

## <a name="security-hardening-features"></a>Características de la protección de la seguridad 

* AKS proporciona de forma predeterminada un sistema operativo de host optimizado para la seguridad. Actualmente no hay ninguna opción para seleccionar otro sistema operativo. 

* Azure aplica revisiones diarias (lo que incluye revisiones de seguridad) a los hosts de máquina virtual de AKS. Algunas de estas revisiones requerirán que se reinicie el equipo, pero otras no. Usted es el responsable de programar los reinicios del host de máquina virtual de AKS cuando sea necesario. Para obtener instrucciones sobre cómo automatizar la aplicación de parches de AKS, consulte el artículo acerca de la [aplicación de parches a los nodos de AKS](https://docs.microsoft.com/azure/aks/node-updates-kured).

A continuación, encontrará un resumen del trabajo de protección de imágenes que se implementa en AKS-Engine para producir el sistema operativo de host optimizado para la seguridad. El trabajo se implementó [en este proyecto de GitHub](https://github.com/Azure/aks-engine/projects/7).  

Actualmente, AKS-Engine no promueve ni se adhiere a ningún estándar de seguridad específico, pero se proporcionan identificadores de las auditorías de CIS (Center for Internet Security) cuando proceda. 

## <a name="whats-configured"></a>¿Qué se configura?

| CIS  | Descripción de la auditoría| 
|---|---|
| 1.1.1.1 |Asegurarse de que el montaje de sistemas de archivos cramfs está deshabilitado|
| 1.1.1.2 |Asegurarse de que el montaje de sistemas de archivos freevxfs está deshabilitado|
| 1.1.1.3 |Asegurarse de que el montaje de sistemas de archivos jffs2 está deshabilitado|
| 1.1.1.4 |Asegurarse de que el montaje de sistemas de archivos HFS está deshabilitado|
| 1.1.1.5 |Asegurarse de que el montaje de sistemas de archivos HFS Plus está deshabilitado|
|1.4.3 |Asegurarse de la autenticación que se requiere para el modo de usuario único |
|1.7.1.2 |Asegurarse de que el banner de advertencia de inicio de sesión local está configurado correctamente |
|1.7.1.3 |Asegurarse de que el banner de advertencia de inicio de sesión remoto está configurado correctamente |
|1.7.1.5 |Asegurarse de que están configurados los permisos en/etc/issue |
|1.7.1.6 |Asegurarse de que están configurados los permisos en/etc/issue.net |
|2.1.5 |Asegurarse de que el valor de --streaming-connection-idle-timeout no es 0 |
|3.1.2 |Asegurarse de que el envío de redireccionamiento de paquetes está deshabilitado |
|3.2.1 |Asegurarse de que no se aceptan los paquetes enrutados de origen |
|3.2.2 |Asegurarse de que no se aceptan los redireccionamientos de ICMP |
|3.2.3 |Asegurarse de que no se aceptan los redireccionamientos de ICMP seguros |
|3.2.4 |Asegurarse de que se registran los paquetes sospechosos |
|3.3.1 |Asegurarse de que no se aceptan anuncios de enrutadores IPv6 |
|3.5.1 |Asegurarse de que DCCP está deshabilitado |
|3.5.2 |Asegurarse de que SCTP está deshabilitado |
|3.5.3 |Asegurarse de que RDS está deshabilitado |
|3.5.4 |Asegurarse de que TIPC está deshabilitado |
|4.2.1.2 |Asegurarse de que el registro está configurado |
|5.1.2 |Asegurarse de que están configurados los permisos en/etc/crontab |
|5.2.4 |Asegurarse de que el reenvío de SSH X11 está deshabilitado |
|5.2.5 |Asegúrese de que el valor de SSH MaxAuthTries es 4, o menos |
|5.2.8 |Asegurarse de que el inicio de sesión de la raíz de SSH está deshabilitado |
|5.2.10 |Asegúrese de que SSH PermitUserEnvironment está deshabilitado |
|5.2.11 |Asegurarse de que solo se usan algoritmos MAX aprobados |
|5.2.12 |Asegúrese de que está configurado el intervalo de tiempo de espera de inactividad de SSH |
|5.2.13 |Asegúrese de que el valor de LoginGraceTime de SSH es un minuto, o menos |
|5.2.15 |Asegurarse de que está configurado el banner de advertencia de SSH |
|5.3.1 |Asegurarse de que están configurados los requisitos para la creación de contraseñas |
|5.4.1.1 |Asegurarse de que la contraseña expira en un plazo máximo de 90 días |
|5.4.1.4 |Asegurarse de que el plazo máximo del bloqueo de contraseñas inactivas es 30 días |
|5.4.4 |Asegurarse de que el umask de usuario predeterminado es 027, o incluso más restrictivo |
|5.6 |Asegurarse de que está restringido el acceso al comando su|

## <a name="additional-notes"></a>Notas adicionales
 
* Para reducir aún más el área expuesta a ataques, se han deshabilitado en el sistema operativo algunos controladores de módulos de kernel que no eran necesarios. 

* El sistema operativo con protección de seguridad NO se admite fuera de la plataforma AKS. 

## <a name="next-steps"></a>Pasos siguientes  

Para más información acerca de la seguridad de AKS, consulte los siguientes artículos: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Consideraciones sobre la seguridad de AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Procedimientos recomendados para AKS](https://docs.microsoft.com/azure/aks/best-practices)
