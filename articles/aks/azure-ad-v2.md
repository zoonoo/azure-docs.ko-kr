---
title: Azure Kubernetes 서비스에서 Azure AD 사용
description: AKS(Azure Kubernetes 서비스)에서 Azure AD를 사용하는 방법에 대해 알아봅니다.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 430df504c677b005f5ff5e7fdd9346aed3e168af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294448"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Azure Kubernetes 서비스에 Azure AD 통합(미리 보기)

> [!Note]
> AD 통합이 있는 기존 AKS v1 클러스터는 새 AKS v2 환경의 영향을 받지 않습니다.

AKS v2와의 Azure AD 통합은 사용자가 클라이언트 앱, 서버 앱을 만들어야 하고 Azure AD 테넌트에 디렉터리 읽기 권한을 부여해야 하는 AKS v1 환경과의 Azure AD 통합을 단순화하도록 설계되었습니다. 새 버전에서는 AKS 리소스 공급자가 클라이언트 및 서버 앱을 관리합니다.

## <a name="limitations"></a>제한 사항

* 현재 기존 Azure AD 지원 AKS v1 클러스터를 v2 환경으로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스, 옵트인 기준으로 사용할 수 있습니다. 미리 보기는 "있는 대로" 및 "사용 가능한 경우"로 제공되며 서비스 수준 계약 및 제한 보증에서 제외됩니다. AKS 미리 보기는 고객 지원에 의해 부분적으로 최선의 노력을 다할 때 다룹니다. 따라서 이러한 기능은 프로덕션 용으로 사용되지 않습니다. 자세한 내용은 다음 지원 문서를 참조하십시오.
>
> - [AKS 지원 정책](support-policies.md)
> - [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI, 버전 2.2.0 이상
- aks 미리 보기 0.4.38 확장
- [1.18 베타의](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries) 최소 버전과 Kubectl

aks 미리 보기 확장 또는 나중에 설치/업데이트하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

kubectl을 설치하려면 다음을 사용하여
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

다른 운영 체제에 는 [다음 지침을](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 사용합니다.

> [!CAUTION]
> 구독에 기능을 등록한 후에는 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용하도록 설정하면 구독에서 나중에 만든 모든 AKS 클러스터에 기본값이 사용될 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마십시오. 대신 별도의 구독을 사용하여 미리 보기 기능을 테스트하고 피드백을 수집합니다.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

상태가 **등록됨으로**표시되는 데 몇 분 정도 걸릴 수 있습니다. [az 기능 목록](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

상태가 등록된 것으로 표시되면 az 공급자 `Microsoft.ContainerService` 레지스터 명령을 사용하여 리소스 [공급자의](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD를 사용하도록 설정한 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용하여 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

그런 다음 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
위의 명령은 세 개의 노드 AKS 클러스터를 생성하지만 기본적으로 클러스터를 만든 사용자는 이 클러스터에 액세스할 수 있는 그룹의 구성원이 아닙니다. 이 사용자는 Azure AD 그룹을 만들고, 자신을 그룹의 구성원으로 추가한 다음 아래와 같이 클러스터를 업데이트해야 합니다. [여기에](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 지침을 따르십시오

그룹을 만들고 사용자(및 기타)를 구성원으로 추가한 후에는 다음 명령을 사용하여 Azure AD 그룹으로 클러스터를 업데이트할 수 있습니다.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
또는 먼저 그룹을 만들고 구성원을 추가하는 경우 다음 명령을 사용하여 만들 때 Azure AD 그룹을 활성화할 수 있습니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Azure AD v2 클러스터를 성공적으로 만들면 응답 본문에 다음 섹션이 있습니다.
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

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD 지원 클러스터 액세스
클러스터에 액세스하는 관리자 자격 증명을 얻으려면 다음을 수행하십시오.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
이제 kubectl get 노드 명령을 사용하여 클러스터의 노드를 봅니다.

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

클러스터에 액세스하는 사용자 자격 증명을 얻으려면 다음을 수행하십시오.
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
지침에 따라 로그인합니다.

수신: **서버에 로그인해야 합니다(무단)**

위의 사용자는 클러스터에 액세스할 수 있는 그룹의 일부가 아니기 때문에 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure [AD 역할 기반 액세스 제어에][azure-ad-rbac]대해 자세히 알아보기.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md