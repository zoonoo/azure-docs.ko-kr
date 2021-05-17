---
title: AKS(Azure Kubernetes Service)에서 kubeconfig에 대한 액세스 제한
description: 클러스터 관리자 및 클러스터 사용자의 Kubernetes 구성 파일(kubeconfig) 액세스 권한을 제어하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693988"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Azure 역할 기반 액세스 제어를 사용하여 AKS(Azure Kubernetes Service)의 Kubernetes 구성 파일에 대한 액세스를 정의합니다.

`kubectl` 도구를 사용하여 Kubernetes 클러스터와 상호 작용할 수 있습니다. Azure CLI는 `kubectl`을 사용하여 AKS 클러스터에 연결하는 데 필요한 액세스 자격 증명 및 구성 정보를 쉽게 받을 수 있는 방법을 제공합니다. Kubernetes 구성(*kubeconfig*) 정보를 가져올 수 있는 사용자와 해당 권한을 제한하려면 Azure RBAC(역할 기반 액세스 제어)를 사용할 수 있습니다.

이 문서에서는 AKS 클러스터의 구성 정보를 받을 수 있는 사람을 제한하는 Azure 역할을 할당하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 이 문서에서는 Azure CLI 버전 2.0.65 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="available-cluster-roles-permissions"></a>사용 가능한 클러스터 역할 권한

`kubectl` 도구를 사용하여 AKS 클러스터와 상호 작용할 때는 클러스터 연결 정보를 정의하는 구성 파일이 사용됩니다. 이 구성 파일은 일반적으로 *~/.kube/config* 에 저장됩니다. 이 *kubeconfig* 파일에 여러 클러스터를 정의할 수 있습니다. [kubectl config use-context][kubectl-config-use-context] 명령을 사용하여 클러스터 간에 전환할 수 있습니다.

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 액세스 자격 증명을 가져와 *kubeconfig* 파일에 병합할 수 있습니다. Azure 역할 기반 액세스 제어(Azure RBAC)를 사용하여 이러한 자격 증명에 대한 액세스를 제어할 수 있습니다. 이러한 Azure 역할을 사용하여 *kubeconfig* 파일을 검색할 수 있는 사람과 그 사람이 클러스터 내에서 가지는 권한을 정의할 수 있습니다.

두 가지 기본 제공 역할은 다음과 같습니다.

* **Azure Kubernetes Service 클러스터 관리자 역할**  
  * *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API 호출에 대한 액세스를 허용합니다. 이 API 호출은 [클러스터 관리자 자격 증명을 나열][api-cluster-admin]합니다.
  * *clusterAdmin* 역할의 *kubeconfig* 를 다운로드합니다.
* **Azure Kubernetes Service 클러스터 사용자 역할**
  * *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API 호출에 대한 액세스를 허용합니다. 이 API 호출은 [클러스터 사용자 자격 증명을 나열][api-cluster-user]합니다.
  * *clusterUser* 역할의 *kubeconfig* 를 다운로드합니다.

이러한 Azure 역할을 Azure AD(Active Directory) 사용자 또는 그룹에 적용할 수 있습니다.

> [!NOTE]
> Azure AD를 사용하는 클러스터에서 *clusterUser* 역할이 있는 사용자에게 로그인을 요청하는 빈 *kubeconfig* 파일이 있습니다. 로그인되면 사용자는 해당 Azure AD 사용자 또는 그룹 설정에 따라 액세스 권한을 가집니다. *clusterAdmin* 역할이 있는 사용자에게는 관리자 액세스 권한이 있습니다.
>
> Azure AD를 사용하지 않는 클러스터는 *clusterAdmin* 역할만 사용합니다.

## <a name="assign-role-permissions-to-a-user-or-group"></a>사용자 또는 그룹에 역할 권한 할당

사용 가능한 역할 중 하나를 할당하려면 AKS 클러스터의 리소스 ID와 사용자 계정의 ID를 가져와야 합니다. 다음 예제 명령에서 수행하는 작업은 다음과 같습니다.

* *myResourceGroup* 리소스 그룹의 *myAKSCluster* 라는 클러스터에 [az aks show][az-aks-show] 명령을 사용하여 클러스터 리소스 ID를 가져옵니다. 필요에 따라 고유한 클러스터 및 리소스 그룹 이름을 지정합니다.
* [az account show][az-account-show] 및 [az ad user show][az-ad-user-show] 명령을 사용하여 사용자 ID를 가져옵니다.
* 마지막으로, [az role assignment create][az-role-assignment-create] 명령을 사용하여 역할을 할당합니다.

다음 예에서는 *Azure Kubernetes Service 클러스터 관리자 역할* 을 개별 사용자 계정에 할당합니다.

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> 경우에 따라 계정에 있는 *user.name* 은 Azure AD 게스트 사용자와 같은 *userPrincipalName* 과 다릅니다.
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> 이 경우 *ACCOUNT_UPN* 값을 Azure AD 사용자의 *userPrincipalName* 으로 설정합니다. 예를 들어 계정 *user.name* 이 *user\@contoso.com* 인 경우가 있습니다.
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Azure AD 그룹에 사용 권한을 할당하려는 경우 *사용자* 가 아닌 *그룹* 의 개체 ID를 사용하여 이전 예제에 표시된 `--assignee` 매개 변수를 업데이트합니다. 그룹의 개체 ID를 가져오려면 [az ad group show][az-ad-group-show] 명령을 사용합니다. 다음 예제에서는 *appdev*: `az ad group show --group appdev --query objectId -o tsv`라는 Azure AD 그룹의 개체 ID를 가져옵니다.

필요에 따라 이전에 할당한 역할을 *클러스터 사용자 역할* 로 변경할 수 있습니다.

다음 예제 출력에서는 역할 할당이 성공적으로 생성되었음을 보여줍니다.

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>구성 정보를 가져와서 확인

Azure 역할이 할당된 경우 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 *kubeconfig* 정의를 가져올 수 있습니다. 다음 예에서는 사용자에게 *클러스터 관리자 역할* 이 부여된 경우에만 올바르게 작동하는 *--admin* 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

그런 다음, [kubectl config view][kubectl-config-view] 명령을 사용하여 클러스터의 *context* 에 관리자 구성 정보가 적용되었다고 표시되는지 확인할 수 있습니다.

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>역할 권한 제거

역할 할당을 제거하려면 [az role assignment delete][az-role-assignment-delete] 명령을 사용하세요. 이전 명령에서 가져온 계정 ID 및 클러스터 리소스 ID를 지정하세요. 사용자가 아닌 그룹에 역할을 할당한 경우 `--assignee` 매개 변수에 대한 계정 개체 ID 대신 적절한 그룹 개체 ID를 지정합니다.

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>다음 단계

AKS 클러스터에 대한 액세스 보안을 강화하기 위해 [Azure Active Directory 인증을 통합][aad-integration]합니다.

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
