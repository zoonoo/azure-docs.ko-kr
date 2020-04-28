---
title: AKS 엔진에 대 한 Azure Policy 알아보기
description: AKS 엔진을 사용 하 여 클러스터를 관리 하기 위해 CustomResourceDefinitions를 사용 하 고 게이트 키퍼 v3에서 정책 에이전트를 여 Azure Policy는 방법을 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436435"
---
# <a name="understand-azure-policy-for-aks-engine"></a>AKS 엔진에 대 한 Azure Policy 이해

Azure Policy는 Azure에서 자체 관리 되는 Kubernetes 클러스터를 신속 하 게 부트스트랩 하는 편리한 도구를 제공 하는 [AKS 엔진과](https://github.com/Azure/aks-engine/blob/master/docs/README.md)통합 됩니다. 이러한 통합을 통해 중앙 집중식으로 일관 된 방식으로 AKS 엔진 자체 관리 클러스터에 대 한 규모 사항을 및 보호 기능을 사용할 수 있습니다. Azure Policy Kubernetes에 대 한 _허용 컨트롤러 webhook_ 인 [Open Policy Agent](https://www.openpolicyagent.org/) (Opa) [게이트 키퍼](https://github.com/open-policy-agent/gatekeeper) v3 (베타)의 사용을 확장 하 여 Azure 리소스 및 AKS 엔진 자체 관리 클러스터의 준수 상태를 한 곳에서 관리 하 고 보고할 수 있습니다.

> [!NOTE]
> AKS 엔진에 대 한 Azure Policy는 공개 미리 보기로 제공 되며 SLA는 없습니다. 게이트 키퍼 v3은 베타 버전이 며 오픈 소스 커뮤니티에서 지원 됩니다. 서비스는 기본 제공 정책 정의 및 서비스 주체를 사용 하 여 구성 된 각 리소스 그룹에 대 한 단일 AKS Engine 클러스터만 지원 합니다.

> [!IMPORTANT]
> AKS 엔진, AKS 엔진 또는 게이트 키퍼 v3에 대 한 Azure Policy 지원을 받으려면 AKS Engine GitHub 리포지토리에서 [새 문제](https://github.com/Azure/aks-engine/issues/new/choose) 를 만듭니다.

## <a name="overview"></a>개요

Azure의 자체 관리 Kubernetes 클러스터에서 AKS 엔진에 대 한 Azure Policy를 사용 하도록 설정 하 고 사용 하려면 다음 작업을 수행 합니다.

- [전제 조건](#prerequisites)
- [Azure Policy 추가 기능 설치](#installing-the-add-on)
- [AKS 엔진에 대 한 정책 정의 할당](#built-in-policies)
- [유효성 검사 대기](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>사전 요구 사항

Azure Policy 추가 기능을 설치 하거나 서비스 기능을 사용 하도록 설정 하기 전에 구독은 **Microsoft. PolicyInsights** 리소스 공급자를 사용 하도록 설정 하 고 클러스터 서비스 주체에 대 한 역할 할당을 만들어야 합니다. 

1. 리소스 공급자를 사용 하도록 설정 하려면 [리소스 공급자 및 형식](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) 의 단계를 수행 하거나 Azure CLI 또는 Azure PowerShell 명령을 실행 합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 클러스터 서비스 사용자에 대 한 역할 할당 만들기

   - 클러스터 서비스 사용자 앱 ID를 모르는 경우 다음 명령을 사용 하 여 조회 합니다.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI를 사용 하 여 ' Policy Insights 데이터 기록기 (미리 보기) ' 역할 할당을 클러스터 서비스 사용자 앱 ID (이전 단계의 값 _aadClientID_ )에 할당 합니다. 를 `<subscriptionId>` 구독 ID로 바꾸고, `<aks engine cluster resource group>` 을 AKS 엔진 자체 관리 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>추가 기능 Azure Policy

Kubernetes에 대 한 _Azure Policy 추가 기능_ 에서 게이트 키퍼 허용 컨트롤러에 Azure Policy 서비스를 연결 합니다. _Kube_ 네임 스페이스에 설치 되는 추가 기능은 다음 함수를 시행 합니다.

- AKS 엔진 클러스터에 할당 Azure Policy를 확인 합니다.
- 정책 세부 정보, 제약 조건 템플릿 및 제약 조건을 다운로드 하 고 설치 합니다.
- AKS 엔진 클러스터에서 전체 검색 준수 검사를 실행 합니다.
- 감사 및 준수 세부 정보를 다시 Azure Policy에 보고 합니다.

### <a name="installing-the-add-on"></a>추가 기능 설치

필수 구성 요소가 완료 되 면 Azure Policy 추가 기능을 설치할 수 있습니다. AKS 엔진을 만들거나 업데이트 하는 동안 이나 기존 클러스터에서 독립 된 작업을 수행 하는 동안 설치를 수행할 수 있습니다.

- 만들기 또는 업데이트 주기 중 설치

  새 자체 관리 클러스터를 만드는 동안 또는 기존 클러스터에 대 한 업데이트로 Azure Policy 추가 기능을 사용 하도록 설정 하려면 AKS 엔진에 대 한 **addons** 속성 클러스터 정의를 포함 합니다.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  에 대 한 자세한 내용은 외부 가이드 [AKS 엔진 클러스터 정의](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)를 참조 하세요.

- 투구 차트를 사용 하 여 기존 클러스터에 설치

  다음 단계를 사용 하 여 클러스터를 준비 하 고 추가 기능을 설치 합니다.

  1. 게이트 키퍼 _시스템_ 네임 스페이스에 게이트 키퍼를 설치 합니다.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. _Kube_에 _제어 평면_ 레이블을 추가 합니다. 이 레이블은 _kube 시스템_ pod 및 서비스를 게이트 키퍼 및 Azure Policy 추가 기능에 대 한 감사를 제외 합니다.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Kubernetes 데이터 (네임 스페이스, Pod, 수신, 서비스)를 OPA와 동기화 합니다.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     자세한 내용은 [Opa-복제 데이터](https://github.com/open-policy-agent/gatekeeper#replicating-data)를 참조 하세요.

  1. 투구에 Azure Policy 리포지토리를 추가 합니다.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     자세한 내용은 [투구 차트-빠른 시작 가이드](https://helm.sh/docs/using_helm/#quickstart-guide)를 참조 하세요.

  1. 투구 차트를 사용 하 여 추가 기능을 설치 합니다. 를 `<subscriptionId>` 구독 ID로 바꾸고, `<aks engine cluster resource group>` 을 AKS 엔진 자체 관리 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     추가 기능 투구 차트가 설치 하는 내용에 대 한 자세한 내용은 GitHub의 [Azure Policy 추가 기능 투구 차트 정의](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) 를 참조 하세요.

     > [!NOTE]
     > Azure Policy 추가 기능과 리소스 그룹 id 간의 관계로 인해 Azure Policy는 각 리소스 그룹에 대해 하나의 AKS 엔진 클러스터만 지원 합니다.

추가 기능이 성공적으로 설치 되었는지 확인 하 고 _azure 정책_ pod가 실행 중인지 확인 하려면 다음 명령을 실행 합니다.

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>유효성 검사 및 보고 빈도

추가 기능에서는를 사용 하 여 5 분 마다 정책 할당의 변경 내용에 대 한 Azure Policy를 확인 합니다. 이 새로 고침 주기 동안 추가 기능에서 변경 내용을 확인 합니다. 이러한 변경 내용은 제약 조건 템플릿 및 제약 조건에 대 한 생성, 업데이트 또는 삭제를 트리거합니다.

> [!NOTE]
> _클러스터 관리자_ 는 제약 조건 템플릿과 제약 조건을 변경할 수 있는 권한을 가질 수 있지만 Azure Policy에서 만든 제약 조건 템플릿이나 제약 조건을 변경 하는 것은 권장 되지 않거나 지원 되지 않습니다. 새로 고침 주기 중에는 수동으로 변경한 내용이 모두 손실 됩니다.

5 분 마다 추가 기능에서 클러스터의 전체 검색을 호출 합니다. 클러스터에 대 한 변경의 게이트 키퍼를 통해 전체 검색 및 실시간 평가에 대 한 세부 정보를 수집한 후 추가 기능을 통해 결과를 다시 Azure Policy에 보고 하 여 Azure Policy 할당과 같은 [규정 준수 정보](../how-to/get-compliance-data.md) 에 포함 합니다. 활성 정책 할당의 결과만 감사 주기 중에 반환 됩니다. 실패 한 제약 조건의 status 필드에 나열 된 위반으로 감사 결과가 표시 될 수도 있습니다.

## <a name="policy-language"></a>정책 언어

AKS 엔진을 관리 하기 위한 Azure Policy 언어 구조는 기존 정책의 구조를 따릅니다. 효과 _EnforceOPAConstraint_ 는 AKS 엔진 클러스터를 관리 하는 데 사용 되며 [Opa 제약 조건 프레임 워크](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 및 게이트 키퍼 v3 작업과 관련 된 _세부_ 정보 속성을 사용 합니다. 자세한 내용과 예제는 [EnforceOPAConstraint](effects.md#enforceopaconstraint) 효과를 참조 하세요.

_ConstraintTemplate_ 및 details의 일부로 정책 정의의 _제약 조건_ 속성은 이러한 [customresourcedefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD)의 uri를 추가 기능으로 전달 Azure Policy. Rego는 Kubernetes 클러스터에 대 한 요청의 유효성을 검사 하기 위해 OPA 및 게이트 키퍼가 지 원하는 언어입니다. Kubernetes 관리에 대 한 기존 표준을 지원 하 Azure Policy를 사용 하면 기존 규칙을 다시 사용 하 여 통합 된 클라우드 규정 준수 보고 환경을 위한 Azure Policy와 연결할 수 있습니다. 자세한 내용은 [Rego 란?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)을 참조 하세요.

## <a name="built-in-policies"></a>기본 제공 정책

Azure Portal를 사용 하 여 AKS 엔진 클러스터를 관리 하기 위한 기본 제공 정책을 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Policy 서비스를 시작 합니다. 왼쪽 창에서 **모든 서비스** 를 선택 하 고 **정책**을 검색 한 다음 선택 합니다.

1. Azure Policy 페이지의 왼쪽 창에서 **정의**를 선택 합니다.

1. 범주 드롭다운 목록 상자에서 **모두 선택** 을 사용 하 여 필터를 지운 다음 **Kubernetes**를 선택 합니다.

1. 정책 정의를 선택한 다음 **할당** 단추를 선택 합니다.

> [!NOTE]
> AKS 엔진 정의에 대 한 Azure Policy를 할당 하는 경우 **범위** 는 AKS 엔진 클러스터의 리소스 그룹 이어야 합니다.

또는 [정책 할당-포털](../assign-policy-portal.md) 빠른 시작을 사용 하 여 AKS 엔진 정책을 찾고 할당 합니다. 샘플 ' vm 감사 ' 대신 AKS 엔진 정책 정의를 검색 합니다.

> [!IMPORTANT]
> Category **Kubernetes** 의 기본 제공 정책은 AKS Engine 에서만 사용 됩니다.

## <a name="logging"></a>로깅

### <a name="azure-policy-add-on-logs"></a>추가 기능 로그 Azure Policy

Kubernetes controller/container Azure Policy 추가 기능이 AKS 엔진 클러스터에 로그를 보관 합니다.

Azure Policy 추가 기능 로그를 보려면 다음을 사용 `kubectl`합니다.

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>게이트 키퍼 로그

게이트 키퍼 pod- _controller-0_은 일반적으로 `gatekeeper-system` 또는 `kube-system` 네임 스페이스에 있지만 배포 방법에 따라 다른 네임 스페이스에 있을 수 있습니다.

게이트 키퍼 로그를 보려면 다음을 `kubectl`사용 합니다.

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

자세한 내용은 OPA 설명서의 [디버깅 게이트 키퍼](https://github.com/open-policy-agent/gatekeeper#debugging) 를 참조 하세요.

## <a name="remove-the-add-on"></a>추가 기능 제거

AKS 엔진 클러스터에서 Azure Policy 추가 기능 및 게이트 키퍼를 제거 하려면 추가 기능이 설치 된 방법에 맞는 메서드를 사용 합니다.

- AKS 엔진에 대 한 클러스터 정의에서 **addons** 속성을 설정 하 여 설치 된 경우:

  _Azure 정책_ 에 대 한 **addons** 속성을 false로 변경한 후 클러스터 정의를 AKS Engine에 다시 배포 합니다.


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 투구 차트와 함께 설치 하는 경우:

  1. 이전 제약 조건 제거

     현재 제거 메커니즘은 게이트 키퍼 시스템만 제거 하 고 사용자가 만든 _ConstraintTemplate_, _제약 조건_또는 _구성_ 리소스를 제거 하지 않으며 함께 제공 된 _crds_를 제거 하지 않습니다.

     게이트 키퍼를 실행 하는 경우 다음을 수행 하 여 원치 않는 제약 조건을 제거할 수 있습니다.

     - 제약 조건 리소스의 모든 인스턴스를 삭제 하는 중
     - _ConstraintTemplate_ 리소스를 삭제 합니다. 그러면 _CRD_ 가 자동으로 정리 됩니다.
     - _구성_ 리소스를 삭제 하면 동기화 된 리소스의 종료 자가 제거 됨

  1. 추가 기능 Azure Policy 제거
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 게이트 키퍼 제거
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy 추가 기능으로 수집 된 진단 데이터

Kubernetes 용 Azure Policy 추가 기능은 제한 된 클러스터 진단 데이터를 수집 합니다. 이 진단 데이터는 소프트웨어 및 성능과 관련 된 중요 한 기술 데이터입니다. 다음과 같은 방법으로 사용 됩니다.

- Azure Policy 추가 기능을 최신 상태로 유지
- 안전 하 고 신뢰할 수 있으며 성능이 뛰어난 추가 기능 Azure Policy 유지
- 추가 기능 사용에 대 한 집계 분석을 통해 Azure Policy 추가 기능 향상

추가 기능으로 수집 되는 정보는 개인 데이터가 아닙니다. 현재 수집 되는 세부 정보는 다음과 같습니다.

- 추가 기능 에이전트 버전 Azure Policy
- 클러스터 유형
- 클러스터 영역
- 클러스터 리소스 그룹
- 클러스터 리소스 ID
- 클러스터 구독 ID
- 클러스터 OS (예: Linux)
- 클러스터 도시 (예: 시애틀)
- 클러스터 상태 또는도 (예: 워싱턴)
- 클러스터 국가 또는 지역 (예: 미국)
- 정책 평가에 에이전트를 설치 하는 동안 Azure Policy 추가 기능에서 발생 하는 예외/오류
- Azure Policy 추가 기능으로 설치 되지 않은 게이트 키퍼 정책 수

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [정책 정의 구조](definition-structure.md)를 검토 합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹을 사용](../../management-groups/overview.md)하 여 리소스를 구성 하는 관리 그룹을 검토 합니다.