---
title: Contenedor personalizado de CI/CD de GitHub Actions
description: Aprenda a usar GitHub Actions para implementar contenedores Linux personalizados en App Service desde una canalización de CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246967"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implementación de un contenedor personalizado en App Service con Acciones de GitHub

[Acciones de GitHub](https://help.github.com/en/articles/about-github-actions) le ofrece la flexibilidad de compilar un flujo de trabajo del ciclo de vida de desarrollo de software automatizado. Gracias a la [acción de Azure App Service para contenedores](https://github.com/Azure/webapps-container-deploy), puede automatizar el flujo de trabajo para implementar aplicaciones como [contenedores personalizados en App Service](https://azure.microsoft.com/services/app-service/containers/) con Acciones de GitHub.

> [!IMPORTANT]
> Acciones de GitHub está actualmente en versión beta. Primero debe [registrarse para unirse a la versión preliminar](https://github.com/features/actions) mediante su cuenta de GitHub.
> 

Un archivo YAML (.yml) define un flujo de trabajo en la ruta de acceso `/.github/workflows/` de su repositorio. En esta definición se incluyen los diversos pasos y parámetros que componen el flujo de trabajo.

Para un flujo de trabajo de contenedor de Azure App Service, el archivo tiene tres secciones:

|Sección  |Tareas  |
|---------|---------|
|**Autenticación** | 1. Defina una entidad de servicio. <br /> 2. Cree un secreto de GitHub. |
|**Compilar** | 1. Configure el entorno. <br /> 2. Compile la imagen de contenedor. |
|**Implementación** | 1. Implemente la imagen de contenedor. |

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Puede crear una [entidad de servicio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) mediante el comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) de la [CLI de Azure](https://docs.microsoft.com/cli/azure/). Puede ejecutar este comando mediante [Azure Cloud Shell](https://shell.azure.com/) en Azure Portal o seleccionando el botón **Pruébelo**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

La salida es un objeto JSON con las credenciales de asignación de roles que proporcionan acceso a la aplicación App Service similar al siguiente. Copie este objeto JSON para autenticarse desde GitHub.

 ```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Siempre es recomendable conceder acceso mínimo. Puede restringir el ámbito del anterior comando de Az CLI a la aplicación de App Service específica y la instancia de Azure Container Registry donde se insertan las imágenes del contenedor.

## <a name="configure-the-github-secret"></a>Configuración del secreto de GitHub

En el ejemplo siguiente se usan credenciales de nivel de usuario, es decir, la entidad de servicio de Azure para la implementación. Siga los pasos para configurar el secreto:

1. En [GitHub](https://github.com/), examine el repositorio y seleccione **Configuración > Secretos > Agregar un nuevo secreto**.

2. Pegue el contenido del comando `az cli` siguiente como valor de la variable secreta. Por ejemplo, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Ahora en el archivo de flujo de trabajo de la rama: `.github/workflows/workflow.yml` reemplaza el secreto en la acción de inicio de sesión de Azure por su secreto.

4. Del mismo modo, defina los siguientes secretos adicionales para las credenciales del registro de contenedor y establézcalos en la acción de inicio de sesión de Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Una vez definidos, verá los secretos como se muestran a continuación.

    ![secretos de contenedor](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Compilación de la imagen de contenedor

En el ejemplo siguiente se muestra parte del flujo de trabajo que compila la imagen de Docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Implementación en un contenedor de App Service

Para implementar la imagen en un contenedor personalizado en App Service, use la acción `azure/webapps-container-deploy@v1`. Esta acción tiene cinco parámetros:

| **Parámetro**  | **Explicación**  |
|---------|---------|
| **app-name** | (Obligatorio) Especifique el nombre de la aplicación de App Service. | 
| **slot-name** | (Opcional) Especifique un espacio existente que no sea el de producción. |
| **images** | (Obligatorio) Especifique el nombre completo de las imágenes de contenedor. Por ejemplo, 'myregistry.azurecr.io/nginx:latest' o 'python:3.7.2-alpine/'. En el caso de una aplicación de varios contenedores, se pueden especificar varios nombres de imagen de contenedor (separados por varias líneas). |
| **configuration-file** | (Opcional) Ruta de acceso del archivo Docker-Compose. Debe ser una ruta de acceso completa o relativa al directorio de trabajo predeterminado. Se requiere para las aplicaciones de varios contenedores. |
| **container-command** | (Opcional) Escriba el comando de inicio. Por ejemplo, dotnet run o dotnet filename.dll |

A continuación se muestra el flujo de trabajo de ejemplo para compilar e implementar una aplicación Node.js en un contenedor personalizado en App Service.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar nuestro conjunto de acciones agrupadas en distintos repositorios de GitHub, cada uno con documentación y ejemplos que le ayudarán a usar GitHub con CI/CD y a implementar sus aplicaciones en Azure.

- [Inicio de sesión de Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp for containers](https://github.com/Azure/webapps-container-deploy)

- [Inicio y cierre de sesión de Docker](https://github.com/Azure/docker-login)

- [Eventos que desencadenan flujos de trabajo](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implementación de K8s](https://github.com/Azure/k8s-deploy)

- [Flujos de trabajo de CI de inicio](https://github.com/actions/starter-workflows)

- [Flujos de trabajo de inicio para implementar en Azure](https://github.com/Azure/actions-workflow-samples)
