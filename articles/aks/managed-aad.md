---
title: Azure Kubernetes Service에서 Azure AD 사용
description: AKS(Azure Kubernetes Service)에서 Azure AD를 사용하는 방법 알아보기
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211146"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에 Azure AD 통합(미리 보기)

> [!Note]
> AAD(Azure Active Directory)와의 기존 AKS 클러스터 통합은 AKS로 관리되는 새로운 AAD 환경의 영향을 받지 않습니다.

AKS로 관리되는 AAD와의 Azure AD 통합은 Azure AD 통합 환경을 간소화하도록 설계되었습니다. 이전에는 사용자가 클라이언트 앱, 서버 앱을 만들어야 했고, 사용자에게 디렉터리 읽기 권한을 부여하는 Azure AD 테넌트가 필요했습니다. 새 버전에서는 AKS 리소스 공급자가 클라이언트 및 서버 앱을 자동으로 관리합니다.

## <a name="limitations"></a>제한 사항

* 현재 기존 AKS AAD 통합 클러스터를 AKS로 관리되는 새로운 AAD 환경으로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스에서 사용할 수 있습니다(옵트인 방식). 미리 보기는 "있는 그대로" "사용 가능한 상태로" 제공되며, 서비스 수준 계약 및 제한적 보증에서 제외됩니다. AKS 미리 보기의 일부는 고객 지원팀에서 최선을 다해 지원합니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조하세요.
>
> - [AKS 지원 정책](support-policies.md)
> - [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전에

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

Kubectl을 설치하려면 다음을 사용합니다.

```azurecli
sudo az aks install-cli
kubectl version --client
```

다른 운영 체제에 대한 [지침](https://kubernetes.io/docs/tasks/tools/install-kubectl/)을 사용합니다.

> [!CAUTION]
> 현재는 구독에 기능을 등록한 후 해당 기능의 등록을 취소할 수 없습니다. 일부 미리 보기 기능을 사용하도록 설정하면 구독에서 이후에 생성되는 모든 AKS 클러스터에 기본값이 사용될 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마세요. 대신 별도의 구독을 사용하여 미리 보기 기능을 테스트하고 피드백을 수집하세요.

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

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD를 사용하는 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용하여 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

그런 다음, AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
위의 명령은 3개의 노드 AKS 클러스터를 만들지만 클러스터를 만든 사용자는 기본적으로 이 클러스터에 대한 액세스 권한이 있는 그룹의 구성원이 아닙니다. 이 사용자는 Azure AD 그룹을 만들고, 자신을 그룹의 구성원으로 추가하고, 아래와 같이 클러스터를 업데이트해야 합니다. [여기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)에 나와 있는 지침을 따르세요.

그룹을 만들고 사용자(및 다른 사용자)를 구성원으로 추가한 후에는 다음 명령을 사용하여 Azure AD 그룹으로 클러스터를 업데이트할 수 있습니다.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
또는 그룹을 먼저 만들고 구성원을 추가하는 경우 그룹을 만들 때 다음 명령을 사용하여 Azure AD 그룹을 사용하도록 설정할 수 있습니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

AKS로 관리되는 AAD 클러스터가 성공적으로 생성되면 응답 본문에 다음 섹션이 포함됩니다.
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
클러스터에 액세스하기 위한 관리자 자격 증명을 가져오려면 다음을 수행합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
이제 kubectl get nodes 명령을 사용하여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

클러스터에 액세스하는 데 사용할 사용자 자격 증명을 가져오려면 다음을 수행합니다.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
지침에 따라 로그인합니다.

다음과 같은 메시지가 수신됩니다. **서버에 로그인해야 합니다(권한 없음).**

위의 사용자는 클러스터에 대한 액세스 권한이 있는 그룹에 속해 있지 않으므로 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 역할 기반 액세스 제어][azure-ad-rbac]에 대해 알아봅니다.
* [kubelogin](https://github.com/Azure/kubelogin)을 사용하여 kubectl에서 사용할 수 없는 Azure 인증 기능에 액세스합니다.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
