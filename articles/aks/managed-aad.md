---
title: Azure Kubernetes Service에서 Azure AD 사용
description: AKS(Azure Kubernetes Service)에서 Azure AD를 사용하는 방법 알아보기
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 896986775f0132ef08b17bdfefc00e5e06cf3d9f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448140"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS 관리 Azure Active Directory 통합

AKS로 관리 되는 Azure ad 통합은 사용자가 이전에 클라이언트 앱, 서버 앱을 만들고 디렉터리 읽기 권한을 부여 하는 데 필요한 azure ad 통합 환경을 간소화 하도록 설계 되었습니다. 새 버전에서는 AKS 리소스 공급자가 클라이언트 및 서버 앱을 자동으로 관리합니다.

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

클러스터 관리자는 사용자의 id 또는 디렉터리 그룹 멤버 자격을 기반으로 Kubernetes RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다. OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

[Azure Active Directory 통합 개념 설명서](concepts-identity.md#azure-active-directory-integration)의 AAD 통합 흐름에 대해 자세히 알아보세요.

## <a name="region-availability"></a>지역 가용성

AKS 관리 Azure Active Directory 통합은 [AKS가 지원](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)되는 공용 지역에서 사용할 수 있습니다.

* Azure Government 현재 지원 되지 않습니다.
* Azure 중국 21Vianet은 현재 지원 되지 않습니다.

## <a name="limitations"></a>제한 사항 

* AKS에서 관리 되는 Azure AD 통합을 사용 하지 않도록 설정할 수 없음
* 비 RBAC 사용 클러스터는 AKS 관리 AAD 통합에 대해 지원 되지 않습니다.
* AKS에서 관리 하는 AAD 통합과 관련 된 Azure AD 테 넌 트 변경은 지원 되지 않음

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스에서 사용할 수 있습니다(옵트인 방식). 미리 보기는 "있는 그대로" "사용 가능한 상태로" 제공되며, 서비스 수준 계약 및 제한적 보증에서 제외됩니다. AKS 미리 보기의 일부는 고객 지원팀에서 최선을 다해 지원합니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조하세요. 
> - [AKS 지원 정책](support-policies.md) 
> - [Azure 지원 FAQ](faq.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.9.0 이상
* Kubectl [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) 이상 버전

> [!Important]
> 최소 1.18 버전의 Kubectl을 사용해야 합니다.

Kubectl을 설치 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

다른 운영 체제에 대한 [지침](https://kubernetes.io/docs/tasks/tools/install-kubectl/)을 사용합니다.

```azurecli-interactive 
az feature register --name AAD-V2 --namespace Microsoft.ContainerService    
``` 

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다. 

```azurecli-interactive 
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"    
``` 

상태가 등록됨으로 표시되면 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.    

```azurecli-interactive 
az provider register --namespace Microsoft.ContainerService 
``` 


## <a name="before-you-begin"></a>시작하기 전에

클러스터의 경우 Azure AD 그룹이 필요 합니다. 이 그룹은 클러스터 관리자 권한을 부여 하기 위해 클러스터에 대 한 관리 그룹으로 필요 합니다. 기존 Azure AD 그룹을 사용 하거나 새 그룹을 만들 수 있습니다. Azure AD 그룹의 개체 ID를 기록 합니다.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

클러스터 관리자에 대 한 새 Azure AD 그룹을 만들려면 다음 명령을 사용 합니다.

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD를 사용하는 AKS 클러스터 만들기

다음 CLI 명령을 사용 하 여 AKS 클러스터를 만듭니다.

Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AKS 클러스터를 만들고 Azure AD 그룹에 대 한 관리 액세스를 사용 하도록 설정 합니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS로 관리 되는 Azure AD 클러스터가 성공적으로 만들어지면 응답 본문에 다음 섹션이 포함 됩니다.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

클러스터를 만든 후에는 해당 클러스터에 대 한 액세스를 시작할 수 있습니다.

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD를 사용하는 클러스터에 액세스

다음 단계를 수행 하려면 [Azure Kubernetes Service 클러스터 사용자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 기본 제공 역할이 필요 합니다.

클러스터에 액세스 하기 위한 사용자 자격 증명을 가져옵니다.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
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
Azure [RBAC (역할 기반 액세스 제어)](./azure-ad-rbac.md) 를 구성 하 여 클러스터에 대 한 추가 보안 그룹을 구성 합니다.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD의 액세스 문제 해결

> [!Important]
> 아래에 설명 된 단계는 일반 Azure AD 그룹 인증을 무시 합니다. 응급 상황 에서만 사용 합니다.

클러스터에 대 한 액세스 권한이 있는 유효한 Azure AD 그룹에 액세스 하지 않아 영구적으로 차단 된 경우에도 관리자 자격 증명을 가져와 클러스터에 직접 액세스할 수 있습니다.

이러한 단계를 수행 하려면 [Azure Kubernetes Service 클러스터 관리자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) 기본 제공 역할에 대 한 액세스 권한이 있어야 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>AKS로 관리 되는 Azure AD 통합으로 업그레이드

클러스터에서 레거시 Azure AD 통합을 사용 하는 경우 AKS로 관리 되는 Azure AD 통합으로 업그레이드할 수 있습니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS로 관리 되는 Azure AD 클러스터의 성공적인 마이그레이션에는 응답 본문에 다음과 같은 섹션이 있습니다.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

클러스터에 액세스 하려면 [다음][access-cluster]단계를 수행 합니다.

## <a name="non-interactive-sign-in-with-kubelogin"></a>Kubelogin를 사용 하 여 비 대화형 로그인

현재 kubectl에서 사용할 수 없는 지속적인 통합 파이프라인과 같은 일부 비 대화형 시나리오가 있습니다. [`kubelogin`](https://github.com/Azure/kubelogin)를 사용 하 여 비 대화형 서비스 사용자 로그인으로 클러스터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 권한 부여에 대 한 AZURE RBAC 통합][azure-rbac-integration] 에 대해 알아보기
* [KUBERNETES RBAC와 AZURE AD 통합][azure-ad-rbac]에 대해 알아봅니다.
* [Kubelogin](https://github.com/Azure/kubelogin) 를 사용 하 여 kubectl에서 사용할 수 없는 Azure 인증 기능에 액세스 합니다.
* [AKS 및 Kubernetes id 개념][aks-concepts-identity]에 대해 자세히 알아보세요.
* [ARM (Azure Resource Manager) 템플릿을][aks-arm-template] 사용 하 여 AKS로 관리 되는 Azure AD 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
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
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
