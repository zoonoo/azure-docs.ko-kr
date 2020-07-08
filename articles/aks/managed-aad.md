---
title: Azure Kubernetes Service에서 Azure AD 사용
description: AKS(Azure Kubernetes Service)에서 Azure AD를 사용하는 방법 알아보기
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: bf635d37559d09e887a67be27c412bff7899127b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023400"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>AKS로 관리 되는 Azure AD 통합 (미리 보기)

> [!Note]
> 기존 AKS (azure Kubernetes Service) Azure Active Directory (Azure AD)를 통합 하는 클러스터는 새로운 AKS 관리 Azure AD 환경의 영향을 받지 않습니다.

AKS로 관리 되는 azure AD와의 azure AD 통합은 사용자가 이전에 클라이언트 앱, 서버 앱을 만들고 디렉터리 읽기 권한을 부여 하는 데 필요한 Azure ad 통합 환경을 간소화 하도록 설계 되었습니다. 새 버전에서는 AKS 리소스 공급자가 클라이언트 및 서버 앱을 자동으로 관리합니다.

## <a name="limitations"></a>제한 사항

* 현재 기존 AKS Azure AD 통합 클러스터를 새로운 AKS로 관리 되는 Azure AD 환경으로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스에서 사용할 수 있습니다(옵트인 방식). 미리 보기는 "있는 그대로" "사용 가능한 상태로" 제공되며, 서비스 수준 계약 및 제한적 보증에서 제외됩니다. AKS 미리 보기의 일부는 고객 지원팀에서 최선을 다해 지원합니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조하세요.
>
> - [AKS 지원 정책](support-policies.md)
> - [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전에

* Azure Portal로 이동 하 여 Azure 계정 테 넌 트 ID를 찾고 Azure Active Directory > 속성 > 디렉터리 ID를 선택 합니다.

> [!Important]
> 최소 1.18 버전의 Kubectl을 사용해야 합니다.

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.5.1 이상
- aks-preview 0.4.38 확장
- Kubectl [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) 이상 버전

aks-preview 확장 이상을 설치/업데이트하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Kubectl을 설치 하려면 다음 명령을 사용 합니다.

```azurecli
sudo az aks install-cli
kubectl version --client
```

다른 운영 체제에 대한 [지침](https://kubernetes.io/docs/tasks/tools/install-kubectl/)을 사용합니다.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

클러스터 관리자는 사용자의 id 또는 디렉터리 그룹 멤버 자격을 기반으로 Kubernetes RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다. OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

## <a name="webhook-and-api-server"></a>웹 후크 및 API 서버

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="웹 후크 및 API 서버 인증 흐름":::

위의 그림에 표시 된 것 처럼 API 서버는 AKS webhook 서버를 호출 하 고 다음 단계를 수행 합니다.

1. Azure AD 클라이언트 응용 프로그램은 kubectl에서 [OAuth 2.0 장치 권한 부여 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)을 사용 하 여 사용자를 로그인 하는 데 사용 됩니다.
2. Azure AD는 access_token, id_token 및 refresh_token를 제공 합니다.
3. 사용자가 kubeconfig의 access_token를 사용 하 여 kubectl에 대 한 요청을 만듭니다.
4. Kubectl는 APIServer에 access_token를 보냅니다.
5. API 서버는 유효성 검사를 수행 하기 위해 Auth WebHook 서버를 사용 하 여 구성 됩니다.
6. 인증 webhook 서버는 Azure AD 공개 서명 키를 확인 하 여 JSON Web Token 서명이 유효한 지 확인 합니다.
7. 서버 응용 프로그램은 사용자 제공 자격 증명을 사용 하 여 MS Graph API에서 로그인 한 사용자의 그룹 멤버 자격을 쿼리 합니다.
8. 응답은 액세스 토큰의 UPN (사용자 계정 이름) 클레임 및 개체 ID를 기반으로 하는 사용자의 그룹 멤버 자격과 같은 사용자 정보를 사용 하 여 APIServer에 전송 됩니다.
9. API는 Kubernetes Role/RoleBinding에 따라 권한 부여 결정을 수행 합니다.
10. 권한이 부여 되 면 API 서버는 kubectl에 대 한 응답을 반환 합니다.
11. Kubectl 사용자에 게 피드백을 제공 합니다.


## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD를 사용하는 AKS 클러스터 만들기

다음 CLI 명령을 사용 하 여 AKS 클러스터를 만듭니다.

Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

기존 Azure AD 그룹을 사용 하거나 새 그룹을 만들 수 있습니다. Azure AD 그룹의 개체 ID가 필요 합니다.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

클러스터 관리자에 대 한 새 Azure AD 그룹을 r) 다음 명령을 사용 합니다.

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

AKS 클러스터를 만들고 Azure AD 그룹에 대 한 관리 액세스를 사용 하도록 설정 합니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

AKS로 관리 되는 Azure AD 클러스터가 성공적으로 만들어지면 응답 본문에 다음 섹션이 포함 됩니다.
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

클러스터는 몇 분 내에 만들어집니다.

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD를 사용하는 클러스터에 액세스

다음 단계를 수행 하려면 [Azure Kubernetes Service 클러스터 사용자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) 기본 제공 역할이 필요 합니다.

클러스터에 액세스 하기 위한 사용자 자격 증명을 가져옵니다.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
지침에 따라 로그인합니다.

Kubectl get nodes 명령을 사용 하 여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[RBAC (역할 기반 Access Control)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) 를 구성 하 여 클러스터에 대 한 추가 보안 그룹을 구성 합니다.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD의 액세스 문제 해결

> [!Important]
> 아래에 설명 된 단계는 일반 Azure AD 그룹 인증을 무시 합니다. 응급 상황 에서만 사용 합니다.

클러스터에 대 한 액세스 권한이 있는 유효한 Azure AD 그룹에 액세스 하지 않아 영구적으로 차단 된 경우에도 관리자 자격 증명을 가져와 클러스터에 직접 액세스할 수 있습니다.

이러한 단계를 수행 하려면 [Azure Kubernetes Service 클러스터 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) 기본 제공 역할에 대 한 액세스 권한이 있어야 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>Kubelogin를 사용 하는 비 대화형 로그인

연속 통합 파이프라인과 같이 현재 kubectl에서 사용할 수 없는 일부 비 대화형 시나리오가 있습니다. [Kubelogin](https://github.com/Azure/kubelogin) 를 사용 하 여 비 대화형 서비스 사용자 로그인으로 클러스터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 역할 기반 액세스 제어][azure-ad-rbac]에 대해 알아봅니다.
* [kubelogin](https://github.com/Azure/kubelogin)을 사용하여 kubectl에서 사용할 수 없는 Azure 인증 기능에 액세스합니다.
* [ARM (Azure Resource Manager) 템플릿을][aks-arm-template] 사용 하 여 AKS로 관리 되는 Azure AD 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

