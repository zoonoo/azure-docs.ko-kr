---
title: Azure에서 Kubernetes의 RBAC 관리
titleSuffix: Azure Kubernetes Service
description: AKS (Azure Kubernetes Service)에서 Kubernetes 권한 부여를 위해 Azure RBAC를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/20/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c1222f671c95d4475de93b9c9e085a94f864b2ae
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003082"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes 권한 부여를 위해 Azure RBAC 사용(미리 보기)

현재 [Azure Active Directory (AZURE AD)와 AKS 간에 통합 인증](managed-aad.md)을 이미 활용할 수 있습니다. 이 통합을 사용 하도록 설정 하면 고객이 Azure AD 사용자, 그룹 또는 서비스 사용자를 Kubernetes RBAC의 제목으로 사용할 수 있습니다. 자세한 내용은 [여기](azure-ad-rbac.md)를 참조 하세요.
이 기능을 사용 하면 Kubernetes에 대 한 사용자 id와 자격 증명을 별도로 관리할 필요가 없습니다. 그러나 Azure RBAC 및 Kubernetes RBAC를 별도로 설정 하 고 관리 해야 합니다. 인증, 권한 부여 및 RBAC에 대 한 자세한 내용은 [여기](concepts-identity.md)를 참조 하세요. AKS.

이 문서에서는 Azure 리소스, AKS 및 Kubernetes 리소스에서 통합 관리 및 액세스 제어를 허용 하는 새로운 접근 방식을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure에서 Kubernetes 리소스에 대 한 RBAC를 관리 하는 기능을 통해 Azure 또는 기본 Kubernetes 메커니즘을 사용 하 여 클러스터 리소스에 대 한 RBAC를 관리할 수 있습니다. 사용 하도록 설정 되 면 azure AD 보안 주체는 Azure RBAC에 의해 독점적으로 유효성이 검사 되 고, regular Kubernetes 사용자 및 서비스 계정은 Kubernetes RBAC에 의해 독점적으로 유효성이 검사 됩니다. 인증, 권한 부여 및 RBAC에 대 한 자세한 내용은 [여기](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)를 참조 하세요. AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>필수 구성 요소 
- 미리 보기에 등록 <https://aka.ms/aad-rbac-sign-up-form> 합니다.
- Azure CLI 버전 2.9.0 이상 인지 확인 합니다.
- `EnableAzureRBACPreview`기능 플래그를 사용 하도록 설정 했는지 확인 합니다.
- `aks-preview` [CLI 확장][az-extension-add] v 0.4.55 이상을 설치 했는지 확인 합니다.
- [Kubectl v 1.18.3 +][az-aks-install-cli]를 설치 했는지 확인 합니다.

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`미리 보기 기능 등록

Kubernetes 권한 부여를 위해 Azure RBAC를 사용 하는 AKS 클러스터를 만들려면 `EnableAzureRBACPreview` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`EnableAzureRBACPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

플래그가 성공적으로 등록 되려면 위의 미리 보기 양식을 제출한 후 승인을 받아야 합니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register] [az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Azure RBAC를 사용 하는 AKS 클러스터를 만들려면 *AKS-preview* CLI 확장 버전 0.4.55 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치 하거나 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

- [관리 되는 AZURE AD 통합](managed-aad.md)이 필요 합니다.
- 미리 보기 중에는 Kubernetes 권한 부여에 대 한 Azure RBAC를 기존 클러스터에 통합할 수 없지만 GA (일반 공급)를 사용할 수 있습니다.
- [Kubectl v 1.18.3 +][az-aks-install-cli]를 사용 합니다.
- 미리 보기 중에는 Azure CLI를 통해 *네임 스페이스 수준* 사용 권한만 추가할 수 있습니다.
- CRDs가 있는 경우 사용자 지정 역할 정의를 제공 하는 유일한 방법은를 제공 하는 것입니다 `Microsoft.ContainerService/managedClusters/*/read` . AKS는 CRDs에 대 한 보다 세부적인 사용 권한을 제공 하기 위해 작업 중입니다. 나머지 개체의 경우 특정 API 그룹 (예:)을 사용할 수 있습니다 `Microsoft.ContainerService/apps/deployments/read` .
- 새 역할 할당은 전파 하는 데 최대 5 분이 걸릴 수 있으며 권한 부여 서버에서 업데이트 됩니다.

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Azure RBAC 및 관리 되는 Azure AD 통합을 사용 하 여 새 클러스터 만들기

다음 CLI 명령을 사용 하 여 AKS 클러스터를 만듭니다.

Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Kubernetes 권한 부여를 위해 관리 되는 Azure AD 통합 및 Azure RBAC를 사용 하 여 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Azure AD 통합 및 Azure RBAC for Kubernetes 권한 부여를 사용 하 여 클러스터를 성공적으로 만든 경우 응답 본문에는 다음 섹션이 포함 됩니다.

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>사용자가 클러스터에 액세스 하는 역할 할당 만들기

AKS는 다음과 같은 네 가지 기본 제공 역할을 제공 합니다.


| 역할                                | 설명  |
|-------------------------------------|--------------|
| Azure Kubernetes 서비스 RBAC 뷰어  | 읽기 전용 액세스를 허용 하 여 네임 스페이스의 대부분의 개체를 표시 합니다. 역할 또는 역할 바인딩을 볼 수 없습니다. `Secrets`비밀의 콘텐츠를 읽으면 네임 스페이스의 ServiceAccount 자격 증명에 액세스할 수 있으므로이 역할은 보기를 허용 하지 않습니다 .이는 네임 스페이스의 모든 ServiceAccount로 API 액세스를 허용 합니다 (권한 상승 형태).  |
| Azure Kubernetes 서비스 RBAC 기록기 | 네임 스페이스의 대부분의 개체에 대 한 읽기/쓰기 액세스를 허용 합니다. 이 역할은 역할이 나 역할 바인딩을 보거나 수정할 수 없습니다. 그러나이 역할을 사용 하 여 `Secrets` 네임 스페이스의 ServiceAccount로 pod를 액세스 하 고 실행할 수 있으므로 네임 스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻는 데 사용할 수 있습니다. |
| Azure Kubernetes 서비스 RBAC 관리자  | 네임 스페이스 내에서 부여할 수 있는 관리 액세스를 허용 합니다. 네임 스페이스 내에서 역할 및 역할 바인딩을 만드는 기능을 포함 하 여 네임 스페이스 (또는 클러스터 범위)에서 대부분의 리소스에 대 한 읽기/쓰기 액세스를 허용 합니다. 이 역할은 리소스 할당량 또는 네임 스페이스 자체에 대 한 쓰기 액세스를 허용 하지 않습니다. |
| Azure Kubernetes 서비스 RBAC 클러스터 관리자  | 슈퍼 사용자 액세스를 허용 하 여 모든 리소스에 대 한 작업을 수행할 수 있습니다. 클러스터의 모든 리소스와 모든 네임 스페이스에 대 한 모든 권한을 부여 합니다. |


**전체 AKS 클러스터** 로 범위가 지정 된 역할 할당은 Azure Portal 클러스터 리소스의 ACCESS CONTROL (IAM) 블레이드에서 또는 아래와 같이 Azure CLI 명령을 사용 하 여 수행할 수 있습니다.

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

여기서 `<AAD-ENTITY-ID>` 는 사용자 이름 (예: user@contoso.com ) 또는 서비스 사용자의 ClientID 일 수 있습니다.

클러스터 내의 특정 **네임 스페이스로** 범위가 지정 된 역할 할당을 만들 수도 있습니다.

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

현재 네임 스페이스로 범위가 지정 된 역할 할당은 Azure CLI를 통해 구성 해야 합니다.


### <a name="create-custom-roles-definitions"></a>사용자 지정 역할 정의 만들기

필요에 따라 고유한 역할 정의를 만든 다음 위와 같이 할당 하도록 선택할 수 있습니다.

다음은 사용자가 배포를 읽기만 할 수 있도록 하는 역할 정의의 예입니다. 가능한 작업의 전체 목록은 [여기](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)에서 확인할 수 있습니다.


아래 json을 이라는 파일에 복사 `deploy-view.json` 합니다.

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

`<YOUR SUBSCRIPTION ID>`을 (를) 실행 하 여 얻을 수 있는 구독의 ID로 바꿉니다.

```azurecli-interactive
az account show --query id -o tsv
```


이제 저장 한 폴더에서 아래 명령을 실행 하 여 역할 정의를 만들 수 있습니다 `deploy-view.json` .

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

역할 정의를 만들었으므로 다음을 실행 하 여 사용자 또는 다른 id에 할당할 수 있습니다.

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Kubernetes 권한 부여에 Azure RBAC 사용`kubectl`

> [!NOTE]
> 아래 명령을 실행 하 여 최신 kubectl 있는지 확인 합니다.
>
> ```azurecli-interactive
> az aks install-cli
> ```
> 권한으로 실행 해야 할 수도 있습니다 `sudo` . 

이제 원하는 역할 및 사용 권한을 할당 했습니다. 예를 들어, Kubernetes API 호출을 시작할 수 있습니다 `kubectl` .

이러한 목적을 위해 먼저 아래 명령을 사용 하 여 클러스터의 kubeconfig를 가져옵니다.

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 위의 단계를 수행 하려면 [Azure Kubernetes Service 클러스터 사용자](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 기본 제공 역할이 필요 합니다.

이제 kubectl을 사용 하 여 클러스터의 노드를 나열할 수 있습니다. 처음 실행 하는 경우 로그인 해야 하며 후속 명령에서 해당 액세스 토큰을 사용 합니다.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Kubernetes 권한 부여에 Azure RBAC 사용`kubelogin`

비 대화형 로그인, 이전 `kubectl` 버전 또는 새 클러스터에 로그인 할 필요 없이 여러 클러스터에서 SSO를 활용 하는 등의 추가 시나리오를 차단 해제 하 여 토큰이 여전히 유효 하다 고 AKS는 이라는 exec 플러그 인을 만들었습니다 [`kubelogin`](https://github.com/Azure/kubelogin) .

다음을 실행 하 여 사용할 수 있습니다.

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

처음에는 일반 kubectl와 같이 대화형으로 로그인 해야 하지만 이후에는 새 Azure AD 클러스터 (토큰이 여전히 유효한 경우) 에서도 더 이상 필요 하지 않습니다.

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>정리

### <a name="clean-role-assignment"></a>역할 할당 정리

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
수행한 모든 할당에서 ID를 복사한 다음.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>역할 정의 정리

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>클러스터 및 리소스 그룹 삭제

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

- AKS 인증, 권한 부여 및 RBAC에 대 한 자세한 내용은 [여기](concepts-identity.md)를 참조 하세요.
- Azure RBAC에 대 한 자세한 내용은 [여기](../role-based-access-control/overview.md)를 참조 하세요.
- Kubernetes [권한 부여](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)에 대 한 사용자 지정 Azure 역할을 세부적으로 정의 하는 데 사용할 수 있는 모든 작업에 대해 자세히 알아보세요.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
