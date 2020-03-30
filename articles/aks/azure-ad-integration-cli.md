---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure CLI를 사용하여 Azure Active Directory 지원 Azure Kubernetes 서비스(AKS) 클러스터를 만들고 만드는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253053"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Active Directory를 Azure Kubernetes 서비스와 통합

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인할 수 있습니다. 클러스터 운영자는 사용자의 ID 또는 디렉터리 그룹 구성원 자격에 따라 Kubernetes 역할 기반 액세스 제어(RBAC)를 구성할 수도 있습니다.

이 문서에서는 필요한 Azure AD 구성 요소를 만든 다음 Azure AD 지원 클러스터를 배포하고 AKS 클러스터에서 기본 RBAC 역할을 만드는 방법을 보여 주며 이 문서에서는 Azure 포털 을 [사용하여 이러한 단계를 완료할][azure-ad-portal]수도 있습니다.

이 문서에서 사용되는 전체 샘플 스크립트는 [Azure CLI 샘플 - Azure AD와의 AKS 통합을][complete-script]참조하십시오.

다음 제한 사항이 적용됩니다.

- Azure AD는 RBAC 사용 클러스터를 새로 만들 때만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하십시오.

브라우저에서 [https://shell.azure.com](https://shell.azure.com) 클라우드 셸을 엽니다.

일관성을 위해 이 문서에서 명령을 실행하는 데 도움이 되도록 원하는 AKS 클러스터 이름에 대한 변수를 만듭니다. 다음 예제에서는 *myakscluster*라는 이름을 사용합니다.

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션을 구성합니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다.

## <a name="create-azure-ad-server-component"></a>Azure AD 서버 구성 요소 만들기

AKS와 통합하려면 ID 요청의 끝점 역할을 하는 Azure AD 응용 프로그램을 만들고 사용합니다. 필요한 첫 번째 Azure AD 응용 프로그램에는 사용자에 대한 Azure AD 그룹 구성원이 가져옵니다.

[az 광고 앱 create][az-ad-app-create] 명령을 사용하여 서버 응용 프로그램 구성 요소를 만든 다음 az 광고 앱 [업데이트][az-ad-app-update] 명령을 사용하여 그룹 구성원 클레임을 업데이트합니다. 다음 예제에서는 [시작하기 전](#before-you-begin) 단면에 정의된 *aksname* 변수를 사용하고 변수를 만듭니다.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

이제 [az 광고 sp 만들기][az-ad-sp-create] 명령을 사용하여 서버 앱에 대한 서비스 주체를 만듭니다. 이 서비스 주체는 Azure 플랫폼 내에서 자체를 인증하는 데 사용됩니다. 그런 다음 [az 광고 sp 자격 증명 재설정][az-ad-sp-credential-reset] 명령을 사용하여 서비스 주 서버 비밀을 얻고 다음 단계 중 하나에서 사용할 수 있는 *서버ApplicationSecret라는* 변수에 할당합니다.

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD에는 다음 작업을 수행할 수 있는 권한이 필요합니다.

* 디렉터리 데이터 읽기
* 로그인 및 사용자 프로필 읽기

[az 광고 앱 권한 추가][az-ad-app-permission-add] 명령을 사용하여 이러한 권한을 할당합니다.

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

마지막으로 [az 광고 앱 권한 부여][az-ad-app-permission-grant] 명령을 사용하여 서버 응용 프로그램에 대해 이전 단계에서 할당된 권한을 부여합니다. 현재 계정이 테넌트 관리자가 아닌 경우 이 단계가 실패합니다. 또한 [az 광고 앱 권한 관리자][az-ad-app-permission-admin-consent]동의를 사용하여 관리 동의가 필요할 수 있는 정보를 요청하기 위해 Azure AD 응용 프로그램에 대한 권한을 추가해야 합니다.

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD 클라이언트 구성 요소 만들기

두 번째 Azure AD 응용 프로그램은 사용자가 Kubernetes CLI()를`kubectl`사용하여 AKS 클러스터에 로그할 때 사용됩니다. 이 클라이언트 응용 프로그램은 사용자의 인증 요청을 받고 자격 증명 및 사용 권한을 확인합니다. az 광고 앱 create 명령을 사용하여 클라이언트 구성 요소에 대한 Azure [AD 앱을 만듭니다.][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

[az 광고 sp create][az-ad-sp-create] 명령을 사용하여 클라이언트 응용 프로그램에 대한 서비스 주체를 만듭니다.

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

[az 광고][az-ad-app-show] 앱 표시 명령을 사용하여 두 앱 구성 요소 간의 인증 흐름을 허용하도록 서버 앱에 대한 oAuth2 ID를 가져옵니다. 이 oAuth2 ID는 다음 단계에서 사용됩니다.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

[az 광고 앱][az-ad-app-permission-add] 권한 추가 명령을 사용하여 oAuth2 통신 흐름을 사용하도록 클라이언트 응용 프로그램 및 서버 응용 프로그램 구성 요소에 대한 권한을 추가합니다. 그런 다음 [az 광고 앱 권한 부여][az-ad-app-permission-grant] 명령을 사용하여 클라이언트 응용 프로그램에 대한 권한을 서버 응용 프로그램과 통신할 수 있도록 허용합니다.

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>클러스터 배포

두 개의 Azure AD 응용 프로그램을 만든 다음 이제 AKS 클러스터 자체를 만듭니다. 먼저 az 그룹을 사용하여 리소스 그룹을 [만듭니다.][az-group-create] 다음 예제에서는 *EastUS* 지역에서 리소스 그룹을 만듭니다.

클러스터에 대한 리소스 그룹 만들기:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

[az 계정 표시][az-account-show] 명령을 사용하여 Azure 구독의 테넌트 ID를 가져옵니다. 그런 다음 az aks create 명령을 사용하여 [AKS 클러스터를 만듭니다.][az-aks-create] AKS 클러스터를 만드는 명령은 서버 및 클라이언트 응용 프로그램 ID, 서버 응용 프로그램 서비스 주 보안 및 테넌트 ID를 제공합니다.

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

마지막으로 [az aks get-CREDENTIALs][az-aks-get-credentials] 명령을 사용하여 클러스터 관리자 자격 증명을 가져옵니다. 다음 단계 중 하나에서일반 *사용자* 클러스터 자격 증명을 사용하여 Azure AD 인증 흐름이 작동하는 지 확인할 수 있습니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할*은 부여할 사용 권한을 정의하고, *바인딩*은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

[az 광고 로그인 사용자 표시][az-ad-signed-in-user-show] 명령을 사용하여 현재 로그인한 사용자의 UPN(사용자 주체 이름)을 가져옵니다. 이 사용자 계정은 다음 단계에서 Azure AD 통합에 대해 활성화됩니다.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> RBAC 바인딩을 부여하는 사용자가 동일한 Azure AD 테넌트에 있는 경우 *userPrincipalName*에 따라 사용 권한을 할당합니다. 사용자가 다른 Azure AD 테넌트에 있는 경우 *대신 objectId* 속성을 쿼리하고 사용합니다.

YAML 매니페스트를 `basic-azure-ad-binding.yaml` 만들고 다음 내용을 붙여넣습니다. 마지막 줄에서 *userPrincipalName_or_objectId* 이전 명령의 UPN 또는 개체 ID 출력으로 바꿉꿉습니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

이제 AKS 클러스터에 대한 Azure AD 인증의 통합을 테스트해 보겠습니다. 구성 `kubectl` 컨텍스트를 설정하여 일반 사용자 자격 증명을 사용합니다. 이 컨텍스트는 Azure AD를 통해 모든 인증 요청을 다시 전달합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

이제 [kubectl get pods][kubectl-get] 명령을 사용하여 모든 네임스페이스에서 창을 봅니다.

```console
kubectl get pods --all-namespaces
```

웹 브라우저를 사용하여 Azure AD 자격 증명을 사용하여 인증하라는 로그인 프롬프트가 표시됩니다. 성공적으로 인증한 후 `kubectl` 다음 예제 출력과 같이 AKS 클러스터의 포드가 표시됩니다.

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

수신된 인증 `kubectl` 토큰이 캐시됩니다. 토큰이 만료되었거나 Kubernetes 구성 파일이 다시 생성될 때만 로그인하라는 메시지가 다시 표시됩니다.

다음 예제 출력과 같이 웹 브라우저를 사용하여 성공적으로 로그인한 후 권한 부여 오류 메시지가 표시되면 다음과 같은 가능한 문제를 확인하십시오.

```output
error: You must be logged in to the server (Unauthorized)
```

* 사용자 계정이 동일한 Azure AD 테넌트에 있는지 여부에 따라 적절한 개체 ID 또는 UPN을 정의했습니다.
* 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.
* 서버에 대한 응용 프로그램 등록에 정의된 비밀이 사용하여 구성된 값과 일치합니다.`--aad-server-app-secret`

## <a name="next-steps"></a>다음 단계

이 문서에 표시된 명령이 포함된 전체 스크립트는 [AKS 샘플 리포지토리의 Azure AD 통합 스크립트를][complete-script]참조하십시오.

Azure AD 사용자 및 그룹을 사용하여 클러스터 리소스에 대한 액세스를 제어하려면 [AKS의 역할 기반 액세스 제어 및 Azure AD ID를 사용하여 클러스터 리소스에][azure-ad-rbac]대한 액세스 제어를 참조하세요.

Kubernetes 클러스터를 보호하는 방법에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션을 참조하십시오.][rbac-authorization]

ID 및 리소스 제어에 대한 모범 사례는 [AKS의 인증 및 권한 부여에 대한 모범 사례를][operator-best-practices-identity]참조하십시오.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
