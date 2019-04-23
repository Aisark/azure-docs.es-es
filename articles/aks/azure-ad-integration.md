---
title: Integración de Azure Active Directory con Azure Kubernetes Service
description: Creación de clústeres de Azure Kubernetes Service (AKS) habilitados para Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: db92526bd02ba55be5df7ce6999e3099e72b8fa5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014479"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integración de Azure Active Directory con Azure Kubernetes Service

Es posible configurar Azure Kubernetes Service (AKS) para que utilice Azure Active Directory (AD) para la autenticación de usuarios. En esta configuración, puede iniciar sesión un clúster de AKS mediante el token de autenticación de Azure Active Directory. Además, los administradores de clústeres pueden configurar Kubernetes control de acceso basado en roles (RBAC) en función de una identidad o el directorio de pertenencia a grupos.

En este artículo se muestra cómo implementar los requisitos previos para AKS y Azure AD y, a continuación, cómo implementar un clúster de Azure habilitado para AD y crear un rol RBAC básico en el clúster de AKS.

Se aplican las siguientes limitaciones:

- Azure AD solo puede habilitarse cuando se crea un clúster habilitado para RBAC nuevo. No se puede habilitar Azure AD en un clúster AKS existente.
- Los usuarios *invitados* en Azure AD, como los que usan un inicio de sesión federado de un directorio distinto, no se admiten.

## <a name="authentication-details"></a>Detalles de la autenticación

La autenticación de Azure AD se proporciona a los clústeres de AKS con OpenID Connect. OpenID Connect es una capa de identidad creada basándose en el protocolo OAuth 2.0. Puede encontrar más información sobre OpenID Connect en la [documentación de OpenID Connect][open-id-connect].

Dentro del clúster de Kubernetes, se usa la autenticación de token de webhook para verificar los tokens de autenticación. La autenticación de token de webhook se configura y administra como parte del clúster de AKS. Puede encontrar más información sobre la autenticación de token de webhook en la [documentación de autenticación de webhook][kubernetes-webhook].

> [!NOTE]
> Al configurar Azure AD para la autenticación de AKS, se configuran dos aplicaciones de Azure AD. Un administrador del inquilino de Azure debe realizar esta operación.

## <a name="create-server-application"></a>Creación de la aplicación de servidor

La primera aplicación de Azure AD se utiliza para obtener la pertenencia a un grupo de Azure AD de los usuarios.

1. Seleccione **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

   Asigne un nombre a la aplicación, seleccione **Aplicación web / API** como tipo de aplicación y escriba cualquier valor con formato de URI para la **Dirección URL de inicio de sesión**. Seleccione **Crear** cuando haya terminado.

   ![Creación del registro de Azure AD](media/aad-integration/app-registration.png)

2. Seleccione **Manifiesto** y establezca el valor de `groupMembershipClaims` en `"All"`.

   Guarde las actualizaciones una vez completadas.

   ![Actualizar la pertenencia al grupo a All (Todos)](media/aad-integration/edit-manifest.png)

3. En la aplicación de Azure AD, seleccione **Configuración** > **Claves**.

   Agregue una descripción de la clave, seleccione una fecha límite de expiración y seleccione **Guardar**. Anote el valor de la clave. Al implementar un clúster de AKS habilitado para Azure AD, este valor se conoce como el `Server application secret`.

   ![Obtención de la clave privada de la aplicación](media/aad-integration/application-key.png)

4. Vuelva a la aplicación de Azure AD y seleccione **Configuración** > **Permisos necesarios** > **Agregar** >  **Seleccione una API** > **Microsoft Graph** > **Seleccionar**.

   ![Selección de la API de grafo](media/aad-integration/graph-api.png)

5. En **Permisos de aplicación**, active la marca de verificación junto a **Leer datos del directorio**.

   ![Establecimiento de los permisos de Graph de la aplicación](media/aad-integration/read-directory.png)

6. En **Permisos delegados**, active la marca de verificación junto a **Iniciar sesión y leer el perfil de usuario** y **Leer datos del directorio**. Guarde las actualizaciones cuando haya terminado.

   ![Establecimiento de los permisos de Graph de la aplicación](media/aad-integration/delegated-permissions.png)

   Seleccione **Listo**.

7. Elija *Microsoft Graph* en la lista de las API y, a continuación, seleccione **Conceder permisos**. Se producirá un error en este paso si la cuenta actual no es un administrador del inquilino.

   ![Establecimiento de los permisos de Graph de la aplicación](media/aad-integration/grant-permissions.png)

   Cuando se han concedido los permisos correctamente, la siguiente notificación se muestra en el portal:

   ![Notificación de concesión de permisos correcta](media/aad-integration/permissions-granted.png)

8. Vuelva a la aplicación y tome nota del **Identificador de la aplicación**. Al implementar un clúster de AKS habilitado para Azure AD, este valor se conoce como el `Server application ID`.

   ![Obtención del identificador de la aplicación](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Creación de la aplicación cliente

La segunda aplicación de Azure AD se usa cuando inicia sesión con la CLI de Kubernetes (kubectl).

1. Seleccione **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

   Asigne un nombre a la aplicación, seleccione **Nativa** como tipo de aplicación y escriba cualquier valor con formato de URI para la **Dirección URI de redirección**. Seleccione **Crear** cuando haya terminado.

   ![Creación del registro de AAD](media/aad-integration/app-registration-client.png)

2. En la aplicación de Azure AD, seleccione **Configuración** > **Permisos necesarios** > **Agregar** > **Seleccionar una API** y busque el nombre de la aplicación de servidor que creó en el último paso de este documento.

   ![Configuración de los permisos de la aplicación](media/aad-integration/select-api.png)

3. Active la marca de verificación junto a la aplicación y haga clic en **Seleccionar**.

   ![Selección del punto de conexión de la aplicación de servidor de AAD de AKS](media/aad-integration/select-server-app.png)

   Seleccione **Listo**

4. Seleccione la API de servidor en la lista y, a continuación, elija **Conceder permisos**:

   ![Concesión de permisos](media/aad-integration/grant-permissions-client.png)

5. Vuelva a la aplicación de AD y tome nota del **Identificador de la aplicación**. Al implementar un clúster de AKS habilitado para Azure AD, este valor se conoce como el `Client application ID`.

   ![Obtención del identificador de la aplicación](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obtención del identificador de inquilino

Por último, obtenga el identificador de su inquilino de Azure. Este valor también se usa al implementar el clúster de AKS.

En Azure Portal, seleccione **Azure Active Directory** > **Propiedades** y tome nota del **Identificador de directorio**. Al implementar un clúster de AKS habilitado para Azure AD, este valor se conoce como el `Tenant ID`.

![Obtención del identificador de inquilino de Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Implementación del clúster

Para crear un grupo de recursos para el clúster de AKS, use el comando [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Implemente el clúster con el comando [az aks create][az-aks-create]. Reemplace los valores del comando de ejemplo siguiente por los valores recopilados al crear las aplicaciones de Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Creación del enlace de RBAC

Para poder usar una cuenta de Azure Active Directory con el clúster AKS, debe crear un enlace de rol o un enlace de rol del clúster. *Roles* define los permisos para conceder, y *enlaces* los aplica a los usuarios deseados. Estas asignaciones se pueden aplicar a un espacio de nombres especificado o a todo el clúster. Para más información, consulte [Uso de la autorización de RBAC][rbac-authorization].

En primer lugar, use el [az aks get-credentials] [ az-aks-get-credentials] comando con el `--admin` argumento para iniciar sesión en el clúster con acceso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

A continuación, cree un ClusterRoleBinding para una cuenta de Azure AD que desea conceder acceso al clúster de AKS. En el ejemplo siguiente se proporciona la cuenta de acceso completo a todos los espacios de nombres en el clúster.

- Si el usuario concede que el RBAC el enlace está en el mismo inquilino de Azure AD, asignar permisos basados en el nombre principal de usuario (UPN). Pasar al paso para crear el manifiesto YAML para el ClusterRuleBinding.

- Si el usuario está en un Azure AD diferente de inquilinos, consultar y usar el *objectId* propiedad en su lugar. Si es necesario, obtenga el *objectId* del usuario requiere cuenta mediante la [show de usuario de ad az] [ az-ad-user-show] comando. Proporcione el nombre principal de usuario (UPN) de la cuenta necesaria:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Cree un archivo, como *rbac-aad-user.yaml* y pegue el siguiente contenido. En la última línea, reemplace *userPrincipalName_or_objectId* con el identificador de objeto o UPN dependiendo de si el usuario es el mismo inquilino de Azure AD o no.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Cree el enlace con el comando [kubectl apply][kubectl-apply], tal como se muestra en el ejemplo siguiente:

```console
kubectl apply -f rbac-aad-user.yaml
```

También se puede crear un enlace de rol para todos los miembros de un grupo de Azure AD. Los grupos de Azure AD se especifican mediante el identificador de objeto de grupo, tal y como se muestra en el siguiente ejemplo. Cree un archivo, como *rbac-aad-group.yaml* y pegue el siguiente contenido. Actualice el identificador del objeto de grupo con uno de su inquilino de Azure AD:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Cree el enlace con el comando [kubectl apply][kubectl-apply], tal como se muestra en el ejemplo siguiente:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para más información sobre cómo proteger un clúster de Kubernetes con RBAC, consulte [Uso de la autorización de RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Acceso al clúster con Azure AD

A continuación, extraiga el contexto para el usuario sin derechos administrativos mediante el comando [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Después de ejecutar cualquier comando de kubectl, se le pedirá que se autentique en Azure. Siga las instrucciones que aparecen en pantalla.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Una vez finalizado, el token de autenticación se almacena en caché. Solo introducir de nuevo para iniciar sesión cuando el token ha expirado o vuelve a crear el archivo de configuración de Kubernetes.

Si ve un mensaje de error de autorización después de iniciar sesión correctamente, compruebe que:
1. El usuario con el que inicia sesión no sea un invitado en una instancia de Azure AD (este suele ser el caso, si está utilizando un inicio de sesión federado desde un directorio diferente).
2. El usuario no es miembro de más de 200 grupos.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Pasos siguientes

Para usar Azure AD usuarios y grupos para controlar el acceso a los recursos de clúster, consulte [controlar el acceso a recursos de clúster mediante el control de acceso basado en roles y las identidades de Azure AD en AKS][azure-ad-rbac].

Para obtener más información acerca de cómo proteger los clústeres de Kubernetes, consulte [opciones de identidad y acceso para AKS)][rbac-authorization].

Para ver recomendaciones de control de identidades y recursos, consulte [procedimientos recomendados para la autenticación y autorización en AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
