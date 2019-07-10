---
title: 가상 머신의 내용을 감사 하는 방법 이해
description: Azure Policy Azure Kubernetes Service에서 클러스터를 관리 하 Rego 및 Open 정책 에이전트를 사용 하는 방법에 대해 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453906"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대 한 Azure 정책 이해

Azure Policy가 통합 된 [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) 클러스터에서 대규모로 사항이 및 보호를 중앙 집중화 하 고 일관 된 방식으로 적용 하 합니다.
사용을 확장 하 여 [게이트 키퍼](https://github.com/open-policy-agent/gatekeeper), _입학 허가 컨트롤러 webhook_ 에 대 한 [열기 정책 에이전트](https://www.openpolicyagent.org/) (불투명) Azure Policy 수 있도록 관리 및 규정 준수 보고 한 곳에서 AKS 클러스터 및 Azure 리소스의 상태입니다.

> [!NOTE]
> AKS에 대 한 azure Policy는 제한 된 미리 보기로 제공 하 고만 기본 제공 정책 정의 지원 합니다.

## <a name="overview"></a>개요

을 사용 하려면 AKS에 대 한 Azure Policy를 사용 하 여 AKS 클러스터를 사용 하 여 다음 작업을 수행 합니다.

- [미리 보기 기능 용 옵트인](#opt-in-for-preview)
- [Azure Policy 추가 기능 설치](#installation-steps)
- [AKS에 대 한 정책 정의 할당](#built-in-policies)
- [유효성 검사에 대 한 대기](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>미리 보기에 옵트인

Azure 정책 추가 기능 설치 또는 서비스 기능 중 하나를 사용 하기 전에 구독의 사용 하도록 설정 해야 합니다 **Microsoft.ContainerService** 리소스 공급자 및 **Microsoft.PolicyInsights**리소스 공급자를 미리 보기에 가입 하려면 승인을 받아야 합니다. 미리 보기에 가입 시키려면 Azure portal 또는 Azure CLI를 사용 하 여 다음이 단계를 수행 합니다.

- Azure Portal:

  1. 등록 된 **Microsoft.ContainerService** 하 고 **Microsoft.PolicyInsights** 리소스 공급자입니다. 단계에 대해서 [리소스 공급자 및 형식](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)합니다.

  1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

     ![모든 서비스에서 정책 검색](../media/rego-for-aks/search-policy.png)

  1. 선택 **미리 보기 참가** Azure 정책 페이지의 왼쪽에 있습니다.

     ![AKS 미리 보기에 대 한 정책 연결](../media/rego-for-aks/join-aks-preview.png)

  1. 행 미리 보기에 추가 하려는 구독을 선택 합니다.

  1. 선택 된 **옵트인** 구독 목록 맨 위에 있는 단추입니다.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Azure Policy 추가 기능

합니다 _Azure 정책 추가 기능_ 게이트 키퍼 입학 허가 컨트롤러에 Azure Policy 서비스를 연결 하는 Kubernetes에 대 한 합니다. 에 설치 되는 추가 기능에는 _azure 정책_ 네임 스페이스에는 다음 함수를 시행 합니다.

- Azure Policy를 사용 하 여 AKS 클러스터에 대 한 할당 검사
- 다운로드 하 고 정책 세부 정보를 포함 하 여 캐시 합니다 _rego_ 정책 정의로 **configmaps**
- AKS 클러스터에서 전체 검색 준수 검사를 실행합니다.
- Azure Policy에 백업 하는 감사 보고서와 규정 준수 세부 정보

### <a name="installing-the-add-on"></a>추가 기능 설치

#### <a name="prerequisites"></a>필수 조건

AKS 클러스터에 추가 기능을 설치 하기 전에 미리 보기 확장을 설치 해야 합니다. 이 단계는 Azure CLI를 사용 하 여 수행 됩니다.

1. 이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.62 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. AKS 클러스터의 버전 이어야 합니다. _1.10_ 이상. 다음 스크립트를 사용 하 여 AKS 클러스터 버전의 유효성을 검사 합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 버전을 설치 _0.4.0_ AKS 용 미리 보기 확장의 Azure CLI `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 이전에 설치한 경우 합니다 _aks 미리 보기_ 확장을 사용 하 여 업데이트 된 설치는 `az extension update --name aks-preview` 명령입니다.

#### <a name="installation-steps"></a>설치 단계

필수 구성 요소가 완료 되 면 관리 하려는 AKS 클러스터에서 Azure Policy 추가 기능을 설치 합니다.

- Azure portal

  1. 클릭 하 여 Azure portal에서 AKS 서비스를 시작할 **모든 서비스**, 다음에 대 한 검색 및 선택 **Kubernetes 서비스**합니다.

  1. AKS 클러스터 중 하나를 선택 합니다.

  1. 선택 **정책 (미리 보기)** Kubernetes 서비스 페이지의 왼쪽에 있습니다.

     ![AKS 클러스터에서 정책](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 기본 페이지에서 선택 합니다 **추가 기능을 활성화할** 단추입니다.

     ![AKS 추가 기능에 대 한 Azure Policy를 사용 하도록 설정](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 경우는 **추가 기능을 활성화할** 단추는 회색, 구독이 미리 보기에 아직 추가 되지 않았습니다. 참조 [옵트인 미리 보기에 대 한](#opt-in-for-preview) 필요한 단계에 대 한 합니다.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>유효성 검사 및 빈도 보고 합니다.

추가 기능 확인 Azure Policy를 사용 하 여 정책 할당의 변경 내용에 대 일 분 마다입니다. 이 새로 고침 주기 동안 추가 기능을 모두 제거 _configmaps_ 에 _azure 정책_ 네임 스페이스 다음 다시 만듭니다 합니다 _configmaps_ 게이트 키퍼 사용에 대 한 합니다.

> [!NOTE]
> 하는 동안를 _클러스터 관리자_ 권한을 가질 수 합니다 _azure 정책_ 네임 스페이스 권장 되거나 되지 네임 스페이스를 변경 하려면 지원 합니다. 수동으로 변경한 새로 고침 주기 동안 손실 됩니다.

5 분 마다 클러스터의 전체 검색에 대 한 추가 기능을 호출합니다. 클러스터에 시도 된 변경의 게이트 키퍼에서 전체 검색 및 실시간 모든 평가의 세부 정보를 수집, 후 추가 기능 결과에 다시 보고 Azure Policy에 포함할 [준수 세부 정보](../how-to/get-compliance-data.md) 모든 Azure Policy와 같은 할당 합니다. 감사 주기 동안 사용 하는 정책 할당에 대 한 결과만 반환 됩니다.

## <a name="policy-language"></a>정책 언어

AKS를 관리 하기 위한 Azure Policy 언어 구조는 기존 정책을 따릅니다. 효과 _EnforceRegoPolicy_ 하며 AKS 클러스터를 관리 하는 데 사용 됩니다 _세부 정보_ 불투명 및 게이트 키퍼를 사용 하 여 작업에 관련 된 속성입니다. 세부 정보 및 예제를 참조 합니다 [EnforceRegoPolicy](effects.md#enforceregopolicy) 적용 합니다.

일부로 합니다 _details.policy_ Azure Policy는 정책 정의에 속성 추가 기능에 rego 정책의 URI를 전달 합니다. Rego에 불투명 및 게이트 키퍼 유효성을 검사 하거나 변경할 Kubernetes 클러스터에 요청을 지 원하는 언어입니다. Kubernetes 관리에 대 한 기존 표준을 지 Azure 정책을 사용 하면 기존 규칙을 다시 사용 하 고 보고 환경이 통합된 클라우드 준수에 대 한 Azure Policy를 사용 하 여 연결할 수 있습니다. 자세한 내용은 [Rego 란?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)합니다.

## <a name="built-in-policies"></a>기본 제공 정책

Azure portal을 사용 하 여 AKS를 관리 하기 위한 기본 제공 정책을 찾으려면 다음이 단계를 수행 합니다.

1. Azure portal에서 Azure Policy 서비스를 시작 합니다. 선택 **모든 서비스** 왼쪽된 창 및 다음에 대 한 검색 및 선택 **정책**합니다.

1. Azure Policy 페이지의 왼쪽된 창에서 선택 **정의**합니다.

1. Category 드롭다운 목록 상자에서 사용 하 여 **모두 선택** 필터의 선택을 취소 하 여 선택한 **Kubernetes 서비스**합니다.

1. 정책 정의 선택한 다음 선택 합니다 **할당** 단추입니다.

> [!NOTE]
> AKS 정의 대 한 Azure 정책을 할당할 때 합니다 **범위** AKS 클러스터 리소스를 포함 해야 합니다.

또는 사용 하 여 합니다 [정책 할당-포털](../assign-policy-portal.md) 빠른 시작을 찾고 AKS 정책을 할당 합니다. 'Vm 감사' 샘플 대신 Kubernetes 정책 정의 검색 합니다.

## <a name="logging"></a>로깅

### <a name="azure-policy-add-on-logs"></a>Azure 정책 추가 로그

Azure Policy 추가 기능을 컨테이너/Kubernetes 컨트롤러, AKS 클러스터에서 로그를 유지합니다. 로그에서 노출 되는 **Insights** AKS 클러스터 페이지입니다. 자세한 내용은 [이해 AKS 클러스터 컨테이너에 대 한 Azure Monitor를 사용 하 여 성능](../../../azure-monitor/insights/container-insights-analyze.md)합니다.

### <a name="gatekeeper-logs"></a>게이트 키퍼 로그

새 리소스 요청에 대 한 게이트 키퍼 로그를 활성화 하려면의 단계에 따라 [을 사용 하도록 설정 하 고 마스터 노드 로그 AKS에서 Kubernetes 검토](../../../aks/view-master-logs.md)합니다.
리소스에 대 한 새 요청에 거부 된 이벤트를 보려면 쿼리의 예는 다음과 같습니다.

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

게이트 키퍼 컨테이너에서 로그를 보려면의 단계에 따라 [사용 하도록 설정 하 고 마스터 노드 로그 AKS에서 Kubernetes를 검토](../../../aks/view-master-logs.md) 확인 하 고는 _kube apiserver_ 옵션을 **진단 설정** 창입니다.

## <a name="remove-the-add-on"></a>추가 기능 제거

Azure Policy 추가 기능을 통해 AKS 클러스터에서를 제거 하려면 Azure portal 또는 Azure CLI를 사용 합니다.

- Azure portal

  1. 클릭 하 여 Azure portal에서 AKS 서비스를 시작할 **모든 서비스**, 다음에 대 한 검색 및 선택 **Kubernetes 서비스**합니다.

  1. Azure Policy 추가 기능을 사용 하지 않도록 설정 하려는 AKS 클러스터를 선택 합니다.

  1. 선택 **정책 (미리 보기)** Kubernetes 서비스 페이지의 왼쪽에 있습니다.

     ![AKS 클러스터에서 정책](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 기본 페이지에서 선택 합니다 **추가 기능을 사용 하지 않도록 설정** 단추입니다.

     ![AKS 추가 기능에 대 한 Azure Policy를 사용 하지 않도록 설정](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>다음 단계

- 예제를 검토 [Azure Policy 샘플](../samples/index.md)합니다.
- [Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- 이해 하는 방법 [프로그래밍 방식으로 정책 만들기](../how-to/programmatically-create.md)합니다.
- 에 대해 알아봅니다 하는 방법 [규정 준수 데이터를 가져올](../how-to/getting-compliance-data.md)합니다.
- 설명 하는 방법 [비준수 리소스를 수정](../how-to/remediate-resources.md)합니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/index.md)을 포함하는 관리 그룹을 검토합니다.