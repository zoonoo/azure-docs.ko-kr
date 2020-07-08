---
title: Azure Kubernetes Service에서 관리 되는 id 사용
description: Azure Kubernetes 서비스 (AKS)에서 관리 id를 사용 하는 방법 알아보기
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/30/2020
ms.author: mlearned
ms.openlocfilehash: 30d1290f9eb7b2750f09e5e256d4dd212c7e4607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610288"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 관리 되는 id 사용

현재 azure Kubernetes 서비스 (AKS) 클러스터 (특히 Kubernetes 클라우드 공급자)에는 Azure에서 부하 분산 장치 및 관리 디스크와 같은 추가 리소스를 만들기 위한 id가 필요 합니다. 이 id는 *관리 되는 id* 또는 *서비스 사용자*일 수 있습니다. [서비스 주체](kubernetes-service-principal.md)를 사용 하는 경우 하나를 제공 하거나 사용자를 대신 하 여 AKS를 만들어야 합니다. 관리 id를 사용 하는 경우 자동으로 AKS에 의해 생성 됩니다. 서비스 주체를 사용 하는 클러스터는 궁극적으로 클러스터 작동을 유지 하기 위해 서비스 주체를 갱신 해야 하는 상태에 도달 합니다. 서비스 주체를 관리 하면 복잡성이 증가 하므로 관리 되는 id를 대신 사용 하는 것이 더 쉽습니다. 서비스 사용자와 관리 되는 id 모두에 대해 동일한 권한 요구 사항이 적용 됩니다.

*관리 id* 는 기본적으로 서비스 사용자를 중심으로 하는 래퍼로, 관리를 단순화 합니다. MI에 대 한 자격 증명 회전은 기본값 Azure Active Directory 따라 46 일 마다 자동으로 발생 합니다. AKS는 시스템 할당 및 사용자 할당 관리 id 유형을 모두 사용 합니다. 이러한 id는 현재 변경할 수 없습니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스를 설치 해야 합니다.

- Azure CLI 버전 2.2.0 이상

## <a name="limitations"></a>제한 사항

* 사용자 고유의 관리 id 가져오기는 현재 지원 되지 않습니다.
* 관리 id를 사용 하는 AKS 클러스터는 클러스터를 만드는 동안에만 사용할 수 있습니다.
* 관리 되는 id를 사용 하도록 기존 AKS 클러스터를 업데이트 하거나 업그레이드할 수 없습니다.
* 클러스터 **업그레이드** 작업 중에 관리 되는 id를 일시적으로 사용할 수 없습니다.

## <a name="summary-of-managed-identities"></a>관리 id 요약

AKS는 기본 제공 서비스 및 추가 기능에 대해 여러 관리 되는 id를 사용 합니다.

| ID                       | 이름    | 사용 사례 | 기본 권한 | 사용자 고유의 id 가져오기
|----------------------------|-----------|----------|
| 제어 평면 | 표시 되지 않음 | AKS에서 네트워킹 리소스를 관리 하는 데 사용 됩니다. 예를 들어 수신, 공용 IP 등에 대 한 부하 분산 장치를 만듭니다.| 노드 리소스 그룹에 대 한 참가자 역할 | 현재 지원되지 않음
| kubelet | AKS 클러스터 이름-agentpool | Azure Container Registry 인증 (ACR) | 노드 리소스 그룹에 대 한 읽기 역할 | 현재 지원되지 않음
| 추가 기능 | AzureNPM | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | AzureCNI 네트워크 모니터링 | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | azurepolicy (게이트 키퍼) | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | azurepolicy | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | Calico | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | 대시보드 | Id가 필요 하지 않음 | 해당 없음 | 아니요
| 추가 기능 | HTTPApplicationRouting | 필요한 네트워크 리소스를 관리 합니다. | 노드 리소스 그룹에 대 한 읽기 역할, DNS 영역에 대 한 참가자 역할 | 아니요
| 추가 기능 | 수신 응용 프로그램 게이트웨이 | 필요한 네트워크 리소스를 관리 합니다.| 노드 리소스 그룹에 대 한 참가자 역할 | 아니요
| 추가 기능 | omsagent | Azure Monitor에 AKS 메트릭을 전송 하는 데 사용 됩니다. | 모니터링 메트릭 게시자 역할 | 아니요
| 추가 기능 | 가상 노드 (ACIConnector) | Azure Container Instances (ACI)에 필요한 네트워크 리소스를 관리 합니다. | 노드 리소스 그룹에 대 한 참가자 역할 | 아니요


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

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

다음 명령을 사용 하 여 관리 되는 컨트롤 평면 id의 objectid를 쿼리 합니다.

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

결과는 다음과 같습니다.

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> 리소스가 작업자 노드 리소스 그룹의 외부에 있는 경우 고유한 VNet, 고정 IP 주소 또는 연결 된 Azure 디스크를 만들고 사용 하려면 클러스터 시스템 할당 관리 Id의 PrincipalID를 사용 하 여 역할 할당을 수행 합니다. 역할 할당에 대 한 자세한 내용은 [다른 Azure 리소스에 대 한 액세스 위임](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)을 참조 하세요.
>
> Azure Cloud provider에서 사용 하는 클러스터 관리 Id에 대 한 권한 부여를 채우는 데 60 분 정도 걸릴 수 있습니다.

마지막으로 클러스터에 액세스 하기 위한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

클러스터가 몇 분 안에 생성 됩니다. 그런 다음 응용 프로그램 워크 로드를 새 클러스터에 배포 하 고 서비스 주체 기반 AKS 클러스터에서 수행한 것 처럼 상호 작용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [ARM (Azure Resource Manager) 템플릿을][aks-arm-template] 사용 하 여 관리 되는 id 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters
