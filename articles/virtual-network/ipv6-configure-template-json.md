---
title: 'Implementación de una aplicación de pila doble IPv6 en Azure Virtual Network: plantilla de Resource Manager (versión preliminar)'
titlesuffix: Azure Virtual Network
description: En este artículo, se explica cómo se implementa una aplicación de pila doble IPv6 en Azure Virtual Network mediante plantillas de VM de Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131034"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Implementación de una aplicación de pila doble IPv6 en Azure: plantilla (versión preliminar)

En este artículo se proporciona una lista de tareas de configuración de IPv6 con la parte de la plantilla de VM de Azure Resource Manager a la que se aplica. Use la plantilla que se describe en este artículo para implementar en Azure una aplicación de pila doble (IPv4 + IPv6) que contenga una red virtual de pila doble con subredes IPv4 e IPv6, un equilibrador de carga con configuraciones de front-end duales (IPv4 + IPv6), VM con NIC que tengan una configuración de IP dual, un grupo de seguridad de red e IP públicas. 

## <a name="required-configurations"></a>Configuraciones necesarias

Busque las secciones de la plantilla para ver dónde se deben producir.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Elemento addressSpace de IPv6 para la red virtual

Sección de plantilla que se va a agregar:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Subred de IPv6 en el elemento addressSpace de la red virtual IPv6

Sección de plantilla que se va a agregar:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configuración de IPv6 para la NIC

Sección de plantilla que se va a agregar:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Reglas del grupo de seguridad de red (NSG) de IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Configuración condicional

Si usa un dispositivo de red virtual, agregue rutas IPv6 en la tabla de rutas. En caso contrario, esta configuración es opcional.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Configuración opcional

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Acceso a Internet de IPv6 para la red virtual

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Direcciones IP públicas IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Front-end IPv6 para Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Grupo de direcciones de back-end IPv6 para Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Reglas del equilibrador de carga de IPv6 para asociar puertos de entrada y de salida

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Ejemplo de JSON de plantilla de VM
Haga clic [aquí](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) para implementar una aplicación de pila doble IPv6 en Azure Virtual Network mediante una plantilla de Azure Resource Manager.

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar detalles sobre precios de [direcciones IP públicas](https://azure.microsoft.com/pricing/details/ip-addresses/), [ancho de banda de red](https://azure.microsoft.com/pricing/details/bandwidth/) o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
