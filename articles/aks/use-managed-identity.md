---
title: Azure Kubernetes Service에서 관리 되는 id 사용
description: Azure Kubernetes 서비스 (AKS)에서 관리 id를 사용 하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 3ace7f1c93ab3918f460d245a863db43d98f1db5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176096"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 관리 되는 id 사용

현재 azure Kubernetes 서비스 (AKS) 클러스터 (특히 Kubernetes 클라우드 공급자)에는 Azure에서 부하 분산 장치 및 관리 디스크와 같은 추가 리소스를 만들기 위한 id가 필요 합니다. 이 id는 *관리 되는 id* 또는 *서비스 사용자* 일 수 있습니다. [서비스 주체](kubernetes-service-principal.md)를 사용 하는 경우 하나를 제공 하거나 사용자를 대신 하 여 AKS를 만들어야 합니다. 관리 id를 사용 하는 경우 자동으로 AKS에 의해 생성 됩니다. 서비스 주체를 사용 하는 클러스터는 궁극적으로 클러스터 작동을 유지 하기 위해 서비스 주체를 갱신 해야 하는 상태에 도달 합니다. 서비스 주체를 관리 하면 복잡성이 증가 하므로 관리 되는 id를 대신 사용 하는 것이 더 쉽습니다. 서비스 사용자와 관리 되는 id 모두에 대해 동일한 권한 요구 사항이 적용 됩니다.

*관리 id* 는 기본적으로 서비스 사용자를 중심으로 하는 래퍼로, 관리를 단순화 합니다. MI에 대 한 자격 증명 회전은 기본값 Azure Active Directory 따라 46 일 마다 자동으로 발생 합니다. AKS는 시스템 할당 및 사용자 할당 관리 id 유형을 모두 사용 합니다. 이러한 id는 현재 변경할 수 없습니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스를 설치 해야 합니다.

- Azure CLI 버전 2.15.1 이상

## <a name="limitations"></a>제한 사항

* 테 넌 트 이동/관리 되는 id 사용 클러스터의 마이그레이션은 지원 되지 않습니다.
* 클러스터가 `aad-pod-identity` 사용 하도록 Node-Managed 설정 된 경우 Pod Identity (NMI)는 Azure 인스턴스 메타 데이터 끝점에 대 한 호출을 가로채는 노드의 iptables를 수정 합니다. 이 구성은 pod가를 사용 하지 않는 경우에도 메타 데이터 끝점에 대 한 모든 요청이 NMI에 의해 차단 됨을 의미 합니다 `aad-pod-identity` . AzurePodIdentityException CRD는 CRD에서 정의 된 `aad-pod-identity` 레이블과 일치 하는 pod에서 발생 하는 메타 데이터 끝점에 대 한 모든 요청이 NMI를 처리 하지 않고 프록시 되어야 함을 알리도록 구성할 수 있습니다. `kubernetes.azure.com/managedby: aks`AZUREPODIDENTITYEXCEPTION CRD를 구성 하 여 _kube_ 네임 스페이스에서 레이블이 인 시스템 pod는에서 제외 되어야 합니다 `aad-pod-identity` . 자세한 내용은 [특정 pod 또는 응용 프로그램에 대 한 aad-Id 사용 안 함](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)을 참조 하세요.
  예외를 구성 하려면 [mic 예외 YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)을 설치 합니다.

## <a name="summary-of-managed-identities"></a>관리 id 요약

AKS는 기본 제공 서비스 및 추가 기능에 대해 여러 관리 되는 id를 사용 합니다.

| ID                       | Name    | 사용 사례 | 기본 권한 | 사용자 고유의 id 가져오기
|----------------------------|-----------|----------|
| 컨트롤 플레인 | 표시 되지 않음 | AKS 제어 평면 구성 요소에서 수신 부하 분산 장치 및 AKS 관리 되는 공용 Ip를 포함 하 여 클러스터 리소스를 관리 하는 데 사용 되며 클러스터 Autoscaler 작업 | 노드 리소스 그룹에 대 한 참가자 역할 | 지원됨
| Kubelet | AKS 클러스터 이름-agentpool | Azure Container Registry 인증 (ACR) | NA (kubernetes v 1.15 +의 경우) | 현재 지원되지 않음
| 추가 기능 | AzureNPM | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | AzureCNI 네트워크 모니터링 | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | azure-정책 (게이트 키퍼) | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | azure-정책 | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | Calico | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | 대시보드 | Id가 필요 하지 않음 | 해당 없음 | 예
| 추가 기능 | HTTPApplicationRouting | 필요한 네트워크 리소스를 관리 합니다. | 노드 리소스 그룹에 대 한 읽기 역할, DNS 영역에 대 한 참가자 역할 | 예
| 추가 기능 | 수신 응용 프로그램 게이트웨이 | 필요한 네트워크 리소스를 관리 합니다.| 노드 리소스 그룹에 대 한 참가자 역할 | 예
| 추가 기능 | omsagent | Azure Monitor에 AKS 메트릭을 전송 하는 데 사용 됩니다. | 모니터링 메트릭 게시자 역할 | 예
| 추가 기능 | Virtual-Node (ACIConnector) | Azure Container Instances (ACI)에 필요한 네트워크 리소스를 관리 합니다. | 노드 리소스 그룹에 대 한 참가자 역할 | 예
| OSS 프로젝트 | aad-pod-id | 응용 프로그램이 AAD (Azure Active Directory)를 사용 하 여 클라우드 리소스에 안전 하 게 액세스할 수 있도록 합니다. | 해당 없음 | 에서 사용 권한을 부여 하는 단계 https://github.com/Azure/aad-pod-identity#role-assignment 입니다.

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리 id를 사용 하 여 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용 하 여 관리 id를 사용 하 여 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

그런 다음, AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

관리 id를 사용 하 여 성공적으로 클러스터를 만들려면 다음 서비스 사용자 프로필 정보가 포함 됩니다.

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

다음 명령을 사용 하 여 관리 되는 컨트롤 평면 id의 objectid를 쿼리 합니다.

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

결과는 다음과 같습니다.

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

클러스터를 만든 후에는 응용 프로그램 워크 로드를 새 클러스터에 배포 하 고 서비스 주체 기반 AKS 클러스터에서 수행한 것 처럼 상호 작용할 수 있습니다.

> [!NOTE]
> 리소스가 작업자 노드 리소스 그룹의 외부에 있는 경우 고유한 VNet, 고정 IP 주소 또는 연결 된 Azure 디스크를 만들고 사용 하려면 클러스터 시스템 할당 관리 Id의 PrincipalID를 사용 하 여 역할 할당을 수행 합니다. 역할 할당에 대 한 자세한 내용은 [다른 Azure 리소스에 대 한 액세스 위임](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)을 참조 하세요.
>
> Azure Cloud provider에서 사용 하는 클러스터 관리 Id에 대 한 권한 부여를 채우는 데 60 분 정도 걸릴 수 있습니다.

마지막으로 클러스터에 액세스 하기 위한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>AKS 클러스터를 관리 되는 id로 업데이트 (미리 보기)

이제 다음 CLI 명령을 사용 하 여 관리 되는 id와 함께 작동 하도록 현재 서비스 사용자로 작업 하는 AKS 클러스터를 업데이트할 수 있습니다.

먼저 시스템 할당 id에 대 한 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

시스템이 할당 한 id를 업데이트 합니다.

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

사용자 할당 id에 대 한 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

사용자 할당 id를 업데이트 합니다.

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> 시스템 할당 또는 사용자 할당 id를 관리 id로 업데이트 한 후에는 노드에서를 수행 `az aks nodepool upgrade --node-image-only` 하 여 관리 되는 id에 대 한 업데이트를 완료 합니다.

## <a name="bring-your-own-control-plane-mi"></a>사용자 고유의 컨트롤 평면 MI 가져오기
사용자 지정 제어 평면 id를 사용 하면 클러스터를 만들기 전에 기존 id에 액세스할 수 있습니다. 이 기능을 사용 하면 미리 만든 관리 id로 사용자 지정 VNET 또는 outboundType를 사용 하는 등의 시나리오를 사용할 수 있습니다.

Azure CLI 버전 2.15.1 이상이 설치 되어 있어야 합니다.

### <a name="limitations"></a>제한 사항
* Azure Government 현재 지원 되지 않습니다.
* Azure 중국 21Vianet은 현재 지원 되지 않습니다.

아직 관리 되는 id가 없는 경우에는 [az IDENTITY CLI][az-identity-create]를 사용 하 여 예를 들어 계속 해 서 만들어야 합니다.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
결과는 다음과 같습니다.

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

관리 id가 구독의 일부인 경우 [az IDENTITY CLI 명령을][az-identity-list] 사용 하 여 쿼리할 수 있습니다.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

이제 다음 명령을 사용 하 여 기존 id로 클러스터를 만들 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

사용자 고유 관리 id를 사용 하 여 클러스터를 성공적으로 만들려면 다음 userAssignedIdentities 프로필 정보를 포함 합니다.

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>다음 단계
* [ARM (Azure Resource Manager) 템플릿을][aks-arm-template] 사용 하 여 관리 되는 id 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
