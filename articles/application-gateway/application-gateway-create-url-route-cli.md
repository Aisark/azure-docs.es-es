---
title: 'Reglas de enrutamiento basado en rutas de dirección URL mediante la CLI: Azure Application Gateway'
description: Aprenda a crear reglas de enrutamiento basadas en rutas de dirección URL para una puerta de enlace de aplicaciones y un conjunto de escalado de máquinas virtuales mediante la CLI de Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 5f75ae1104297c461584e061f5a94aecd987caad
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246782"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Creación de una puerta de enlace de aplicaciones con reglas de enrutamiento basadas en rutas de dirección URL con la CLI de Azure

Puede usar la CLI de Azure para configurar [reglas de enrutamiento basadas en rutas de dirección URL](application-gateway-url-route-overview.md) cuando se crea una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, creará grupos de back-end mediante un [conjunto de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). A continuación, creará reglas de enrutamiento que garanticen que el tráfico web llega a los servidores adecuados de los grupos.

En este artículo aprenderá a:

> [!div class="checklist"]
> * Configuración de la red
> * Crear una puerta de enlace de aplicaciones con asignación de direcciones URL
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end

![Ejemplo de enrutamiento de direcciones URL](./media/application-gateway-create-url-route-cli/scenario.png)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Para crear un grupo de recursos, use [az group create](/cli/azure/group).

En el ejemplo siguiente, se crea un grupo de recursos llamado *myResourceGroupAG* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Crear recursos de red 

Cree la red virtual llamada *myVNet* y la subred llamada *myAGSubnet* mediante [az network vnet create](/cli/azure/network/vnet). Luego, puede agregar la subred llamada *myBackendSubnet* que necesitan los servidores back-end mediante [az network vnet subnet create](/cli/azure/network/vnet/subnet). Cree la dirección IP pública llamada *myAGPublicIPAddress* mediante [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Creación de la puerta de enlace de aplicaciones con asignación de direcciones URL

Puede usar [az network application-gateway create](/cli/azure/network/application-gateway) para crear la puerta de enlace de aplicaciones llamada *myAppGateway*. Cuando se crea una puerta de enlace de aplicaciones mediante la CLI de Azure, se especifica información de configuración, como capacidad, SKU y HTTP. La puerta de enlace de aplicaciones se asigna a los elementos *myAGSubnet* y *myAGPublicIPAddress* creados anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 La puerta de enlace de aplicaciones puede tardar varios minutos en crearse. Después de crear la puerta de enlace de aplicaciones, puede ver estas nuevas características de ella:

- *appGatewayBackendPool*: una puerta de enlace de aplicaciones debe tener al menos un grupo de direcciones de servidores back-end.
- *appGatewayBackendHttpSettings*: especifica que se use el puerto 80 y un protocolo HTTP para la comunicación.
- *appGatewayHttpListener*: agente de escucha predeterminado asociado con *appGatewayBackendPool*.
- *appGatewayFrontendIP*: asigna *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1*: la regla de enrutamiento predeterminada asociada a *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Adición de un puerto de back-end y grupos de back-end de imágenes y vídeo

Puede agregar los grupos back-end llamados *imagesBackendPool* y *videoBackendPool* a la puerta de enlace de aplicaciones mediante [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create). El puerto de front-end de los grupos se agrega mediante [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Adición del agente de escucha de back-end

Agregue el agente de escucha de back-end llamado *backendListener* que es necesario para enrutar el tráfico mediante [az network application-gateway http-listener create](/cli/azure/network/application-gateway).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Adición de asignación de ruta de URL

Las asignaciones de ruta de URL se aseguran de que las direcciones URL específicas se enrutan a grupos de back-end específicos. Puede crear las asignaciones de ruta de URL llamadas *imagePathRule* y *videoPathRule* mediante [az network application-gateway url-path-map create](/cli/azure/network/application-gateway) y [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Adición de reglas de enrutamiento

La regla de enrutamiento asocia las asignaciones de URL con el agente de escucha que ha creado. Puede agregar la regla llamada *rule2* mediante [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Creación de conjuntos de escalado de máquinas virtuales

En este ejemplo, creará tres conjuntos de escalado de máquinas virtuales que admiten los tres grupos de back-end que ha creado. Los conjuntos de escalado que crea se llaman *myvmss1*, *myvmss2* y *myvmss3*. Cada conjunto de escalado contiene dos instancias de máquina virtual en las que se instalará NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalación de NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

Para obtener la dirección IP pública de la puerta de enlace de aplicaciones, puede usar [az network public-ip show](/cli/azure/network/public-ip). Copie la dirección IP pública y péguela en la barra de direcciones del explorador. Como, por ejemplo, `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` o `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Prueba de la dirección URL base en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Cambie la dirección URL a `http://<ip-address>:8080/video/test.html` al final de la dirección URL base y verá algo parecido al ejemplo siguiente:

![Prueba de la dirección URL de imágenes en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Cambie la dirección URL a `http://<ip-address>:8080/video/test.html` y verá algo parecido al ejemplo siguiente.

![Prueba de la dirección URL de vídeo en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Configuración de la red
> * Crear una puerta de enlace de aplicaciones con asignación de direcciones URL
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end

Para más información acerca de las puertas de enlace de aplicaciones y sus recursos asociados, vaya a los artículos de procedimientos.
